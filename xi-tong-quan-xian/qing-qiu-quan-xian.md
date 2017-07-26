#请求权限

Android是一个特权分离的操作系统，每个应用程序都运行着不同的系统标识（Linux用户标识和组ID）。系统的一部分也被分成不同的身份。因此，Linux将应用程序从系统中隔离开来。

因为每个Android应用程序都在一个进程沙箱中运行，所以应用程序必须明确请求访问沙箱外的资源和数据。他们通过声明其基本沙箱未提供的附加功能所需的权限来请求此访问权限。根据区域的敏感程度，系统可以自动授予权限，或者可能会提示用户批准或拒绝该请求。

此页面描述应用程序如何请求标准系统权限。以下文档“ 定义权限 ”介绍了应用程序如何定义自己的权限，以便他们可以安全地与其他应用程序共享其数据和功能。

另请参阅材料[样式指南的权限](https://material.io/guidelines/patterns/permissions.html)。

##使用权限
默认情况下，基本的Android应用程序没有与此关联的权限，这意味着它不会对任何会对用户体验或设备上的任何数据造成不利影响。要使用设备的受保护功能，您必须<uses-permission> 在应用程序清单中包含一个或多个 标签。

例如，需要监控传入SMS消息的应用程序将指定：

```xml
<manifest  xmlns:android="http://schemas.android.com/apk/res/android" package="com.android.app.myapp" >
    <uses-permission android:name="android.permission.RECEIVE_SMS" />
    ...
</manifest>
```

>权限级别
有关权限的不同保护级别的更多信息，请参阅 正常和危险权限。

* 如果您的应用程序在其清单中列出了正常的权限（即，不会对用户的隐私或设备的操作造成很大风险的权限），系统将自动授予这些权限。如果您的应用列出其清单中的危险权限（即可能影响用户隐私或设备正常运行的权限），则系统会要求用户明确授予这些权限。Android提出请求的方式取决于系统版本以及您的应用程序定位的系统版本：

* 如果设备正在运行Android 6.0（API级别23）或更高版本， 并且应用程序targetSdkVersion 是23或更高版本，则应用程序将在运行时请求用户的权限。用户可以随时撤销权限，因此应用程序需要检查每次运行时是否具有权限。有关在应用程序中请求权限的更多信息，请参阅“ 使用系统权限”培训指南。
如果设备正在运行Android 5.1（API级别22）或更低版本，或者 该应用程序targetSdkVersion 是22或更低版本，则系统在用户安装应用程序时要求用户授予权限。如果您向应用程序的更新版本添加新权限，系统会在用户更新应用程序时要求用户授予该权限。用户安装应用程序后，他们可以撤销权限的唯一方法是卸载该应用程序。

通常，权限失败将导致SecurityException被抛回应用程序。但是，这并不能保证在任何地方都能发生。例如，在sendBroadcast(Intent)方法调用返回后，该方法将检查数据正在传递到每个接收方的权限，因此如果存在权限失败，则不会收到异常。然而，在几乎所有情况下，将向系统日志打印权限失败。

Android系统提供的权限可以在这里找到Manifest.permission。任何应用程序也可以定义和执行自己的权限，因此这不是所有可能权限的全面列表。

在程序运行期间，可能会在许多地方执行特定许可：

* 在系统调用时，防止应用程序执行某些功能。
* 开始活动时，要防止应用启动其他应用的活动。
* 发送和接收广播，以控制哪些人可以接收您的广播或谁可以向您发送广播。
* 访问和操作内容提供商时。
* 绑定或启动服务。

##自动权限调整
随着时间的推移，新的限制可能会添加到平台中，以便为了使用某些API，您的应用程序必须请求以前不需要的权限。由于现有的应用程序可以免费获得对这些API的访问权限，Android可能会将新的许可请求应用于应用程序的清单，以避免在新平台版本上破坏该应用程序。Android决定是否应用程序可能需要基于为该targetSdkVersion 属性提供的值的权限。如果该值低于添加权限的版本，则Android会添加权限。

例如，WRITE_EXTERNAL_STORAGE在API级别4中添加了权限以限制对共享存储空间的访问。如果您targetSdkVersion 的3级或更低版本，则会在较新版本的Android上将此权限添加到您的应用。

> 注意：如果权限自动添加到您的应用程序，您的Google Play上的应用程序列表会列出这些附加权限，即使您的应用可能实际上不需要它们。

为了避免这种情况并删除您不需要的默认权限，请始终将其更新targetSdkVersion 为尽可能高。您可以在Build.VERSION_CODES文档中看到每个版本添加了哪些权限 。

##查看应用的权限
您可以使用“设置”应用程序和shell命令查看当前系统中定义的权限adb shell pm list permissions。要使用“设置”应用，请转到“设置” >“ 应用”。选择一个应用程序并向下滚动以查看应用程序使用的权限。对于开发人员，adb'-s'选项以与用户将如何看到它们相似的形式显示权限：

```shell
$ adb shell pm list permissions -s
All Permissions:

网络通信：查看Wi-Fi状态，创建蓝牙连接，完全
上网，查看网络状态

您的位置：访问额外的位置提供商命令，精细（GPS）位置，
模拟位置来源测试，粗糙（基于网络）的位置

服务，花费你的钱：发送短信，直接拨打电话号码
...
```

##正常和危险的权限
系统权限分为几个保护级别。要知道的两个最重要的保护级别是正常和 危险的权限：

正常的权限涵盖了您的应用程序需要访问应用程序沙箱外的数据或资源的区域，但用户隐私风险甚至其他应用程序的运行风险很小。例如，设置时区的权限是一个正常的权限。如果一个应用程序声明它需要一个正常的权限，系统将自动授予该应用的权限。有关当前正常权限的完整列表，请参阅常规权限。
危险的权限涵盖了应用程序想要涉及用户私人信息的数据或资源的区域，或者可能影响用户存储的数据或其他应用程序的操作。例如，读取用户联系人的能力是一个危险的许可。如果一个应用程序声明它需要一个危险的权限，用户必须明确地授予该应用的权限。

##特权-特殊权限
有几个权限不像正常和危险的权限。SYSTEM_ALERT_WINDOW并且WRITE_SETTINGS特别敏感，所以大多数应用程序不应该使用它们。如果应用程序需要其中一个权限，则必须在清单中声明权限，并发送请求用户授权的意图。系统通过向用户显示详细的管理屏幕来响应意图。

有关如何请求这些权限的详细信息，请参阅SYSTEM_ALERT_WINDOW和 WRITE_SETTINGS引用条目。

##权限组
所有危险的Android系统权限属于权限组。如果设备正在运行Android 6.0（API级别23）且应用程序的数量targetSdkVersion为23或更高版本，则当您的应用程序请求危险的权限时，会适用以下系统行为：

如果应用程序请求其清单中列出的危险许可，并且应用程序当前没有权限组中的任何权限，系统将向用户显示描述应用程序要访问的权限组的对话框。对话框不会描述该组中的特定权限。例如，如果应用程序请求 READ_CONTACTS权限，系统对话框只会说应用程序需要访问设备的联系人。如果用户获得批准，系统会向应用程序提供其请求的许可。
如果应用程序请求其清单中列出的危险许可，并且应用程序在同一权限组中已经具有另一个危险的权限，则系统将立即授予权限，而无需与用户进行任何交互。>

>例如，如果应用程序先前已经请求并被 READ_CONTACTS许可，然后它请求WRITE_CONTACTS，系统立即授予该权限。
任何权限都可以属于权限组，包括您的应用程序定义的常规权限和权限。但是，如果许可是危险的，权限组只会影响用户体验。您可以忽略权限组以获取正常权限。

如果设备正在运行Android 5.1（API级别22）或更低版本，或者该应用程序 targetSdkVersion为22或更低版本，则系统会要求用户在安装时授予权限。系统再次告诉用户应用程序需要哪些权限组，而不是单独的权限。

权限组	|权限
---|---
CALENDAR|	READ_CALENDAR<br/> WRITE_CALENDAR
CAMERA	|CAMERA
CONTACTS	|READ_CONTACTS<br/> WRITE_CONTACTS<br/> GET_ACCOUNTS
LOCATION	|ACCESS_FINE_LOCATION<br/> ACCESS_COARSE_LOCATION
MICROPHONE	|RECORD_AUDIO
PHONE	|READ_PHONE_STATE<br/> CALL_PHONE<br/> READ_CALL_LOG<br/> WRITE_CALL_LOG<br/> ADD_VOICEMAIL<br/> USE_SIP<br/> PROCESS_OUTGOING_CALLS
SENSORS	|BODY_SENSORS
SMS	|SEND_SMS<br/> RECEIVE_SMS<br/> READ_SMS<br/> RECEIVE_WAP_PUSH<br/> RECEIVE_MMS
STORAGE	|READ_EXTERNAL_STORAGE<br/> WRITE_EXTERNAL_STORAGE

------
继续阅读：|您可能也有兴趣：
---|---
[允许功能要求的权限](https://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions)<br/>&#160;&#160;有关如何请求某些权限的信息会将您的应用程序隐含地限制在包含相应硬件或软件功能的设备上。|[设备兼容性](https://developer.android.com/guide/practices/compatibility.html)<br/>&#160;&#160;有关Android的信息适用于不同类型的设备，并介绍如何优化每个设备的应用程序，或将应用程序的可用性限制在不同的设备上。
[< uses-permission>](https://developer.android.com/guide/topics/manifest/uses-permission-element.html)<br/>&#160;&#160;用于声明应用程序所需系统权限的清单标签的API参考。|[Android安全性概述](http://source.android.com/devices/tech/security/index.html)<br/>&#160;&#160;详细讨论Android平台的安全模式。
[Manifest.permission](https://developer.android.com/reference/android/Manifest.permission.html)<br/>&#160;&#160;所有系统权限的API参考。|[定义权限](https://developer.android.com/guide/topics/permissions/defining.html)<br/>&#160;&#160;描述应用程序如何定义自己的自定义权限，使其能够与其他应用程序共享其信息和功能。

reference: https://developer.android.com/guide/topics/permissions/requesting.html