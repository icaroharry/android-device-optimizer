# Bloatware Database

Known bloatware and unnecessary packages by category. Agents should cross-reference installed packages against these lists.

## Facebook System Services

Pre-installed on most OEM devices. Run in background even if the user never opens Facebook.

```
com.facebook.services
com.facebook.system
com.facebook.appmanager
```

## OEM Telemetry

### Motorola

```
com.motorola.ccc.devicemanagement
com.motorola.ccc.mainplm
com.motorola.ccc.notification
com.motorola.bug2go
com.motorola.motocare
```

### Samsung

```
com.samsung.android.voc
com.samsung.android.sdm.config
com.samsung.android.rubin.app
com.sec.android.diagmonagent
com.samsung.android.forest
com.samsung.android.dqagent
```

### Xiaomi / Redmi

```
com.miui.analytics
com.miui.msa.global
com.xiaomi.joyose
com.xiaomi.micloud.sdk
```

### OnePlus

```
com.oneplus.opbugreport
com.oplus.statistics.rom
com.oplus.oshare
```

## OEM Unused Features

### Motorola

```
com.motorola.mobiledesktop
com.motorola.mobiledesktop.core
com.motorola.motcameradesktop
com.motorola.gamemode
com.motorola.dimo
com.motorola.freeform
com.motorola.spaces
com.motorola.personalize
com.motorola.aiservices
```

### Samsung

```
com.samsung.android.game.gamehome
com.samsung.android.game.gametools
com.samsung.android.arzone
com.samsung.android.aremoji
com.samsung.android.visionintelligence
com.samsung.android.bixby.agent
com.samsung.android.bixby.service
com.samsung.android.bixby.avrcp
com.samsung.android.app.dressroom
com.samsung.android.mateagent
```

### Xiaomi / Redmi

```
com.miui.videoplayer
com.miui.player
com.miui.compass
com.miui.yellowpage
com.xiaomi.gamecenter.sdk.service
com.miui.bugreport
```

### OnePlus

```
com.oneplus.gamespace
com.oplus.arunit
com.heytap.browser
com.heytap.music
```

## Google Bloat

Apps that come pre-installed and often run background services unnecessarily.

```
com.google.android.apps.chromecast.app
com.google.android.videos
com.google.android.apps.magazines
com.google.android.apps.subscriptions.red
com.google.ambient.streaming
com.google.android.adservices.api
com.google.android.apps.youtube.music.setupwizard
```

## Carrier / Region Bloat

Common across multiple OEMs, varies by region.

```
com.dti.motorola
com.dti.amx
com.taboola.mip
com.aura.oobe.deutsche
com.aura.oobe.motorola
com.orange.aura.oobe
com.orange.update
de.telekom.tsc
com.claroColombia.contenedor
com.payjoy.access
com.amazon.appmanager
```

## User Apps — Known Heavy Background Activity

These are commonly installed apps known for aggressive background services, tracking, and battery drain. Only flag if actually installed as user apps.

```
com.einnovation.temu
com.zzkko
com.shopee.br
com.inmobi.weather
com.lemon.lvoverseas
com.zhiliaoapp.musically
```

## Notes

- This database is not exhaustive. If an installed package looks like bloatware but isn't listed here, research it and ask the user.
- Package names can vary by region and carrier. Use pattern matching when appropriate (e.g. `com.motorola.ccc.*`).
- Some packages in this list may be protected by the system and refuse `pm disable-user`. That's expected — skip and report.
