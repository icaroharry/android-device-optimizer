# Recovery Procedures

What to do when package removal causes a boot loop or other issues.

## Key Fact

Packages removed with `pm uninstall -k --user 0` are **NOT deleted** from the device. They remain on the system partition but are disabled for user 0. They can be restored with:

```bash
adb shell cmd package install-existing <package>
```

## Scenario 1: Device boots but an app is broken

The simplest case. Just restore the package:

```bash
adb -s <serial> shell cmd package install-existing <package>
```

## Scenario 2: Boot loop — ADB connects briefly during boot

The device shows the boot animation on loop but ADB connects for a few seconds each cycle.

### Why this happens

On MIUI and other heavy skins, the system server has hard dependencies on certain OEM packages. When it can't find them, the boot process takes too long and the system watchdog (MediaTek AEE, Qualcomm, etc.) kills the system server process (SIGKILL). Zygote sees its child die and exits, causing a restart loop.

### The challenge

- The package manager service starts BEFORE boot completes
- `cmd package install-existing` triggers a broadcast
- Broadcasting before boot completes throws `IllegalStateException: Cannot broadcast before boot completed`
- This exception crashes the system server again
- So you get a tiny window where the package service exists but any install attempt crashes it

### The fix: Parallel restore

Run all install-existing commands simultaneously as background shell processes. Each individual install may crash, but some succeed before the crash propagates:

```bash
# Wait for device, wait for package service, then parallel restore
while true; do
  if adb devices 2>/dev/null | grep "<serial>" | grep -q "device$"; then
    # Wait for package service to be ready
    while ! adb -s <serial> shell "cmd package list packages" 2>/dev/null | grep -q "package:"; do
      sleep 0.3
    done

    # Fire all restores in parallel
    adb -s <serial> shell "
      cmd package install-existing com.miui.msa.global &
      cmd package install-existing com.miui.cloudservice &
      cmd package install-existing com.miui.gallery &
      cmd package install-existing com.miui.analytics &
      # ... add all removed packages ...
      wait
    " 2>&1
    break
  fi
  sleep 0.5
done
```

**Expect ~2-3 packages to be restored per boot cycle.** The device will crash and reboot. Run the script again. Each cycle restores more packages. After enough cycles, the critical dependencies are back and the boot completes normally. Once booted, restore all remaining packages in a normal loop.

### How to identify which package caused the loop

Before attempting recovery, capture the crash log to understand what's failing:

```bash
# Catch the device during boot and dump logcat
while true; do
  if adb devices | grep "<serial>" | grep -q "device$"; then
    adb -s <serial> shell "logcat -d" > /tmp/crash.log 2>&1
    break
  fi
  sleep 1
done
```

Look for these patterns in the log:
- `Exit zygote because system server (pid XXXX) has terminated` — confirms system server crash
- `AEE_LIBAEE: shell(raise_error): safe read fail` — watchdog timeout killed the process
- `Unable to start service Intent { pkg=... }` — identifies which missing package triggered the timeout
- `init.svc.zygote: restarting` — confirms zygote restart loop

## Scenario 3: Boot loop — ADB never connects

The device is stuck on the boot logo and ADB doesn't appear at all.

### Recovery mode approach

1. Power off completely (hold Power 15+ seconds)
2. Enter recovery: hold **Power + Volume Up** (varies by device)
3. In recovery, try **"Wipe cache partition"** — this does NOT erase data
4. Reboot and see if it helps

### MiAssistant approach (MIUI only)

1. Enter recovery mode (Power + Volume Up)
2. Select **"Connect with MiAssistant"**
3. The device appears in `adb devices` in **sideload** mode
4. Note: sideload mode has limited access — no shell, no package manager
5. This is primarily useful for flashing recovery ZIPs, not for restoring packages

### Last resort: Factory reset

If nothing else works:

1. Enter recovery mode
2. Select **"Wipe data / Factory reset"**
3. **WARNING: This erases ALL user data** — apps, photos, accounts, authenticator tokens
4. The device will boot fresh with all system packages restored

Before factory resetting, try to extract critical data:
- If ADB connects briefly, try: `adb pull /sdcard/ /local/backup/`
- Authenticator apps (LastPass, Google Authenticator) store tokens in `/data/data/<package>/` which requires root to access

## Prevention

To avoid boot loops in the future:

1. **Always create a restore point** before removing packages
2. **Never bulk-remove OEM system packages** on MIUI, OneUI, or ColorOS
3. **Remove DANGEROUS packages one at a time** with a reboot test after each
4. **Test the most integrated packages first** (MSA, cloud services, analytics) — if those survive, the rest is likely safe
5. **Keep the device plugged in via USB** during the process so ADB stays available
6. If a DANGEROUS package survives a reboot after removal, it's safe to proceed to the next one
