#普通权限

许多权限被指定为PROTECTION_NORMAL，这表示在让应用具有这些权限的情况下，用户的隐私或安全性没有很大的风险。例如，用户将合理地想知道应用程序是否可以读取其联系信息，因此用户必须明确授予此权限。相比之下，允许应用程序振动设备的风险并不大，所以将权限指定为正常。

如果应用程序在其清单中声明它需要正常的权限，系统会在安装时自动向应用授予该权限。系统不会提示用户授予普通权限，用户不能撤销这些权限。

从API级别23(6.0)起，以下权限分为PROTECTION_NORMAL：`android.android.Manifest.permission`

```txt
ACCESS_LOCATION_EXTRA_COMMANDS
ACCESS_NETWORK_STATE
ACCESS_NOTIFICATION_POLICY
ACCESS_WIFI_STATE
BLUETOOTH
BLUETOOTH_ADMIN
BROADCAST_STICKY
CHANGE_NETWORK_STATE
CHANGE_WIFI_MULTICAST_STATE
CHANGE_WIFI_STATE
DISABLE_KEYGUARD
EXPAND_STATUS_BAR
GET_PACKAGE_SIZE
INSTALL_SHORTCUT
INTERNET
KILL_BACKGROUND_PROCESSES
MODIFY_AUDIO_SETTINGS
NFC
READ_SYNC_SETTINGS
READ_SYNC_STATS
RECEIVE_BOOT_COMPLETED
REORDER_TASKS
REQUEST_IGNORE_BATTERY_OPTIMIZATIONS
REQUEST_INSTALL_PACKAGES
SET_ALARM
SET_TIME_ZONE
SET_WALLPAPER
SET_WALLPAPER_HINTS
TRANSMIT_IR
UNINSTALL_SHORTCUT
USE_FINGERPRINT
VIBRATE
WAKE_LOCK
WRITE_SYNC_SETTINGS
```

reference: https://developer.android.com/guide/topics/permissions/normal-permissions.html