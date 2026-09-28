# Infrastructure Setup

Ansible playbooks for automated Arch Linux installations with LUKS encryption, btrfs, Secure Boot, systemd-boot, and KDE Plasma 6.

## Supported machines

| Host | Type | CPU/GPU |
|------|------|---------|
| `laptop-framework-amd-ryzen-ai-300-1` | Framework Laptop | AMD Ryzen AI 300 / Radeon |
| `laptop-framework-intel-core-v12-1` | Framework Laptop | Intel Core v12 |
| `laptop-thinkpad-p14s-gen3-1` | ThinkPad P14s Gen 3 | Intel |
| `laptop-dell-xps-13-7390-1` | Dell XPS 13 7390 | Intel |
| `pc-amd-ryzen-amd-radeon-1` | Desktop PC | AMD Ryzen / AMD Radeon |

## Prerequisites

- A running Arch Linux live environment (USB installer) on the target machine
- Ansible installed in the live environment (`pacman -Sy ansible`)
- This repository cloned or copied to the live environment
- Fill in connection and credential variables in `inventory.yml` for the target host

## What gets installed

The playbooks set up, in order:

1. **Hard drive** -- GPT partitioning, LUKS2 encryption, btrfs with `@home` and `@swap` subvolumes
2. **Basic system** -- Base Arch packages, dracut UKI, systemd-boot, systemd-networkd, systemd-resolved, systemd-homed, Docker, zsh, and developer tooling
3. **Secure Boot** (optional) -- Custom signing keys, signed UKI and bootloader, auto-enrollment via systemd-boot
4. **System-specific** -- CPU microcode, GPU drivers, Wi-Fi, Bluetooth, hardware quirk workarounds
5. **Plasma 6 desktop** -- KDE Plasma meta packages, Firefox, LibreOffice, VirtualBox, Steam, and more

Each playbook creates a date-stamped btrfs root subvolume (`@root-YYYY-MM-DD`), so reinstalls preserve the home directory and previous roots.

## Usage

Replace `<host>` with one of the host names from the table above.

```bash
ansible-playbook common/setup-harddrive.yml -i inventory.yml --limit <host>
ansible-playbook common/setup-basic-system.yml -i inventory.yml --limit <host>
ansible-playbook common/setup-secure-boot.yml -i inventory.yml --limit <host>   # optional
ansible-playbook <host>/setup-system-specific.yml -i inventory.yml --limit <host>
ansible-playbook common/setup-plasma6-desktop.yml -i inventory.yml --limit <host>
```

The playbooks prompt for passwords interactively (LUKS passphrase, root password).

### Secure Boot

**Before first install:** Put the UEFI firmware into Setup Mode. The "enable setup mode" toggle in Framework BIOS may not work -- manually delete the PK, KEK, and DB entries individually in the UEFI Secure Boot settings.

On reinstall this is not needed. The signing keys persist in the `@secure-boot-keys` subvolume and the firmware already has them enrolled.

**After first install:** On the first reboot, systemd-boot auto-enrolls the Secure Boot keys (firmware must be in Setup Mode). Verify with:

```bash
bootctl status
# Expected: Secure Boot: enabled (user)
```

Then set a BIOS password in UEFI settings to prevent unauthorized Setup Mode re-entry.

### User creation

The playbooks do not create a regular user. After booting into the new system, create one with systemd-homed:

```bash
homectl create <username> --shell=/bin/zsh --member-of=wheel
```

### Printer (canon-office)

Sets up the Canon MF745C/746C office printer with the UFR II driver, double-sided printing and A4 as defaults. Run on the installed system (not the live environment); requires `cnrdrvcups-lb-bin` from AUR (see below):

```bash
ansible-playbook common/setup-printer-canon-office.yml -i inventory.yml --limit <host>
```

The printer URL comes from `printer_device_uri` in `group_vars/all.yml` (copy `group_vars/all.yml.example`) or `-e printer_device_uri=socket://...`. The playbook deliberately uses the vendor PPD instead of driverless IPP Everywhere: the printer's firmware (10.03) sends a malformed IPP attributes response that CUPS rejects, so `lpadmin -m everywhere` fails against it.

### KNX licence stick (USB/IP)

The ETS6 licence stick sits in a server that exports it over USB/IP (`usbipd`). This playbook installs `usbip`, a oneshot service that attaches the stick through `vhci-hcd` (skipped while `usbip port` already lists it) and a timer that re-runs it 30 s after boot and then every minute, so the stick returns by itself after a WiFi drop or a reboot. The VirtualBox VM running ETS captures it with its USB filter for `2a07:0102`. The server's name and the stick's bus id there (`usbip list -l` on the server) come from `usbip_server` and `usbip_busid` in `group_vars/all.yml` (copy `group_vars/all.yml.example`). Run on the installed system (not the live environment):

```bash
ansible-playbook common/setup-knx-license-stick.yml -i inventory.yml --limit <host>
```

The playbook also shortens the host's TCP keepalive probing (`/etc/sysctl.d/90-usbip-tcp-keepalive.conf`, 3 probes every 10 s on top of Arch's 120 s idle time) so a vanished USB/IP server or an address change frees the dead attach after about 150 s instead of 13 min; `usbip attach` sets keepalive on its socket but has no per-socket timing. Check with `lsusb | grep 2a07` and `journalctl -u usbip-knx-license-stick.service`; `usbip detach -p 0` (as root) frees the stick for another client, the timer takes it back within a minute unless stopped.

## Project structure

```
inventory.yml                        # Host definitions and disk/encryption variables
group_vars/all.yml.example           # Site-specific values; copy to group_vars/all.yml (gitignored)
common/
  setup-harddrive.yml                # Partitioning, encryption, btrfs subvolumes
  setup-basic-system.yml             # Base system, packages, services, bootloader
  setup-secure-boot.yml              # Secure Boot key generation, signing, enrollment
  setup-plasma6-desktop.yml          # KDE Plasma 6 and desktop applications
  setup-printer-canon-office.yml     # Canon MF745C/746C print queue (post-install)
  setup-knx-license-stick.yml        # USB/IP client for the ETS licence stick (post-install)
  files/                             # Config files deployed by common playbooks
  templates/                         # Templates rendered by common playbooks
<host>/
  setup-system-specific.yml          # Hardware-specific packages and configuration
  files/                             # Config files deployed by the host playbook
```

## Additional packages (manual)

These AUR packages are not covered by the playbooks and need to be installed manually after setup:

- `ausweisapp2`
- `claude-code`
- `cnrdrvcups-lb-bin` and `libjpeg6-turbo`
- `geekbench`
- `google-chrome`
- `jetbrains-toolbox`
- `lmstudio-beta`
- `masterpdfeditor-free`
- `openaudible-bin`
- `slack-desktop`
- `teams-for-linux-bin`
- `ttf-ms-win11-auto`
- `visual-studio-code-bin`

### Framework laptop (any model)

- `framework-system` -- CLI for the Framework embedded controller: battery charge limit, fan curve, LEDs, firmware info. Talks to the in-tree `cros_ec` driver.

### Framework AMD Ryzen AI 300 (NPU)

The `amdxdna` driver and NPU firmware ship with the in-tree kernel and `linux-firmware`, so the device shows up out of the box. Userspace runtime for inference on the NPU is AUR-only:

- `xdna-driver` -- XRT-based userspace runtime for the AMD XDNA NPU.
