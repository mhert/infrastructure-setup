## Laptop Framework Intel v12

```bash
ansible-playbook laptop-framework-intel-core-v12-1/setup-harddrive.yml -i inventory.yml --limit laptop-framework-intel-core-v12-1
ansible-playbook common/setup-basic-system.yml -i inventory.yml --limit laptop-framework-intel-core-v12-1
ansible-playbook laptop-framework-intel-core-v12-1/setup-system-specific.yml -i inventory.yml --limit laptop-framework-intel-core-v12-1
ansible-playbook common/setup-plasma6-desktop.yml -i inventory.yml --limit laptop-framework-intel-core-v12-1
```
## Laptop Thinkpad P14s 3rd Generation

```bash
ansible-playbook laptop-thinkpad-p14s-gen3-1/setup-harddrive.yml -i inventory.yml --limit laptop-thinkpad-p14s-gen3-1
ansible-playbook common/setup-basic-system.yml -i inventory.yml --limit laptop-thinkpad-p14s-gen3-1
ansible-playbook laptop-thinkpad-p14s-gen3-1/setup-system-specific.yml -i inventory.yml --limit laptop-thinkpad-p14s-gen3-1
ansible-playbook common/setup-plasma6-desktop.yml -i inventory.yml --limit laptop-thinkpad-p14s-gen3-1
```

## PC Amd Ryzen + AMD Radeon

```bash
ansible-playbook pc-amd-ryzen-amd-radeon-1/setup-harddrive.yml -i inventory.yml --limit pc-amd-ryzen-amd-radeon-1
ansible-playbook common/setup-basic-system.yml -i inventory.yml --limit pc-amd-ryzen-amd-radeon-1
ansible-playbook pc-amd-ryzen-amd-radeon-1/setup-system-specific.yml -i inventory.yml --limit pc-amd-ryzen-amd-radeon-1
ansible-playbook common/setup-plasma6-desktop.yml -i inventory.yml --limit pc-amd-ryzen-amd-radeon-1
```
