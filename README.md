Ansible Role: Proxmox
=========

Management of Proxmox from Ansible using pct (and soon qm) commands.

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
  rootfs_create: ssd:8
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

If the argument has a `-`, then it is replaced with `_`.  
For example: `ignore-unpack-errors` becomes `ignore_unpack_errors`

`rootfs` argument is separated into `rootfs_create` and `rootfs_set`. 
The reason for that is when you create it with for example `ssd:8`, proxmox will generate
the necessary files and from that point you will have to provide the disks exact location. (e.g. `ssd:vm-103-disk-0,size=8G`)

Dependencies
------------

None

Example Playbook
----------------


**CREATE or SET if run on existing container**:

```yml
- hosts: all
  become: false

  tasks:
    - name: Create container
      include_role:
        name: rlacko58.proxmox
        tasks_from: container
      vars:
        vmid: 999
        ostemplate: local:vztmpl/ubuntu-21.04-standard_21.04-1_amd64.tar.gz
        rootfs_create: ssd:8
        cores: 2
        net_arr:
          - n: 0
            args: 'name=eth0,bridge=vmbr0,ip=dhcp'
        password: '12345'
```

**CHANGE STATE**:

```yml
- hosts: all
  become: false

  tasks:
    - name: Change state of container
    include_role:
      name: rlacko58.proxmox
      tasks_from: container
    vars:
      vmid: "{{ item }}"
      state: stop # or shutdown, start, reboot, destroy
    loop:
      - 103
      - 104
```

**DESTROY**

```yml
- hosts: all
  become: false

  tasks:
    - name: Change state of container
    include_role:
      name: rlacko58.proxmox
      tasks_from: container
    vars:
      vmid: 102
      state: destroy
      destroy_unreferenced_disks: yes
      force: yes
      purge: yes
```

License
-------

MIT

Author Information
------------------

László Rafael | [Gitlab](https://gitlab.com/rlacko58)
