#Requesting Permissions at Run Time
运行时请求权限

从Android 6.0（API级别23）开始，用户在应用运行时向用户授予权限，而不是安装应用。该方法简化了应用程序安装过程，因为用户在安装或更新应用程序时不需要授予权限。它还使用户能够更好地控制应用程序的功能; 例如，用户可以选择给相机应用程序访问相机而不是设备位置。用户可以随时撤销权限，方法是转到应用的“设置”屏幕。

###系统权限分为两类，普通和危险：

* **普通权限**不会直接危及用户的隐私。如果您的应用程序在其清单中列出了正常的权限，系统将自动授予权限。
* **危险权限**可以让应用程序访问用户的机密数据。如果您的应用程序在其清单中列出了正常的权限，系统将自动授予权限。如果您列出危险的许可，用户必须明确地批准您的应用程序。
有关更多信息，请参阅正常和危险权限。

###在所有版本的Android上，您的应用程序需要声明其应用程序清单中所需的正常和危险权限，如“ 声明权限”中所述。但是，该声明的效果因系统版本和应用程序的目标SDK级别而异：

* 如果设备正在运行Android 5.1或更低版本，或者您的应用的目标SDK为22或更低版本：如果您在清单中列出了危险的权限，则用户必须在安装应用时授予权限; 如果他们没有授予权限，系统根本就不会安装该应用程序。
* 如果设备运行的是Android 6.0或更高版本，并且您的应用的目标SDK为23或更高版本：该应用必须列出清单中的权限， 并且它必须在应用运行时请求每个需要的危险权限。用户可以授予或拒绝每个权限，即使用户拒绝了权限请求，应用程序也可以继续以有限的功能运行。

>**注意：从Android 6.0（API级别23）开始，用户可以随时从任何应用程序撤销权限，即使应用程序的API级别较低。您应该测试您的应用程序，以便在缺少所需权限的情况下验证其是否正常运行，而不管应用程序的API级别如何。**

