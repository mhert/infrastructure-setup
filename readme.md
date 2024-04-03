## Laptop Framework Intel v12

```bash
ansible-playbook laptop-framework-intel-core-v12-1/setup-harddrive.yml -i inventory.yml --limit laptop-framework-intel-core-v12-1
ansible-playbook common/setup-basic-system.yml -i inventory.yml --limit laptop-framework-intel-core-v12-1
ansible-playbook laptop-framework-intel-core-v12-1/setup-system-specific.yml -i inventory.yml --limit laptop-framework-intel-core-v12-1
ansible-playbook common/setup-plasma6-desktop.yml -i inventory.yml --limit laptop-framework-intel-core-v12-1
```
