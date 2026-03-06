# Bloatware Database

Known bloatware and unnecessary packages by category. Agents should cross-reference installed packages against these lists.

Each package is tagged with a safety tier:
- **SAFE** — No boot risk, can remove in batches
- **CAUTION** — Low risk but OEM may depend on it, remove in small groups
- **DANGEROUS** — Can cause boot loops on heavy skins, remove one at a time with reboot verification

## Facebook System Services

Pre-installed on most OEM devices. Run in background even if the user never opens Facebook.

```
com.facebook.services        # SAFE
com.facebook.system           # SAFE
com.facebook.appmanager       # SAFE
```

## OEM Telemetry

### Motorola

```
com.motorola.ccc.devicemanagement   # SAFE
com.motorola.ccc.mainplm            # SAFE
com.motorola.ccc.notification       # SAFE
com.motorola.bug2go                  # SAFE
com.motorola.motocare                # SAFE
```

### Samsung

```
com.samsung.android.voc              # SAFE
com.samsung.android.sdm.config       # SAFE
com.samsung.android.rubin.app        # SAFE
com.sec.android.diagmonagent         # SAFE
com.samsung.android.forest           # SAFE
com.samsung.android.dqagent          # SAFE
```

### Xiaomi / Redmi

```
com.miui.analytics            # DANGEROUS — initialized during system server boot
com.miui.msa.global           # DANGEROUS — deeply tied to MIUI system server init
com.xiaomi.joyose              # CAUTION
com.xiaomi.micloud.sdk         # CAUTION
```

### OnePlus

```
com.oneplus.opbugreport        # SAFE
com.oplus.statistics.rom       # SAFE
com.oplus.oshare               # SAFE
```

## OEM Unused Features

### Motorola

```
com.motorola.mobiledesktop       # SAFE
com.motorola.mobiledesktop.core  # SAFE
com.motorola.motcameradesktop    # SAFE
com.motorola.gamemode             # SAFE
com.motorola.dimo                 # SAFE
com.motorola.freeform             # SAFE
com.motorola.spaces               # SAFE
com.motorola.personalize          # SAFE
com.motorola.aiservices           # SAFE
```

### Samsung

```
com.samsung.android.game.gamehome          # SAFE
com.samsung.android.game.gametools         # SAFE
com.samsung.android.arzone                 # SAFE
com.samsung.android.aremoji                # SAFE
com.samsung.android.visionintelligence     # SAFE
com.samsung.android.bixby.agent           # CAUTION
com.samsung.android.bixby.service         # CAUTION
com.samsung.android.bixby.avrcp           # CAUTION
com.samsung.android.app.dressroom         # SAFE
com.samsung.android.mateagent             # SAFE
```

### Xiaomi / Redmi

```
com.miui.videoplayer           # CAUTION — some MIUI components reference it
com.miui.player                # CAUTION — some MIUI components reference it
com.miui.compass               # SAFE
com.miui.yellowpage            # SAFE
com.xiaomi.gamecenter.sdk.service  # SAFE
com.miui.bugreport             # SAFE
com.miui.weather2              # SAFE
com.miui.notes                 # SAFE
com.miui.calculator            # SAFE
com.miui.touchassistant        # SAFE
com.miui.qr                    # SAFE
com.xiaomi.scanner             # SAFE
com.miui.extraphoto            # SAFE
com.miui.mediaeditor           # SAFE
com.miui.newmidrive            # SAFE
com.miui.android.fashiongallery  # SAFE
com.miui.fm                    # SAFE
com.miui.gallery               # DANGEROUS — MIUI media stack depends on it
com.miui.cloudservice          # DANGEROUS — system server dependency
com.miui.micloudsync           # DANGEROUS — cloud sync tied to system services
com.miui.cloudbackup           # DANGEROUS — cloud backup framework
com.miui.backup                # DANGEROUS — backup framework
com.miui.mishare.connectivity  # DANGEROUS — service referenced during boot
com.xiaomi.mi_connect_service  # DANGEROUS — service referenced during boot
com.xiaomi.midrop              # CAUTION
com.xiaomi.miplay_client       # CAUTION
com.xiaomi.mipicks             # SAFE
com.xiaomi.discover            # SAFE
com.xiaomi.finddevice          # SAFE
com.xiaomi.payment             # SAFE
com.mipay.wallet.in            # SAFE
```

### OnePlus

```
com.oneplus.gamespace           # SAFE
com.oplus.arunit                # SAFE
com.heytap.browser              # SAFE
com.heytap.music                # SAFE
```

## Google Bloat

Apps that come pre-installed and often run background services unnecessarily.

```
com.google.android.youtube                        # SAFE
com.google.android.gm                             # SAFE
com.google.android.apps.maps                      # SAFE
com.google.android.calendar                        # SAFE
com.google.android.apps.photos                     # SAFE
com.google.android.apps.docs                       # SAFE
com.google.android.apps.docs.editors.sheets        # SAFE
com.google.android.apps.translate                  # SAFE
com.google.ar.lens                                 # SAFE
com.google.android.apps.wellbeing                  # SAFE
com.google.android.projection.gearhead             # SAFE
com.google.android.apps.walletnfcrel               # SAFE
com.google.android.googlequicksearchbox            # SAFE
com.google.android.apps.googleassistant            # SAFE
com.google.android.apps.chromecast.app             # SAFE
com.google.android.videos                          # SAFE
com.google.android.apps.magazines                  # SAFE
com.google.android.apps.subscriptions.red          # SAFE
com.google.ambient.streaming                       # SAFE
com.google.android.adservices.api                  # SAFE
com.google.android.apps.youtube.music.setupwizard  # SAFE
```

## Browsers

```
com.android.chrome             # SAFE
com.mi.globalbrowser           # SAFE
com.android.browser            # SAFE
com.heytap.browser             # SAFE
```

## Carrier / Region Bloat

Common across multiple OEMs, varies by region.

```
com.dti.motorola               # SAFE
com.dti.amx                    # SAFE
com.taboola.mip                # SAFE
com.aura.oobe.deutsche         # SAFE
com.aura.oobe.motorola         # SAFE
com.orange.aura.oobe           # SAFE
com.orange.update               # SAFE
de.telekom.tsc                 # SAFE
com.claroColombia.contenedor   # SAFE
com.payjoy.access              # SAFE
com.amazon.appmanager          # SAFE
cn.wps.xiaomi.abroad.lite      # SAFE
```

## User Apps — Known Heavy Background Activity

These are commonly installed apps known for aggressive background services, tracking, and battery drain. Only flag if actually installed as user apps.

```
com.einnovation.temu           # SAFE
com.zzkko                      # SAFE
com.shopee.br                  # SAFE
com.inmobi.weather             # SAFE
com.lemon.lvoverseas           # SAFE
com.zhiliaoapp.musically       # SAFE
```

## Notes

- This database is not exhaustive. If an installed package looks like bloatware but isn't listed here, research it and ask the user.
- Package names can vary by region and carrier. Use pattern matching when appropriate (e.g. `com.motorola.ccc.*`).
- Some packages in this list may be protected by the system and refuse `pm disable-user`. That's expected — skip and report.
- **DANGEROUS packages on MIUI must NEVER be removed in bulk.** Remove one, reboot, verify boot completes, then proceed to the next.
- Safety tiers are based on real-world testing. A package marked SAFE on one OEM may be DANGEROUS on another. When in doubt, treat as CAUTION.
