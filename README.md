Role Name
=========

Management of Proxmox Containers from Ansible using pct commands.

Requirements
------------

None

Role Variables
--------------

Variables come from the official docs for each command of Proxmox `pct`.  
https://pve.proxmox.com/pve-docs/pct.1.html 

If the argument is an array, it becomes `<argument>_arr` with
two keys: `n`, `args`.

For example:

```yml
vars:
  vmid: 999
  ostemplate: local:vztmpl/ubuntu-21.04-standard_21.04-1_amd64.tar.gz
  rootfs: ssd:8
  cores: 2
  net_arr:
    - n: 0
      args: 'name=eth0,bridge=vmbr0,ip=dhcp'
  password: '12345'
```

Output:

```bash
pct create 999 local:vztmpl/ubuntu-21.04-standard_21.04-1_amd64.tar.gz --rootfs ssd:8 --cores 2 --net0 name=eth0,bridge=vmbr0,ip=dhcp --password 12345
```

If the argument has a `-`, then it replaced with `_`.  
For example: `ignore-unpack-errors` becomes `ignore_unpack_errors`

Dependencies
------------

None

Example Playbook
----------------


**Create**:

```yml
- hosts: all
  become: false

  tasks:
    - name: Create container
      include_role:
        name: proxmox-containers
        tasks_from: create
      vars:
        vmid: 999
        ostemplate: local:vztmpl/ubuntu-21.04-standard_21.04-1_amd64.tar.gz
        rootfs: ssd:8
        cores: 2
        net_arr:
          - n: 0
            args: 'name=eth0,bridge=vmbr0,ip=dhcp'
        password: '12345'
```

**destroy**:

```yml
- hosts: all
  become: false
  roles:
    - proxmox-containers

  tasks:
    - name: Create container
      include_role:
        name: proxmox-containers
        tasks_from: destroy
      vars:
        vmid: "{{ item }}"
        destroy_unreferenced_disks: 1
        force: 1
        purge: 1
      loop:
        - 911
        - 912
        - 996
```
License
-------

MIT

Author Information
------------------

László Rafael | [Gitlab](https://gitlab.com/rlacko58)
