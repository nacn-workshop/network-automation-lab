---
title: Experimenting with Ansible
layout: page
---

*If you haven't already followed the [setup steps]({{ "setup" | relative_url }}), complete those before continuing with this tutorial.*

The commands in this tutorial need the files in the `ansible/` directory: Change
to it before running the subsequent commands.

```terminal
$ cd ansible
```

# Validate your inventory

We can validate that our inventory is formatted correctly and contains all the
hosts we expect with the following helper command:

```terminal
(venv) $ ansible-inventory -i hosts.cfg --list
{
    "_meta": {
        "hostvars": {
            "eos": {
                "ansible_connection": "network_cli",
                "ansible_host": "127.0.0.1",
                "ansible_network_os": "eos",
                "ansible_password": "vagrant",
                "ansible_port": 12201,
                "ansible_user": "vagrant"
            }
        }
    },
    "all": {
        "children": [
            "switches",
            "ungrouped"
        ]
    },
    "switches": {
        "hosts": [
            "eos"
        ]
    }
}
```

# Run a one-off Ansible command

Ansible has some built-in modules for interacting with EOS devices. We can use
one of them, `eos_info`, to get some information about our virtual device:

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

We can further expand on this example by running arbitrary show commands with
the `eos_command` module:

```terminal
(venv) $ $ ansible -i hosts.cfg eos -m eos_command -a "commands='show version'"
eos | SUCCESS => {
    "changed": false, 
    "stdout": [
        "Arista vEOS\nHardware version:    \nSerial number:       \nSystem MAC address:  0800.277b.25dd\n\nSoftware image version: 4.21.1.1F\nArchitecture:           i386\nInternal build version: 4.21.1.1F-10146868.42111F\nInternal build ID:      ed3973a9-79db-4acc-b9ac-19b9622d23e2\n\nUptime:                 0 weeks, 0 days, 0 hours and 42 minutes\nTotal memory:           2016612 kB\nFree memory:            1391180 kB"
    ], 
    "stdout_lines": [
        [
            "Arista vEOS", 
            "Hardware version:    ", 
            "Serial number:       ", 
            "System MAC address:  0800.277b.25dd",                                                
            "", 
            "Software image version: 4.21.1.1F",                                                  
            "Architecture:           i386", 
            "Internal build version: 4.21.1.1F-10146868.42111F",                                  
            "Internal build ID:      ed3973a9-79db-4acc-b9ac-19b9622d23e2",                       
            "", 
            "Uptime:                 0 weeks, 0 days, 0 hours and 42 minutes",                    
            "Total memory:           2016612 kB",                                                 
            "Free memory:            1391180 kB"
        ]
    ]
}
```

In this example, we've used the `-a` flag to specify and additional argument to
the `eos_command` module that is the command we want to run.  Also, note in the
return output from ansible, we've recieved the data in two different formats.
In the "stdout" key, we can see the raw string contents of the "show version"
command, as well as a separate "stdout_lines" key that contains the native
output as it would be seen on the EOS CLI.
