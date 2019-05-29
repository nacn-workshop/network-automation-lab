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
