---
title: Lab Setup
layout: page
---

# Clone Lab Files

To get the files used in these tutorials, clone the project repository:

```terminal
$ git clone https://github.com/nacn-workshop/network-automation-intro-lab-2019.git
```

This will create a directory called `network-automation-intro-lab-2019`. *The rest of the lab instructions will assume you are starting in this directory.*

# Install VirtualBox

VirtualBox is a cross-platform virtualization tool. This will allow us to run virtual servers and devices in a private, virtual lab.

- [Download and install VirtualBox](https://www.virtualbox.org/)

Once installed, you should be able to check the version from the command line:

```terminal
$ vboxmanage --version
6.0.8r130520
```

There is also a GUI that installs with VirtualBox for managing VMs, but we won't be using that in this lab.

<!-- TODO: Add link to VirtualBox documentation -->

# Install Vagrant

Vagrant is a tool that simplifies the process of managing development environments. It will give us a straightforward way to use VirtualBox consistently and easily.

[Download and install Vagrant](https://www.vagrantup.com/downloads.html)

Once installed, you should be able to check the version from the command line:

```terminal
$ vagrant version
Installed Version: 2.2.4
Latest Version: 2.2.4

You're running an up-to-date version of Vagrant!
```

<!-- TODO: Add link to Vagrant documentation -->

# Create a Python virtual environment

Next we'll create a virtual environment for python that we'll run directly on our host machine.

```terminal
$ python3 -m venv venv
$ ls venv
bin        include    lib        pyvenv.cfg
```

Whenever you want to use this environment, you'll need to activate it first. But before you activate, take a look at the existing locations of your python command:

```terminal
$ which python3
/usr/local/bin/python3
```

The path you see may be different, but will probably point to a system location.

Now activate the virtual environemnt:


```terminal
$ source venv/bin/activate
(venv) $
```

Note that your prompt may have changed to add an indicator that you are in an active python virtualenv.

Check the location of your python command again:

```terminal
(venv) $ which python3
/Users/username/network-automation-intro-lab/venv/bin/python3
```

Again what you see will be different, but notice that the path is now pointing to a python3 command in your virtualenv directory, not a system path.

To get out of the virtual environemnt, use the `deactivate` alias (which is defined in the environemnt):

```terminal
(venv) $ deactivate
$
```

# Install NAPALM

```terminal
$ source venv/bin/activate
(venv) $ pip install napalm
```

This command will produce a lot of output while it installs the package. If it completes successfully, it should finish with this line:

```terminal
Successfully installed MarkupSafe-1.1.1 asn1crypto-0.24.0 bcrypt-3.1.6 certifi-2019.3.9 cffi-1.12.3 chardet-3.0.4 cryptography-2.6.1 future-0.17.1 idna-2.8 jinja2-2.10.1 junos-eznc-2.2.1 lxml-4.3.3 napalm-2.4.0 ncclient-0.6.6 netaddr-0.7.19 netmiko-2.3.3 nxapi-plumbing-0.5.2 paramiko-2.4.2 pyIOSXR-0.53 pyYAML-5.1 pyasn1-0.4.5 pycparser-2.19 pyeapi-0.8.2 pynacl-1.3.0 pyserial-3.4 requests-2.22.0 scp-0.13.2 setuptools-41.0.1 six-1.12.0 textfsm-0.4.1 urllib3-1.25.3
```

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

<!-- TODO: Add link to virtualenv documentation -->
