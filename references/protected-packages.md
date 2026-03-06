# Protected Packages — NEVER Disable or Uninstall

These packages are critical to Android system operation. Disabling them can brick the device, break telephony, or make the phone unusable.

## Core Android System

```
android
com.android.systemui
com.android.settings
com.android.phone
com.android.providers.settings
com.android.providers.contacts
com.android.providers.telephony
com.android.providers.downloads
com.android.providers.media
com.android.bluetooth
com.android.nfc
com.android.shell
com.android.se
com.android.keychain
com.android.server.telecom
com.android.networkstack.process
com.android.location.fused
com.android.inputdevices
com.android.certinstaller
com.android.packageinstaller
com.android.storagemanager
com.android.externalstorage
```

## Google Mobile Services (GMS)

```
com.google.android.gms
com.google.android.gsf
com.android.vending
com.google.android.inputmethod.latin
com.google.android.dialer
com.google.android.contacts
com.google.android.apps.messaging
com.google.android.webview
com.google.android.permissioncontroller
com.google.android.ext.services
com.google.android.networkstack
com.google.android.networkstack.tethering
com.google.android.providers.media.module
com.google.android.setupwizard
```

## Telephony / Carrier Framework

```
com.android.phone
com.android.stk
com.android.mms.service
com.android.ons
com.android.carrierconfig
com.android.carrierdefaultapp
com.android.imsserviceentitlement
org.codeaurora.ims
vendor.qti.iwlan
com.qti.phone
```

## MIUI Critical Packages (Xiaomi / Redmi / POCO)

These packages are deeply integrated into MIUI's system server boot sequence. Removing them causes the system server watchdog (MediaTek AEE) to kill the process, resulting in a zygote crash loop and an unbootable device.

**NEVER remove these on MIUI devices:**

```
com.miui.securitycenter
com.miui.securitycore
com.lbe.security.miui
com.miui.home
com.miui.system
com.miui.core
com.miui.rom
com.miui.daemon
com.miui.notification
com.miui.powerkeeper
com.miui.global.packageinstaller
com.miui.face
com.miui.wmsvc
com.miui.freeform
com.xiaomi.account
com.xiaomi.misettings
com.xiaomi.xmsf
com.xiaomi.xmsfkeeper
com.xiaomi.bluetooth
```

**DANGEROUS on MIUI (remove one at a time with reboot test):**

These are removable in theory but have caused boot loops on some MIUI versions. Remove ONE AT A TIME and reboot to verify after each:

```
com.miui.msa.global          # MSA ad service — deeply tied to system server init
com.miui.cloudservice         # Cloud framework — system server dependency
com.miui.analytics            # Analytics — initialized during boot
com.miui.gallery              # Gallery — MIUI media stack depends on it
com.miui.micloudsync          # Cloud sync
com.miui.cloudbackup          # Cloud backup
com.miui.backup               # Local backup
com.miui.mishare.connectivity # Mi Share — service referenced at boot
com.xiaomi.mi_connect_service # Mi Connect — service referenced at boot
```

## Samsung Critical Packages (OneUI)

```
com.samsung.android.provider.filterprovider
com.samsung.android.incallui
com.samsung.android.honeyboard
com.samsung.android.app.telephonyui
com.samsung.android.messaging
com.sec.android.app.launcher
com.samsung.android.lool
com.samsung.android.setting.multisound
```

## Notes

- If you are unsure whether a package is safe to disable, **do not disable it**.
- If a user explicitly asks to disable a protected package, warn them about the risk and ask for double confirmation.
- Disabling `com.google.android.gms` will break nearly all Google apps and many third-party apps.
- Disabling `com.android.systemui` will make the device unusable (no status bar, no navigation).
- On MIUI, `com.miui.securitycenter` controls USB install permissions, app permissions, and battery optimization. Removing it makes the device extremely difficult to manage.
- The DANGEROUS packages on MIUI may be safe on some MIUI versions and cause boot loops on others. Always test with a reboot after each removal.
