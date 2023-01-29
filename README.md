![Ansible Lint](https://github.com/Qubinode/setup-kvmhost/workflows/Ansible%20Lint/badge.svg?branch=dev)

Qubinode kvmhost setup role 
=========
KVM Host Setup for RHEL Qubinode servers

Dependencies
------------

The role uses the network collections from rhel-system-roles.
```
sudo dnf -y install rhel-system-roles
```

Requirements
------------

* Ansible 

Role Variables
--------------

See defaults/main.yml.


Example Playbook
----------------

```
---
- name: Configure RHEL as virtualization host
  hosts: baremetal
  become: yes
  vars:
    kvmhost_bridge_device: vmbr0
    kvm_host_ip: "192.168.1.2"
    kvm_host_gw: "192.168.1.1"
    kvm_host_search_domains: 
      - "lab.example.com"
       - "example.com"
    kvm_host_interface: 'enp7s0'
    kvm_host_mask_prefix: 24
    kvm_host_dns_servers:
      - "192.168.1.1"
      - "8.8.8.8" 
    network_connections:
      # Define the bridge interface
      - name: "{{ kvmhost_bridge_device }}"
        state: up
        type: bridge
        interface_name: "{{ kvmhost_bridge_device }}"
        ip:
          dhcp4: false
          auto6: false
          address:
            - "{{ kvm_host_ip }}/{{ kvm_host_mask_prefix }}"
          gateway4: "{{ kvm_host_gw }}"
          dns: "{{ kvm_host_dns_servers }}"
          dns_search: "{{ kvm_host_search_domains }}"
      # set an ethernet port to the bridge
      - name: "{{ kvm_host_interface }}"
        state: up
        type: ethernet
        interface_name: "{{ kvm_host_interface }}"
        controller: "{{ kvmhost_bridge_device }}"
        port_type: bridge

  roles:
    - role: setup-kvmhost
```
          

License
-------

BSD

Author Information
------------------

* Rodrique Heron - [flyemsafe](https://github.com/flyemsafe)