本课介绍如何使用Android 支持库来检查和请求权限。Android框架提供与Android 6.0（API级别23）类似的方法。然而，使用[**支持库更简单**](https://developer.android.com/tools/support-library/index.html)，因为您的应用程序不需要在调用方法之前检查其运行的Android版本。

##检查权限
如果您的应用程序需要危险的权限，则必须检查每次执行需要该权限的操作时是否具有该权限。用户永远可以自由撤销权限，所以即使应用程序昨天使用相机，也不能认为今天仍然有这个权限。

要检查您是否有权限，请调用该**ContextCompat.checkSelfPermission()**方法。例如，此代码段显示如何检查活动是否有权写入日历：

```java
//假设thisActivity是当前活动
int permissionCheck = ContextCompat.checkSelfPermission(thisActivity,
        Manifest.permission.WRITE_CALENDAR);
```
        
如果应用程序有权限，该方法返回**PackageManager.PERMISSION_GRANTED**，应用程序可以继续操作。如果应用程序没有权限，该方法返回**PERMISSION_DENIED**，并且应用程序必须明确地要求用户许可。

##请求权限
如果您的应用需要应用清单中列出的危险许可，则必须要求用户授予权限。Android提供了几种可以用来请求权限的方法。调用这些方法会出现一个标准的Android对话框，您无法自定义。

##解释为什么应用程序需要权限

图1.系统对话框提示用户授予或拒绝许可。

在某些情况下，您可能希望帮助用户了解您的应用程序需要许可的原因。例如，如果用户启动摄影应用程序，用户可能不会惊讶于应用程序要求使用相机的权限，但用户可能不了解应用程序要访问用户位置或联系人的原因。在您请求许可之前，您应该考虑向用户提供说明。请记住，您不想用说明来淹没用户; 如果您提供太多说明，用户可能会发现该应用程序令人沮丧，并将其删除。

您可能使用的一种方法是仅在用户已经拒绝该权限请求时提供说明。如果用户不断尝试使用需要权限的功能，但是不断关闭权限请求，这可能表明用户不明白为什么应用程序需要提供该功能的权限。在这样的情况下，显示一个解释可能是一个好主意。

为了帮助找到用户需要解释的情况，Android提供了一个实用方法**shouldShowRequestPermissionRationale()**。true如果应用程序以前请求了此权限，并且用户拒绝了该请求，则此方法返回。

>注意：如果用户拒绝了过去的权限请求，并在权限请求系统对话框中选择了不再询问选项，则此方法返回false。false如果设备策略禁止应用拥有该权限，该方法也将返回。

##请求您需要的权限
如果您的应用程序尚未获得所需的权限，则应用程序必须调用其中一种**requestPermissions()**方法来请求相应的权限。您的应用程序会传递其所需的权限，以及您指定的整数请求代码以标识此权限请求。该方法异步运行：它立即返回，用户响应对话框后，系统会使用结果调用应用程序的回调方法，传递应用程序传递的相同请求代码 **requestPermissions()**。

以下代码检查应用程序是否有权限读取用户的联系人，并在必要时请求许可：

```java
// Here, thisActivity is the current activity
if (ContextCompat.checkSelfPermission(thisActivity,
                Manifest.permission.READ_CONTACTS)
        != PackageManager.PERMISSION_GRANTED) {

    // Should we show an explanation?
    if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,
            Manifest.permission.READ_CONTACTS)) {

        // Show an explanation to the user *asynchronously* -- don't block
        // this thread waiting for the user's response! After the user
        // sees the explanation, try again to request the permission.

    } else {

        // No explanation needed, we can request the permission.

        ActivityCompat.requestPermissions(thisActivity,
                new String[]{Manifest.permission.READ_CONTACTS},
                MY_PERMISSIONS_REQUEST_READ_CONTACTS);

        // MY_PERMISSIONS_REQUEST_READ_CONTACTS is an
        // app-defined int constant. The callback method gets the
        // result of the request.
    }
}
```
    
>注意：当您的应用程序调用时**requestPermissions()**，系统会向用户显示一个标准对话框。您的应用程序无法配置或更改该对话框。如果您需要向用户提供任何信息或说明，您应该在通话前进行此操作，**requestPermissions()**如解释应用程序需要权限的原因所述。

##处理权限请求响应
当您的应用程序请求权限时，系统会向用户显示一个对话框。当用户响应时，系统调用您的应用程序的**onRequestPermissionsResult()**方法，传递用户响应。您的应用程序必须重写该方法，以确定是否已授予权限。回调传递给您传递给相同的请求代码 **requestPermissions()**。例如，如果应用程序请求READ_CONTACTS访问，它可能具有以下回调方法：

```java
@Override
public void onRequestPermissionsResult(int requestCode,
        String permissions[], int[] grantResults) {
    switch (requestCode) {
        case MY_PERMISSIONS_REQUEST_READ_CONTACTS: {
            // If request is cancelled, the result arrays are empty.
            if (grantResults.length > 0
                && grantResults[0] == PackageManager.PERMISSION_GRANTED) {

                // permission was granted, yay! Do the
                // contacts-related task you need to do.

            } else {

                // permission denied, boo! Disable the
                // functionality that depends on this permission.
            }
            return;
        }

        // other 'case' lines to check for other
        // permissions this app might request
    }
}
```
    
系统显示的对话框描述了您的应用程序需要访问的权限组 ; 它没有列出具体的权限。例如，如果您请求READ_CONTACTS权限，系统对话框只是说您的应用程序需要访问设备的联系人。用户只需要为每个权限组授予一次权限。如果您的应用程序请求该组中的任何其他权限（在应用程序清单中列出），系统将自动授予它们。当您请求权限时，系统将调用您的**onRequestPermissionsResult()**回调方法并传递PERMISSION_GRANTED，方式与用户通过系统对话框显式授予您的请求相同。

>注意：即使用户已经在同一个组中已经授予了另一个权限，您的应用程序仍然需要明确请求所需的每个权限。此外，将来的Android版本中的权限分组可能会有变化。您的代码不应该依赖于特定权限是或不在同一个组中的假设。

例如，假设你同时列出READ_CONTACTS，并WRITE_CONTACTS在您的应用程序清单。如果您请求 READ_CONTACTS并且用户授予权限，然后您请求WRITE_CONTACTS，系统将立即授予您该权限，而无需与用户交互。

如果用户拒绝许可请求，您的应用程序应该采取适当的措施。例如，您的应用程序可能会显示一个对话框，说明为什么无法执行需要该权限的用户请求的操作。

当系统要求用户授予权限时，用户可以选择告诉系统不再请求该权限。在这种情况下，任何应用程序应用程序**requestPermissions()**再次请求该权限，系统将立即拒绝该请求。系统调用您的**onRequestPermissionsResult()**回调方法并传递PERMISSION_DENIED，与用户再次明确拒绝您的请求相同。这意味着当您打电话时**requestPermissions()**，您不能假定与用户发生任何直接的互动。

reference: https://developer.android.com/training/permissions/requesting.html