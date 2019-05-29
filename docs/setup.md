---
title: Lab Setup
layout: page
---

We will start by setting up a virtual environment to work in using VirtualBox, Vagrant, and a virtual Arista device.

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

Now install the vagrant-hostmanager plugin:

```terminal
$ vagrant plugin install vagrant-hostmanager
Installing the 'vagrant-hostmanager' plugin. This can take a few minutes...
Fetching: vagrant-hostmanager-1.8.9.gem (100%)
Installed the plugin 'vagrant-hostmanager (1.8.9)'!
```

<!-- TODO: Add link to Vagrant documentation -->
