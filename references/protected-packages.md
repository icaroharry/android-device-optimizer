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

## Notes

- If you are unsure whether a package is safe to disable, **do not disable it**.
- If a user explicitly asks to disable a protected package, warn them about the risk and ask for double confirmation.
- Disabling `com.google.android.gms` will break nearly all Google apps and many third-party apps.
- Disabling `com.android.systemui` will make the device unusable (no status bar, no navigation).
