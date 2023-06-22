exodious.proxmoxer
==================

Removes distro package of [proxmoxer](https://github.com/proxmoxer/proxmoxer) and installs the specified version of same via pip.

Requirements
------------

None

Role Variables
--------------

- `proxmoxer_version`: the pip version constraints for proxmoxer

Dependencies
------------

None

Example Playbook
----------------

```yaml
- hosts: servers
  roles:
    - exodious.proxmoxer
```

License
-------

MIT

Author Information
------------------

https://github.com/exodious
