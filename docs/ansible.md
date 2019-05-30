---
title: Experimenting with Ansible
layout: page
---

*If you haven't already followed the [setup steps]({{ "setup" | relative_url }}), complete those before continuing with this tutorial.*

The commands in this tutorial need the files in the `ansible` directory: Change to it before running the subsequent commands.

```terminal
$ cd ansible
```

# Run a one-off Ansible command

Ansible has some built-in modules for interacting with EOS devices. We can use one of them, `eos_info`, to get some information about our virtual device:

```terminal
(venv) $ ansible -i hosts.cfg eos -m eos_facts
eos | SUCCESS =﹥ {
    "ansible_facts": {
        "ansible_net_all_ipv4_addresses": [
            "10.0.2.15"
        ],
        "ansible_net_all_ipv6_addresses": [],
        "ansible_net_api": "cliconf",
        "ansible_net_filesystems": [
            "file:",
            "flash:",
            "system:"
        ],
        "ansible_net_fqdn": "localhost",
        "ansible_net_gather_subset": [
            "hardware",
            "interfaces",
            "default"
        ],
        "ansible_net_hostname": "localhost",
        "ansible_net_image": "flash:/vEOS-lab.swi",
        "ansible_net_interfaces": {
            "Management1": {
                "bandwidth": 1000000000,
                "description": "",
                "duplex": "duplexFull",
                "ipv4": {
                    "address": "10.0.2.15",
                    "masklen": 24
                },
                "lineprotocol": "up",
                "macaddress": "08:00:27:f5:d6:58",
                "mtu": 1500,
                "operstatus": "connected",
                "type": "routed"
            }
        },
        "ansible_net_memfree_mb": 1358.3828125,
        "ansible_net_memtotal_mb": 1969.34765625,
        "ansible_net_model": "vEOS",
        "ansible_net_neighbors": {},
        "ansible_net_python_version": "3.7.3",
        "ansible_net_serialnum": "",
        "ansible_net_system": "eos",
        "ansible_net_version": "4.21.1.1F"
    },
    "changed": false
}
```

Let's break down what's happening with this command:
  - The `ansible` part is the command we're calling. There are several other--more commonly used--commands that we'll see later.
  - The `-i hosts.cfg` part is telling Ansible to use that file for its inventory.
  - The `eos` part is specifying with host to target.
  - The `-m eos_facts` part is telling Ansible to run the eos\_facts module.
