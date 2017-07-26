#定义权限

本文档描述了应用开发人员如何使用Android提供的安全功能来定义自己的权限。通过定义自定义权限，应用程序可以与其他应用程序共享其资源和功能。有关权限的更多信息，请参阅[Android安全性概述](ying-yong-an-quan.md)。

#背景
Android是一个特权分离的操作系统，每个应用程序都运行着不同的系统标识（Linux用户标识和组ID）。系统的一部分也被分成不同的身份。因此，Linux将应用程序从系统中隔离开来。

应用可以通过定义其他应用可以请求的权限来将其功能公开到其他应用。他们还可以定义自动向任何其他使用相同证书签名的应用程序提供的权限。

#应用程式签名
所有APK（.apk文件）必须使用其开发人员持有的私钥的证书进行签名。此证书标识该应用程序的作者。该证书也并不需要由证书颁发机构签署; Android应用程序使用自签名证书是绝对允许的，典型的是。Android中的证书的目的是区分应用程序作者。这允许系统授予或拒绝应用程序访问签名级权限，并允许或拒绝将应用程序的请求与其他应用程序相同的Linux身份。

#用户ID和文件访问
在安装时，Android会为每个软件包提供不同的Linux用户ID。在该设备的包装寿命期间，身份保持不变。在不同的设备上，相同的包可能具有不同的UID; 重要的是每个包在给定设备上具有不同的UID。

由于安全执行在进程级别发生，所以任何两个程序包的代码通常不能在同一进程中运行，因为它们需要作为不同的Linux用户运行。您可以使用每个包的 标签中的sharedUserId属性 来分配相同的用户ID。通过这样做，为了安全起见，两个包被视为相同的应用程序，具有相同的用户ID和文件权限。请注意，为了保留安全性，只有两个具有相同签名（并请求相同的sharedUserId）的应用程序将被赋予相同的用户ID。AndroidManifest.xmlmanifest

应用程序存储的任何数据将被分配该应用程序的用户标识，而其他软件包通常不可访问。


#定义和执行权限
要强制执行您自己的权限，您必须先AndroidManifest.xml使用一个或多个<permission> 元素声明它们 。

例如，一个想要控制谁可以开始其中一个活动的应用程序可以声明此操作的权限如下：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myapp" >
    <permission android:name="com.example.myapp.permission.DEADLY_ACTIVITY"
        android:label="@string/permlab_deadlyActivity"
        android:description="@string/permdesc_deadlyActivity"
        android:permissionGroup="android.permission-group.COST_MONEY"
        android:protectionLevel="dangerous" />
    ...
</manifest>
```

>注意：系统不允许多个包声明具有相同名称的权限，除非所有包都使用相同的证书签名。如果软件包声明权限，则系统不允许用户安装具有相同权限名称的其他软件包，除非这些软件包使用与第一个软件包相同的证书进行签名。为了避免命名冲突，我们建议对自定义权限使用反向域风格的命名com.example.myapp.ENGAGE_HYPERSPACE。

该protectionLevel属性是必需的，告诉系统如何向用户通知需要许可的应用程序，或者被允许持有该许可的用户，如链接文档中所述。

的android:permissionGroup 属性是可选的，并且仅用于帮助系统显示权限给用户。在大多数情况下，您需要将其设置为标准系统组（列出android.Manifest.permission_group），尽管您可以自己定义组。优选使用现有组，因为这简化了向用户显示的许可UI。

您需要为权限提供标签和描述。这些是用户在查看权限列表（android:label）或单个权限（android:description）的详细信息 时可以看到的字符串资源。标签应该很短 描述许可正在保护的关键功能部分的几个字。描述应该是一些句子，描述持有人允许的权限。我们的惯例是一个两句话的描述：第一句描述权限，第二句话告诉用户如果某个应用被授予权限，可能会出错的事情类型。

以下是CALL_PHONE权限的标签和说明示例：

```xml
<string name="permlab_callPhone">directly call phone numbers</string>
<string name="permdesc_callPhone">Allows the app to call
    phone numbers without your intervention. Malicious apps may
    cause unexpected calls on your phone bill. Note that this does not
    allow the app to call emergency numbers.</string>
