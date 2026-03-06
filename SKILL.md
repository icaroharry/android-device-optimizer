---
name: android-device-optimizer
description: Analyzes and optimizes Android devices connected via ADB. Detects bloatware, background services, memory hogs, and duplicate apps, then interactively disables or uninstalls them in user-approved batches. Use when the user asks to optimize, debloat, clean up, or speed up an Android phone or tablet.
compatibility: Requires adb (Android Debug Bridge) in PATH and a device with USB debugging enabled.
metadata:
  author: icarominka
  version: "2.0"
---

# Android Device Optimizer

Optimize Android devices by removing bloatware, disabling unnecessary background services, and freeing RAM — all interactively with user approval at every step.

## Prerequisites

Before starting, verify that ADB is installed and the device has USB debugging enabled. If either is missing, guide the user through setup.

### Installing ADB

Check if ADB is available:

```bash
adb version
```

If not found, help the user install it:

- **macOS**: `brew install android-platform-tools`
- **Linux (Debian/Ubuntu)**: `sudo apt install android-tools-adb`
- **Linux (Fedora)**: `sudo dnf install android-tools`
- **Windows**: `winget install Google.PlatformTools` or download from https://developer.android.com/tools/releases/platform-tools and add to PATH

After installing, verify with `adb version`.

### Enabling Developer Mode on the phone

Guide the user through these steps on the Android device:

1. Open **Settings > About phone**
2. Tap **Build number** 7 times — a toast will say "You are now a developer!"
3. Go back to **Settings > System > Developer options** (location may vary by OEM)
4. Enable **USB debugging**

On some devices the path differs:
- **Samsung**: Settings > About phone > Software information > Build number
- **Xiaomi/Redmi**: Settings > About phone > MIUI version (tap 7 times), then Settings > Additional settings > Developer options > USB debugging
- **Motorola**: Settings > About phone > Build number

### Connecting the device

1. Connect the phone to the computer via USB cable
2. On the phone, a prompt will appear: **"Allow USB debugging?"** — tap **Allow** (optionally check "Always allow from this computer")
3. Verify the connection:

```bash
adb devices
```

The device should appear with status `device`. If it shows `unauthorized`, the user needs to accept the prompt on the phone. If it shows `offline`, try `adb kill-server && adb start-server`.

## Workflow

Follow these phases in order:

### Phase 1: Discover

Detect connected devices and collect specs.

```bash
adb devices
```

If multiple devices are found, ask the user which one to optimize. Then collect device info:

```bash
DEVICE="<serial>"
adb -s $DEVICE shell getprop ro.product.brand
adb -s $DEVICE shell getprop ro.product.model
adb -s $DEVICE shell getprop ro.product.marketname
adb -s $DEVICE shell getprop ro.build.version.release
adb -s $DEVICE shell getprop ro.board.platform
adb -s $DEVICE shell "cat /proc/cpuinfo | grep processor | wc -l"
adb -s $DEVICE shell cat /proc/meminfo
adb -s $DEVICE shell dumpsys battery
```

**Detect the Android skin/ROM** — this is critical for safety classification:

```bash
# MIUI (Xiaomi/Redmi/POCO)
adb -s $DEVICE shell getprop ro.miui.ui.version.name

# OneUI (Samsung)
adb -s $DEVICE shell getprop ro.build.version.oneui

# ColorOS (OPPO/Realme)
adb -s $DEVICE shell getprop ro.build.version.oplusrom

# OxygenOS (OnePlus)
adb -s $DEVICE shell getprop ro.oxygen.version
```

Present a summary table to the user, including the detected skin. **Warn the user if the device is running MIUI** — it has aggressive inter-package dependencies and requires extra caution.

### Phase 2: Analyze

Collect installed packages, running services, and memory usage:

```bash
# Third-party apps
adb -s $DEVICE shell "pm list packages -3"

# System apps
adb -s $DEVICE shell "pm list packages -s"

# Already disabled packages
adb -s $DEVICE shell "pm list packages -d"

# Running services
adb -s $DEVICE shell "dumpsys activity services" | grep 'ServiceRecord'

# Memory consumers
adb -s $DEVICE shell "dumpsys meminfo"

# Doze whitelist
adb -s $DEVICE shell "dumpsys deviceidle whitelist"
```

Record a **before** snapshot of available RAM and service count.

### Phase 3: Create a restore point

**CRITICAL: Always save the list of packages before making changes.** This enables recovery if something goes wrong.

```bash
# Save full package list to device
adb -s $DEVICE shell "pm list packages" > /tmp/android-optimizer-backup-$(date +%Y%m%d-%H%M%S).txt

# Save to device storage as well
adb -s $DEVICE shell "pm list packages > /sdcard/package-backup.txt"
```

Tell the user: **"I've saved a backup of all installed packages. If anything goes wrong, we can restore them."**

### Phase 4: Classify into safety tiers and batches

Cross-reference findings against the bloatware databases in [references/bloatware-db.md](references/bloatware-db.md). Never touch packages listed in [references/protected-packages.md](references/protected-packages.md).

#### Safety tier classification

Every package to be removed must be assigned a safety tier:

