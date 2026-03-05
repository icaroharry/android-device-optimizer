# Android Device Optimizer

An [Agent Skill](https://agentskills.io) that analyzes and optimizes Android devices connected via ADB. It detects bloatware, background services, memory hogs, and duplicate apps, then interactively disables or uninstalls them in user-approved batches.

## What it does

- Identifies your device (brand, model, chipset, RAM, battery)
- Pulls installed packages, running services, and memory usage
- Cross-references against a known bloatware database (Motorola, Samsung, Xiaomi, OnePlus, Google, carrier bloat)
- Organizes findings into batches and prompts you before each one
- Disables system bloat and uninstalls user apps with your approval
- Shows before/after metrics (RAM freed, services reduced)

## Install

```bash
npx skillsadd icarominka/android-device-optimizer
```

## Requirements

- [ADB](https://developer.android.com/tools/releases/platform-tools) installed and in PATH
- An Android device with USB debugging enabled

The skill includes setup instructions for enabling Developer Mode and installing ADB if needed.

## Skill structure

```
android-device-optimizer/
├── SKILL.md                        # Agent instructions and workflow
└── references/
    ├── bloatware-db.md             # Known bloatware by OEM and category
    └── protected-packages.md       # Critical packages that must never be touched
```

## Supported OEMs

The bloatware database includes entries for:

- **Motorola** — telemetry, desktop mode, game mode, AI services
- **Samsung** — Bixby, AR Zone, game tools, diagnostics
- **Xiaomi / Redmi** — analytics, MSA, Joyose, game center
- **OnePlus** — bug reports, OShare, HeyTap bloat
- **Google** — pre-installed apps with unnecessary background services
- **Carrier bloat** — DTI, Taboola, Aura, regional carrier apps

## How it works

The agent follows a 5-phase workflow:

1. **Discover** — detect devices, collect specs
2. **Analyze** — pull packages, services, memory usage
3. **Classify** — organize into batches against the bloatware DB
4. **Execute** — prompt the user per batch, then disable/uninstall
5. **Verify** — show before/after comparison

All changes are reversible:
- System apps are disabled with `pm disable-user --user 0` (re-enable with `pm enable`)
- User apps are uninstalled for the current user only (reinstallable from Play Store)

## License

MIT
