# n3t.uk Virtual Machines Ansible Role

An Ansible role for the configuration of KVM-based QEMU virtual machines through
`libvirtd`, with the automatic creation of logical volumes as needed to populate
the drives required for each virtual machine.

## Requirements

None other than the Ansible Role, although the `libvirtd` role is recommended as
it prepares `libvirtd` with recommended settings and links the appropriate
Volume Groups into the Storage Pools. `systemd_networkd` is also used to
standardise the naming of the bridge and VLAN interfaces.

## Role Variables

| Variable        | Default | Description                                                                                          |
| :-------------- | :------ | :--------------------------------------------------------------------------------------------------- |
| `machines_list` | `[]`    | A list of virtual machines to be configured on this host, based on the options in the example below. |

### Example Virtual Machine

```yaml
machines_list:
  - # The name of the virtual machine, which will define the domain of the
    # machine on the server, as well logical volumes, and the MAC address
    name: test-01 # required
    # The environment, location, and purpose of the virtual machine, which will
    # be used to help name and describe the host, as well as the MAC address
    environment: services # required
    location: cym-south-1 # required
    purpose: test-vm # optional (default '')
    # The number of vCPUs to be allocated to the virtual machine
    vcpu: 1 # optional (default 2)
    # The amount of memory to be allocated to the virtual machine, with current
    # and maximum settings, as well as whether to enable shared memory
    memory:
      current: 4 # required
      max: 4 # optional (default as current)
      unit: G # optional (default 'G')
      shared: true # optional (default false)
    # The volumes and their size, to be connected to the virtual machine, with
    # the name of the device provided being that given inside the machine
    volumes:
      - dev: vda # required
        vg: storage # optional (default 'storage')
        size: 32G # required
      - dev: vdb
        size: 32G
    # The ID of the VLAN this virtual machine will be connected to via the
    # bridge on the underlying physical node
    vlan: 31 # required
```

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Configure the Virtual Machines
  hosts: all
  become: true
  become_user: root
  vars:
    machines_list:
      - name: test-01
        environment: services
        location: cym-south-1
        purpose: test-vm
        vcpu: 1
        memory:
          current: 4
          max: 4
          unit: G
          shared: true
        volumes:
          - dev: vda
            vg: storage
            size: 32G
          - dev: vdb
            vg: storage
            size: 32G
        vlan: 31
  roles:
    - role: systemd_networkd
    - role: libvirtd
    - role: machines
```
