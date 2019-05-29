---
title: Experiment with Napalm
layout: page
---

The Napalm library has a very good intro tutorial, which we will borrow, and use as the foundation for other exercises in this lab.

# Napalm Tutorial Lab Setup

The instructions in this section are an abbreviated version of the [Setting up the lab](https://napalm.readthedocs.io/en/latest/tutorials/lab.html) section of the Napalm tutorial.

In order to download the image for the Arista virtual device, you'll have to create an account at <https://www.arista.com/en/user-registration>.

Once you've done that you can download the latest `vEOS-lab-<version>-virtualbox.box` image from <https://www.arista.com/en/support/software-download>.

Add it to your vagrant box list, changing the `<version>` to match the one you downloaded:

```terminal
$ vagrant box add --name vEOS-lab-<version>-virtualbox ~/Downloads/vEOS-lab-<version>-virtualbox.box
$ vagrant box list
vEOS-lab-quickstart (virtualbox, 0)
```

Start by creating a file called `Vagratfile` with these contents:

```ruby
# Vagrantfile for the quickstart tutorial

# Script configuration:
#
# Arista vEOS box.
# Please change this to match your installed version
# (use `vagrant box list` to see what you have installed).
VEOS_BOX = "vEOS-lab-4.18.1F"

Vagrant.configure(2) do |config|

  config.vm.define "base" do |base|
    # This box will be downloaded and added automatically if you don't
    # have it already.
    base.vm.box = "hashicorp/precise64"
    base.vm.network :forwarded_port, guest: 22, host: 12200, id: 'ssh'
    base.vm.network "private_network", virtualbox__intnet: "link_1", ip: "10.0.1.100"
    base.vm.network "private_network", virtualbox__intnet: "link_2", ip: "10.0.2.100"
    base.vm.provision "shell", inline: "apt-get update; apt-get install lldpd -y"
  end

  config.vm.define "eos" do |eos|
    eos.vm.box = VEOS_BOX
    eos.vm.network :forwarded_port, guest: 22, host: 12201, id: 'ssh'
    eos.vm.network :forwarded_port, guest: 443, host: 12443, id: 'https'
    eos.vm.network "private_network", virtualbox__intnet: "link_1", ip: "169.254.1.11", auto_config: false
    eos.vm.network "private_network", virtualbox__intnet: "link_2", ip: "169.254.1.11", auto_config: false
  end

end
```

Make sure you change the `VEOS_BOX` variable to match the version number of the vEOS image you downloaded.

Use the vagrant command to start these virtual machines:

```terminal
$ vagrant up
```

This command will take a while to run and will produce a lot of output. When it is finished you should have two virtual machines running in VirtualBox. Vagrant uses VirtualBox as its default provider, so if VirtualBox is installed you don't need to do anything special to tell Vagrant to use it.

You can use vagrant's `status` command to list the current status of the VMs defined in your Vagrantfile:

```terminal
$ vagrant status
Current machine states:

base                      running (virtualbox)
eos                       running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```

Now that we have a virtual lab running, let's experiment with that `eos` device.

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

And install the napalm package:

```terminal
(venv) $ python3 -m pip install napalm
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
