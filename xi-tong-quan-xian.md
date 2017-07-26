#系统的权限

为了维护系统和用户的安全性，Android要求应用程序在应用程序可以使用某些系统数据和功能之前请求许可。根据区域的敏感程度，系统可以自动授予权限，或者可以要求用户批准该请求。

>注意：这些权限是Android权限; 他们允许访问设备功能。如果您正在寻找Google API权限，例如用于与Google云端硬盘交互的权限，请参阅Google Play服务权限。

本节介绍应用程序如何请求访问系统权限。它还描述了应用程序如何定义自己的权限，因此应用程序可以安全地与其他应用程序共享其数据和功能。

##要求许可

API指南 | 也可以看看
---|---
[请求权限<br/>&#160;&#160;描述应用程序如何请求标准的Android权限，该权限控制对系统范围的资源和信息的访问。](/xi-tong-quan-xian/qing-qiu-quan-xian.md)|[使用系统权限培训<br/>&#160;&#160;显示您如何声明和请求您的应用程序的权限。](https://developer.android.com/training/permissions/index.html)
[定义权限<br/>&#160;&#160;描述应用程序如何定义自己的自定义权限，使其能够与其他应用程序共享其信息和功能。](/xi-tong-quan-xian/ding-yi-quan-xian.md)|[权限最佳实践<br/>&#160;&#160;提供有关如何以及何时使用系统权限的提示，以及如何获取等效的功能而不请求权限。](https://developer.android.com/training/articles/user-data-permissions.html)

reference:  https://developer.android.com/guide/topics/permissions/index.html