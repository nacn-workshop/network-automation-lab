---
title: Experiment with Napalm
layout: page
---

*If you haven't already followed the [setup steps]({{ "setup" | relative_url }}), complete those before continuing with this tutorial.*

The commands in this part of the tutorial need the files in the `napalm` directory: Change to it before running the subsequent commands.

```terminal
(venv) $ cd napalm
```

# Login to vEOS device

You can login to the virtual Arista device and show its current hostname:

```terminal
(venv) $ vagrant ssh eos
Last login: Wed May 29 02:32:52 2019 from 10.0.2.2

Arista Networks EOS shell

-bash-4.3# Cli
localhost>show hostname
Hostname: localhost
FQDN:     localhost
localhost>exit
-bash-4.3# exit
logout
Connection to 127.0.0.1 closed.
```

# Test Napalm command to verify connectivity

Verify your Napalm instance is correctly installed and can contact your EOS test device using this command: 

```terminal
(venv) $ napalm --user vagrant --password vagrant --vendor eos 127.0.0.1 --optional_args 'port=12443' call get_facts
{
    "hostname": "localhost",
    "fqdn": "localhost",
    "vendor": "Arista",
    "model": "vEOS",
    "serial_number": "",
    "os_version": "4.21.1.1F-10146868.42111F",
    "uptime": 1854,
    "interface_list": [
        "Ethernet1",
        "Ethernet2",
        "Management1"
    ]
}
```

This command is gathering basic information about the device and displaying it.

# Simple Napalm script to load config

Now we can run the `load_replace.py` script with the `new_hostname.conf` file as an argument:

```terminal
(venv) $ python3 load_replace.py new_hostname.conf
```

The script will display a diff of the candidate config and ask if you want to apply it. Enter `y` to apply and continue.

Login to the `eos` device again and check the hostname:

```terminal
(venv) $ vagrant ssh eos

Arista Networks EOS shell

-bash-4.3# Cli
set-with-load-replace-script>show hostname
Hostname: set-with-load-replace-script
FQDN:     set-with-load-replace-script
set-with-load-replace-script>exit
-bash-4.3# exit
logout
Connection to 127.0.0.1 closed.
```

# Summary

There are several ways to use Napalm to interact with the network devices, via the CLI or in Python scrip, via the CLI or in Python scripts

Refer to the [Napalm Documentation](https://napalm.readthedocs.io/en/latest/) for more information.

# Next

Let's continue to the [Experimenting with Ansible]({{ 'ansible' | relative_url }}) exercise.
