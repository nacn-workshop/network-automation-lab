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

  - [Download and install Vagrant](https://www.vagrantup.com/downloads.html)

Once installed, you should be able to check the version from the command line:

```terminal
$ vagrant version
Installed Version: 2.2.4
Latest Version: 2.2.4

You're running an up-to-date version of Vagrant!
```

<!-- TODO: Add link to Vagrant documentation -->

# Download vEOS vagrant box

(The instructions in this section are an abbreviated version of the [Setting up the lab](https://napalm.readthedocs.io/en/latest/tutorials/lab.html) section of the Napalm tutorial.)

In order to download the image for the Arista virtual device, you'll have to create an account at <https://www.arista.com/en/user-registration>.

Once you've done that you can download the latest `vEOS-lab-<version>-virtualbox.box` image from <https://www.arista.com/en/support/software-download>. **The examples below use the `vEOS-lab-4.21.1.1F` image, if you download a different version, update the commands to your version.**

Add it to your vagrant box list:

```terminal
$ vagrant box add --name vEOS-lab-4.21.1.1F ~/Downloads/vEOS-lab-4.21.1.1F-virtualbox.box
==﹥ box: Box file was not detected as metadata. Adding it directly...
==﹥ box: Adding box 'vEOS-lab-4.21.1.1F' (v0) for provider:
    box: Unpacking necessary files from: file:///Users/username/Downloads/vEOS-lab-4.21.1.1F-virtualbox.box
==﹥ box: Successfully added box 'vEOS-lab-4.21.1.1F' (v0) for 'virtualbox'!
```

You can list the boxes available to vagrant:

```terminal
$ vagrant box list
```

The output of this command should include `vEOS-lab-4.21.1.1F  (virtualbox, 0)` if the previous command succeeded.

# Start the virtual device

The lab files include a `Vagrantfile` which defines exactly how to boot the virtual device. Use the vagrant command to start the virtual device:

```terminal
$ vagrant up
```

This command will take a while to run and will produce a lot of output. When it is finished you should have two virtual machines running in VirtualBox. Vagrant uses VirtualBox as its default provider, so if VirtualBox is installed you don't need to do anything special to tell Vagrant to use it.

You can use vagrant's `status` command to list the current status of the VMs defined in your Vagrantfile:

```terminal
$ vagrant status
Current machine states:

eos                       running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```

# Create a Python virtual environment

Next we'll create a virtual environment for Python that we'll run directly on our host machine.

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

Activate the virtualenv before continuing:

```terminal
$ source venv/bin/activate
(venv) $
```

<!-- TODO: Add link to virtualenv documentation -->

# Install Napalm

```terminal
(venv) $ pip install napalm
```

This command will produce a lot of output while it installs the package. If it completes successfully, it should finish with this line:

```terminal
Successfully installed MarkupSafe-1.1.1 asn1crypto-0.24.0 bcrypt-3.1.6 certifi-2019.3.9 cffi-1.12.3 chardet-3.0.4 cryptography-2.6.1 future-0.17.1 idna-2.8 jinja2-2.10.1 junos-eznc-2.2.1 lxml-4.3.3 napalm-2.4.0 ncclient-0.6.6 netaddr-0.7.19 netmiko-2.3.3 nxapi-plumbing-0.5.2 paramiko-2.4.2 pyIOSXR-0.53 pyYAML-5.1 pyasn1-0.4.5 pycparser-2.19 pyeapi-0.8.2 pynacl-1.3.0 pyserial-3.4 requests-2.22.0 scp-0.13.2 setuptools-41.0.1 six-1.12.0 textfsm-0.4.1 urllib3-1.25.3
```

# Install Ansible

```terminal
(venv) $ pip install ansible
```

Once that is done, verify that you're using the virtual environments version of Ansible by typing `which ansible`. The output of this command should reference the executable that has been installed into the virtualenv:

```terminal
(venv) $ which ansible
/Users/username/network-automation-intro-lab/venv/bin/ansible
```

# Summary

Now that VirtualBox and Vagrant are installed, and we're running a vEOS device, we have a place test configurations and commands.

The python virtualenv in `venv` now has napalm and ansible installed. This is where we'll do most of our for in this lab.