| Tier | Risk | Examples | Removal strategy |
|------|------|----------|------------------|
| **SAFE** | No boot risk | Facebook services, third-party apps, Google apps (YouTube, Maps, etc.), carrier bloat | Can remove in batches |
| **CAUTION** | Low boot risk, OEM may depend on it | OEM media players, galleries, file managers, weather apps | Remove in small batches (max 5), verify device still works |
| **DANGEROUS** | Can cause boot loops on heavy skins | OEM ad services (MSA), cloud services, analytics, core OEM frameworks | Remove ONE AT A TIME, verify boot after each |

**For MIUI devices specifically**, the following are DANGEROUS tier — see [references/protected-packages.md](references/protected-packages.md) for the full list:
- `com.miui.msa.global` (MSA ad service — deeply integrated into MIUI system server)
- `com.miui.cloudservice` (cloud framework — system server depends on it)
- `com.miui.analytics` (analytics — system server initializes it at boot)
- `com.miui.gallery` (gallery — MIUI media stack depends on it)
- `com.miui.securitycenter` (Security Center — NEVER remove, controls device permissions)

#### Batch organization

Organize into batches in this order:

1. **System adware/tracking** — Facebook services, ad SDKs
2. **OEM telemetry** — manufacturer analytics, crash reporting
3. **OEM unused features** — desktop mode, game mode, AI assistants
4. **Google bloat** — unused Google apps running in background
5. **Carrier/region bloat** — DTI, Taboola, Aura, carrier configs
6. **Google ad services** — ad tracking infrastructure
7. **Misc OEM bloat** — remaining low-value OEM packages
8. **User apps (heavy)** — shopping apps, ad-heavy apps with aggressive background services
9. **User apps (duplicates)** — redundant apps in the same category (e.g. two launchers, two wearable apps)
10. **User apps (unused)** — stale installs, old games, leftovers

Only include packages that are actually installed on the device.

**Within each batch, group by safety tier.** SAFE packages can be done together. DANGEROUS packages must be done individually.

### Phase 5: Execute (interactive)

For **each batch**:

1. Present the batch name, the list of packages with their safety tier, and a brief explanation of why they're candidates
2. **Wait for explicit user approval** before running anything
3. Respect user overrides — if they say "keep X", remove it from the batch
4. Execute based on safety tier:

**SAFE tier packages:**
```bash
# Can remove in batch
for pkg in <packages>; do
  adb -s $DEVICE shell pm uninstall -k --user 0 $pkg && echo "REMOVED: $pkg" || echo "FAILED: $pkg"
done
```

**CAUTION tier packages:**
```bash
# Remove in small groups (max 5), then verify
adb -s $DEVICE shell pm uninstall -k --user 0 <package>
# After each small group, check device is responsive:
adb -s $DEVICE shell "getprop sys.boot_completed"
```

**DANGEROUS tier packages (MIUI and heavy skins):**
```bash
# Remove ONE AT A TIME
adb -s $DEVICE shell pm uninstall -k --user 0 <package>
# Then VERIFY the device survives a reboot:
adb -s $DEVICE reboot
# Wait for boot to complete:
adb wait-for-device
# Confirm boot completed:
adb -s $DEVICE shell "while [ \"$(getprop sys.boot_completed)\" != \"1\" ]; do sleep 2; done; echo BOOT_OK"
```

If a DANGEROUS package causes a boot loop after reboot, immediately follow the recovery procedure in [references/recovery-procedures.md](references/recovery-procedures.md).

5. Report results per package (success / skipped-protected / failed)
6. If `pm disable-user` fails with "non-disable", report it and move on — do not retry

### Phase 6: Verify

Take an **after** snapshot and present a comparison table:

| Metric | Before | After |
|--------|--------|-------|
| Available RAM | X MB | Y MB |
| Free RAM | X MB | Y MB |
| Background services | X | Y |
| Packages disabled | — | N |
| Apps uninstalled | — | N |

Suggest a reboot for full effect. Ask before rebooting.

## Rules

- **NEVER** disable or uninstall without explicit user approval per batch
- **NEVER** touch protected packages (see [references/protected-packages.md](references/protected-packages.md))
- **NEVER** use `pm uninstall` (without `--user 0` or `-k --user 0`) — that requires root and is irreversible
- **NEVER** bulk-remove DANGEROUS tier packages — always one at a time with reboot verification
- **ALWAYS** create a restore point (Phase 3) before making any changes
- **ALWAYS** use `pm uninstall -k --user 0` (with `-k` to keep data) so reinstall preserves app data
- Use `pm disable-user --user 0` for system apps that refuse uninstall — this is reversible via `pm enable`
- If a device disconnects, restart ADB with `adb kill-server && adb start-server`
- Run individual commands per package (not chained with `&&`) so one failure doesn't skip the rest
- When in doubt about a package, ask the user rather than guessing
- On MIUI devices, warn the user that Xiaomi's system has deep inter-package dependencies and recommend a conservative approach

## Recovery

If a device enters a boot loop after removing packages, follow the procedures in [references/recovery-procedures.md](references/recovery-procedures.md). The key insight: packages removed with `pm uninstall -k --user 0` are NOT deleted — they remain on the system partition and can be restored with `cmd package install-existing <package>`.
