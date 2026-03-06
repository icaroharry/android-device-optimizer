# Android Device Optimizer

An [Agent Skill](https://agentskills.io) that analyzes and optimizes Android devices connected via ADB. It detects bloatware, background services, memory hogs, and duplicate apps, then interactively disables or uninstalls them in user-approved batches.

## What it does

- Identifies your device (brand, model, chipset, RAM, battery, Android skin)
- Detects the ROM/skin (MIUI, OneUI, ColorOS, OxygenOS) and adjusts safety thresholds
- Pulls installed packages, running services, and memory usage
- Cross-references against a known bloatware database (Motorola, Samsung, Xiaomi, OnePlus, Google, carrier bloat)
- Classifies packages into safety tiers (SAFE / CAUTION / DANGEROUS)
- Creates a restore point before making changes
- Organizes findings into batches and prompts you before each one
- Removes DANGEROUS packages one at a time with reboot verification
- Shows before/after metrics (RAM freed, services reduced)
- Includes recovery procedures for boot loops

## Install

```bash
npx skills add icaroharry/android-device-optimizer
```

## Requirements

- [ADB](https://developer.android.com/tools/releases/platform-tools) installed and in PATH
- An Android device with USB debugging enabled

The skill includes setup instructions for enabling Developer Mode and installing ADB if needed.

## Skill structure

```
android-device-optimizer/
├── SKILL.md                              # Agent instructions and workflow
└── references/
    ├── bloatware-db.md                   # Known bloatware by OEM and category with safety tiers
    ├── protected-packages.md             # Critical packages that must never be touched
    └── recovery-procedures.md            # Boot loop recovery and parallel restore technique
```

## Supported OEMs

The bloatware database includes entries for:

- **Motorola** — telemetry, desktop mode, game mode, AI services
- **Samsung** — Bixby, AR Zone, game tools, diagnostics
- **Xiaomi / Redmi / POCO** — analytics, MSA, Joyose, cloud services, game center
- **OnePlus** — bug reports, OShare, HeyTap bloat
- **Google** — pre-installed apps with unnecessary background services
- **Carrier bloat** — DTI, Taboola, Aura, regional carrier apps

## How it works

The agent follows a 6-phase workflow:

1. **Discover** — detect devices, collect specs, identify Android skin
2. **Analyze** — pull packages, services, memory usage
3. **Backup** — save restore point of all installed packages
4. **Classify** — assign safety tiers, organize into batches
5. **Execute** — prompt the user per batch; DANGEROUS packages removed one at a time with reboot verification
6. **Verify** — show before/after comparison

All changes are reversible:
- Packages are removed with `pm uninstall -k --user 0` (keeps data, reinstallable)
- Can be restored with `cmd package install-existing <package>`
- If a boot loop occurs, the parallel restore technique can recover the device

## Safety tiers

| Tier | Risk | Strategy |
|------|------|----------|
| **SAFE** | No boot risk | Remove in batches |
| **CAUTION** | May affect OEM features | Remove in small groups (max 5) |
| **DANGEROUS** | Can cause boot loops | Remove one at a time, reboot after each |

## License

MIT
