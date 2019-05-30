---
title: Experiment with Napalm
layout: page
---

*If you haven't already followed the [setup steps]({{ "setup" | relative_url }}), complete those before continuing with this tutorial.*

The commands in this part of the tutorial need the files in the `napalm` directory: Change to it before running the subsequent commands.

```terminal
$ cd napalm
```

# Login to vEOS device

You can login to the virtual Arista device and show its current hostname:

```terminal
$ vagrant ssh eos
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

Verify your NAPALM instance is correctly installed and can contact your EOS test device using commands from [NAPALM CLI Documentation](https://napalm.readthedocs.io/en/latest/cli.html)

```terminal
$ source venv/bin/activate
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

# Simple Napalm script to load config

Create a new python script called `load_replace.py` with these contents<!-- TDOO: add link to original source ([original source]()) -->:

```python
# Sample script to demonstrate loading a config for a device.
#
# Note: this script is as simple as possible: it assumes that you have
# followed the lab setup in the quickstart tutorial, and so hardcodes
# the device IP and password.  You should also have the
# 'new_good.conf' configuration saved to disk.
from __future__ import print_function

import napalm
import sys
import os


def main(config_file):
    """Load a config for the device."""

    if not (os.path.exists(config_file) and os.path.isfile(config_file)):
        msg = "Missing or invalid config file {0}".format(config_file)
        raise ValueError(msg)

    print("Loading config file {0}.".format(config_file))

    # Use the appropriate network driver to connect to the device:
    driver = napalm.get_network_driver("eos")

    # Connect:
    device = driver(
        hostname="127.0.0.1",
        username="vagrant",
        password="vagrant",
        optional_args={"port": 12443},
    )

    print("Opening ...")
    device.open()

    print("Loading replacement candidate ...")
    device.load_replace_candidate(filename=config_file)

    # Note that the changes have not been applied yet. Before applying
    # the configuration you can check the changes:
    print("\nDiff:")
    print(device.compare_config())

    # You can commit or discard the candidate changes.
    try:
        choice = raw_input("\nWould you like to commit these changes? [yN]: ")
    except NameError:
        choice = input("\nWould you like to commit these changes? [yN]: ")
    if choice == "y":
        print("Committing ...")
        device.commit_config()
    else:
        print("Discarding ...")
        device.discard_config()

    # close the session with the device.
    device.close()
    print("Done.")


if __name__ == "__main__":
    if len(sys.argv) < 2:
        print('Please supply the full path to "new_good.conf"')
        sys.exit(1)
    config_file = sys.argv[1]
    main(config_file)
```

And also create a simple EOS config file called `new-hostname.conf` with these contents:

```
transceiver qsfp default-mode 4x10G

hostname set-with-load-replace-script

spanning-tree mode mstp

aaa authorization exec default local

no aaa root

username admin privilege 15 role network-admin secret 5 $1$RT/92Zg9$J8wD1qPAdQBcOhv4fefyt.
username vagrant privilege 15 role network-admin secret 5 $1$Lw2STh4k$bPEDVVTY2e7lf.vNlnNEO0

interface Management1
   ip address 10.0.2.15/24

no ip routing

management api http-commands
   no shutdown

end
```

In order to use the `load_replace.py` script to apply this config, let's activate our Python virtualenv:

```terminal
$ source venv/bin/activate
```

This install command will produce a lot of output as it downloads and installs the napalm package.

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
