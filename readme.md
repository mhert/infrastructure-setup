## Laptop Framework Intel v12

### Pre-requisites (Secure Boot)

On first install, put the UEFI firmware into Setup Mode. The "enable setup mode" toggle in Framework BIOS may not work - manually delete the PK, KEK, and DB entries individually in the UEFI Secure Boot settings.

On reinstall, this is not needed - the signing keys persist in the `@secure-boot-keys` subvolume and the firmware already has them enrolled.

### Install

```bash
ansible-playbook common/setup-harddrive.yml -i inventory.yml --limit laptop-framework-intel-core-v12-1
ansible-playbook common/setup-basic-system.yml -i inventory.yml --limit laptop-framework-intel-core-v12-1
ansible-playbook common/setup-secure-boot.yml -i inventory.yml --limit laptop-framework-intel-core-v12-1
ansible-playbook laptop-framework-intel-core-v12-1/setup-system-specific.yml -i inventory.yml --limit laptop-framework-intel-core-v12-1
ansible-playbook common/setup-plasma6-desktop.yml -i inventory.yml --limit laptop-framework-intel-core-v12-1
```

### Post-install (Secure Boot)

After the first reboot, systemd-boot auto-enrolls the Secure Boot keys (firmware must be in Setup Mode).

Verify with:
```bash
bootctl status
# Expected: Secure Boot: enabled (user)
```

Then set a BIOS password in UEFI settings to prevent unauthorized Setup Mode re-entry.

## Laptop Framework AMD Ryzen AI 300

### Pre-requisites (Secure Boot)

On first install, put the UEFI firmware into Setup Mode. The "enable setup mode" toggle in Framework BIOS may not work - manually delete the PK, KEK, and DB entries individually in the UEFI Secure Boot settings.

On reinstall, this is not needed - the signing keys persist in the `@secure-boot-keys` subvolume and the firmware already has them enrolled.

### Install

```bash
ansible-playbook common/setup-harddrive.yml -i inventory.yml --limit laptop-framework-amd-ryzen-ai-300-1
ansible-playbook common/setup-basic-system.yml -i inventory.yml --limit laptop-framework-amd-ryzen-ai-300-1
ansible-playbook common/setup-secure-boot.yml -i inventory.yml --limit laptop-framework-amd-ryzen-ai-300-1
ansible-playbook laptop-framework-amd-ryzen-ai-300-1/setup-system-specific.yml -i inventory.yml --limit laptop-framework-amd-ryzen-ai-300-1
ansible-playbook common/setup-plasma6-desktop.yml -i inventory.yml --limit laptop-framework-amd-ryzen-ai-300-1
```

### Post-install (Secure Boot)

After the first reboot, systemd-boot auto-enrolls the Secure Boot keys (firmware must be in Setup Mode).

Verify with:
```bash
bootctl status
# Expected: Secure Boot: enabled (user)
```

Then set a BIOS password in UEFI settings to prevent unauthorized Setup Mode re-entry.

## Laptop Thinkpad P14s 3rd Generation

```bash
ansible-playbook common/setup-harddrive.yml -i inventory.yml --limit laptop-thinkpad-p14s-gen3-1
ansible-playbook common/setup-basic-system.yml -i inventory.yml --limit laptop-thinkpad-p14s-gen3-1
ansible-playbook laptop-thinkpad-p14s-gen3-1/setup-system-specific.yml -i inventory.yml --limit laptop-thinkpad-p14s-gen3-1
ansible-playbook common/setup-plasma6-desktop.yml -i inventory.yml --limit laptop-thinkpad-p14s-gen3-1
```

## PC Amd Ryzen + AMD Radeon

```bash
ansible-playbook common/setup-harddrive.yml -i inventory.yml --limit pc-amd-ryzen-amd-radeon-1
ansible-playbook common/setup-basic-system.yml -i inventory.yml --limit pc-amd-ryzen-amd-radeon-1
ansible-playbook pc-amd-ryzen-amd-radeon-1/setup-system-specific.yml -i inventory.yml --limit pc-amd-ryzen-amd-radeon-1
ansible-playbook common/setup-plasma6-desktop.yml -i inventory.yml --limit pc-amd-ryzen-amd-radeon-1
```

## Laptop Dell XPS13 7390

```bash
ansible-playbook common/setup-harddrive.yml -i inventory.yml --limit laptop-dell-xps-13-7390-1
ansible-playbook common/setup-basic-system.yml -i inventory.yml --limit laptop-dell-xps-13-7390-1
ansible-playbook laptop-dell-xps-13-7390-1/setup-system-specific.yml -i inventory.yml --limit laptop-dell-xps-13-7390-1
ansible-playbook common/setup-plasma6-desktop.yml -i inventory.yml --limit laptop-dell-xps-13-7390-1
```

## Additional packages

### AUR
- `ausweisapp2`
- `claude-code`
- `cnrdrvcups-lb-bin` and `libjpeg6-turbo`
- `geekbench`
- `google-chrome`
- `jetbrains-toolbox`
- `joplin-bin`
- `lmstudio-beta`
- `masterpdfeditor-free`
- `openaudible-bin`
- `slack-desktop`
- `teams-for-linux-bin`
- `ttf-ms-win11-auto`
- `visual-studio-code-bin`