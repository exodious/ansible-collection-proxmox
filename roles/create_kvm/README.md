exodious.create_kvm
===================

The Proxmox API and the community.general.proxmox_kvm module have some quirks that can make creating and cloning VMs challenging. This role aims to simplify these usages.

Requirements
------------

The [proxmoxer](../proxmoxer/README.md) role needs to be executed at least once in order for a majority of the tasks in this role to function.

Alternatively, the proxmoxer package can be installed manually via pip... but THIS! IS! ANSIBLE!

Role Variables
--------------

This role has variables for most of the parameters to the community.general.proxmox_kvm, community.general.proxmox_disk, and community.general.proxmox_nic modules, prefixed with `pve_`. E.g., `api_host` is configurable via `pve_api_host`. Only unique variables and additional usage of the aforementioned module variables are documented below.

- `pve_firewall`: whether to install a Proxmox guest firewall.
- `pve_post_clone_delay_secs`: open loop delay after cloning before moving any VM disks. Seems to resolve intermittent move task failures because something with Proxmox hasn't detected the new disk yet. The community.general.proxmox_kvm module does not adequately handle these task failures.
- `pve_start`: whether to start the guest after creation/cloning and configuration.
- `pve_storage`: target storage for the VM disks.
- `pve_update`: whether to update the VM configuration after cloning. Because cloning and configuration happen in separate steps, you'll generally want this to be `true` when cloning a VM. TODO is this relevant to creating vs cloning?
- `pve_clone`: name of the template from which to clone the VM. If set, the VM will be created via cloning instead of from scratch.
- `pve_disks`: list of disks to move, create, resize, or otherwise configure. Each entry is an object consisting of the following keys:
    - `disk`: disk identifier, e.g. scsi0, virtio0
    - Any other parameter to the community.general.proxmox_disk module, except `vmid` which is automatically provided.
- `pve_nics`: list of NICs to create or otherwise configure. Each entry is an object consisting of the following keys:
    - `interface`: NIC identifier, e.g. net0
    - Any other parameter to the community.general.proxmox_nic module, except `vmid` which is automatically provided.

Dependencies
------------

None

Example Playbook
----------------

```yaml
- name: Clone VM
  hosts: proxmox_api

  roles:
    - role: exodious.proxmox.proxmoxer
    - role: exodious.proxmox.create_kvm
      vars:
        pve_api_host: "{{ inventory_hostname }}"
        pve_api_token_id: "SECRET SQUIRREL TOKEN ID"
        pve_api_token_secret: "SECRET SQUIRREL TOKEN SECRET"
        pve_clone: ubuntu-2204-cloud-template
        pve_full: true
        pve_node: "{{ inventory_hostname_short }}"
        pve_storage: local-lvm
        pve_vmid: 9000
        pve_sshkeys: "SOME_SSH_KEY"
        pve_cipassword: "SUPER DUPER SECRET PASSWORD"
        pve_name: myvm
        pve_newid: 9001
        pve_target: "{{ inventory_hostname_short }}"
        pve_cores: 2
        pve_memory: 2048
        pve_disks:
          - disk: scsi0
            size: 10
          - disk: ide2
        pve_ipconfig:
          ipconfig0: ip=dhcp
```

License
-------

MIT

Author Information
------------------

https://github.com/exodious
