---
title: Experimenting with Ansible
layout: page
---

Start by installing ansible into the virtual environment you created for NAPALM:

```terminal
(venv) $ pip install ansible
```

Once that is done, verify that you're using the virtual environments version of
ansible by typing `which ansible`.  The output of this command should reference
the executable that has been installed into the virtualenv, e.g.:

```terminal
(venv) $ which ansible
/home/grundler/ansible_venv/bin/ansible
```

Additionally, you will also want to create a file in your home directory named
`.ansible.cfg` with the following content:

```ini
[defaults]
host_key_checking = False
```

This will allow Ansible to not throw an error when it detects an unknown SSH
key.  Next, we need to define an inventory so Ansible knows how to connect to
our network devices.  Ansible inventories can dynamically pull inventory data
from remote sources such as Netbox or Cobbler, but for this workshop we will
simply use a flat file for simplicity.  Create a file named `inventory.ini` in
your current working directory with the following contents:

```ini
```

We can now validate that ansible can correctly parse this a source of inventory
data using the `ansible-inventory` command:

```terminal
```

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