```

#自定义权限建议
应用程序可以通过定义从其他应用定义他们自己的定制权限，并请求定制权限<uses-permission>元素。但是，您应该仔细评估应用程序是否需要这样做。

如果您正在设计一套将功能相互揭露的应用程序，请尝试设计应用程序，以便每个权限仅定义一次。如果应用程序未全部使用相同的证书签名，则必须执行此操作。即使应用程序都使用相同的证书进行签名，所以最佳做法是仅定义一次权限。
如果功能仅适用于与提供应用程序签名相同的签名的应用程序，则可能会通过使用签名检查来避免定义自定义权限。当您的一个应用程序发出其他应用程序的请求时，第二个应用程序可以在遵守该请求之前验证这两个应用程序是否使用相同的证书进行签名。
如果您正在开发一套仅在自己的设备上运行的应用程序，那么您应该开发并安装一个管理套件中所有应用程序的权限的软件包。此软件包不需要提供任何服务本身。它只是声明所有权限，并且套件中的其他应用程序使用该<uses-permission> 元素请求这些权限。

#在AndroidManifest.xml中强制执行权限

您可以应用高级权限，限制对系统或应用程序的整个组件的访问 AndroidManifest.xml。为此，android:permission在所需组件上包含一个属性，命名控制对其的访问权限。

Activity权限（应用于 <activity>标记）限制谁可以启动关联的活动。在权限检查 Context.startActivity()和 Activity.startActivityForResult(); 如果调用者没有所需的权限，SecurityException则从呼叫中抛出。

Service权限（应用于 <service>标记）限制谁可以启动或绑定到关联的服务。在权限检查 Context.startService()， Context.stopService()和 Context.bindService(); 如果调用者没有所需的权限，SecurityException则从呼叫中抛出。

BroadcastReceiver权限（应用于 <receiver>标签）限制谁可以向相关联的接收方发送广播。返回后 检查许可Context.sendBroadcast()，因为系统尝试将提交的广播传送给给定的接收者。因此，权限失败不会导致将异常抛回给调用者; 它只是没有实现意图。以相同的方式，可以提供许可 Context.registerReceiver() 以控制谁可以广播到编程注册的接收者。另一方面，当调用Context.sendBroadcast() 限制哪个BroadcastReceiver对象允许接收广播时，可以提供许可 （见下文）。

ContentProvider权限（应用于 <provider>标记）限制谁可以访问a中的数据ContentProvider。（内容提供商有一个重要的额外的安全设施称为 URI权限，稍后将会介绍）与其他组件不同，您可以设置两个独立的权限属性： android:readPermission限制谁可以从提供程序读取，并 android:writePermission限制谁可以写入它。请注意，如果提供程序受到读取和写入权限的保护，则只保留写入权限并不意味着您可以从提供程序读取。当您首次检索提供程序（如果您没有任何权限，将抛出SecurityException）以及对提供程序执行操作时，将检查权限。使用 ContentResolver.query()需要持有读权限; ContentResolver.insert()使用， ContentResolver.update()， ContentResolver.delete() 需要写权限。在所有这些情况下，不保留所需权限导致 SecurityException从呼叫中抛出。

#发送广播时强制执行权限

除了执行谁可以发送Intent到注册BroadcastReceiver（如上所述）的权限之外，您还可以在发送广播时指定所需的权限。通过Context.sendBroadcast()使用权限字符串调用，您需要接收方的应用程序必须拥有该权限才能接收广播。

请注意，接收方和广播机构都可能需要许可。当这种情况发生时，两个权限检查都必须传递给要传递到关联目标的意图。

有关详细信息，请参阅使用权限限制广播。

#其他许可执行

任何调用服务都可以执行任意细粒度的权限。这是通过该Context.checkCallingPermission() 方法完成的。使用所需的权限字符串调用，它将返回一个整数，表示该权限是否已被授予当前调用进程。请注意，这只能在您执行来自另一个进程的呼叫时使用，通常通过从服务发布的IDL接口或以其他方式给予另一个进程。

还有一些其他有用的方法来检查权限。如果您有另一个进程的pid，可以使用Context方法Context.checkPermission(String, int, int) 来检查该pid的权限。如果您有其他应用程序的软件包名称，则可以使用直接PackageManager方法PackageManager.checkPermission(String, String) 来确定该特定软件包是否已被授予特定权限。

#URI权限

到目前为止描述的标准权限系统与内容提供商一起使用时通常是不够的。内容提供商可能希望通过读取和写入权限来保护自身，而其直接客户端也需要将特定的URI传递给其他应用程序以供其操作。一个典型的例子是邮件应用程序中的附件。访问邮件应受到权限的保护，因为这是敏感的用户数据。但是，如果向图像查看器提供图像附件的URI，则该图像查看器将无权打开附件，因为它没有理由持有访问所有电子邮件的权限。

该问题的解决方案是per-URI权限：当启动活动或将结果返回到活动时，调用者可以设置 Intent.FLAG_GRANT_READ_URI_PERMISSION和/或 Intent.FLAG_GRANT_WRITE_URI_PERMISSION。这允许接收活动权限访问Intent中的特定数据URI，而不管其是否具有访问与Intent相对应的内容提供商中的数据的任何权限。

这种机制允许用户交互（打开附件，从列表中选择联系人等）的常用功能样式模型驱动了对细粒度权限的特别授予。这可以是将应用程序所需的权限降低到与其行为直接相关的权限的关键工具。

然而，授予细粒度的URI权限需要与持有这些URI的内容提供商进行一些合作。强烈建议内容提供商实现此设施，并声明通过android:grantUriPermissions属性或 <grant-uri-permissions>标记来支持它 。

更多信息可在发现 Context.grantUriPermission()， Context.revokeUriPermission()和 Context.checkUriPermission() 方法。

------

继续阅读：|您可能也有兴趣：
---|---
< uses-permission>|Android安全性概述
用于声明应用程序所需系统权限的清单标签的API参考。|详细讨论Android平台的安全模式。

reference: https://developer.android.com/guide/topics/permissions/defining.html