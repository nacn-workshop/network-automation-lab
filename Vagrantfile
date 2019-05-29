# Arista vEOS box.
# Please change this to match your installed version
# (use `vagrant box list` to see what you have installed).
VEOS_BOX = "vEOS-lab-4.18.1F"

Vagrant.configure(2) do |config|

  config.vm.define "control" do |control|
    control.vm.box = "bento/ubuntu-18.04"
    control.vm.provider "virtualbox" do |vbox|
      vbox.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    #   # vbox.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    end

    control.vm.network :forwarded_port, guest: 22, host: 12200, id: 'ssh'
    control.vm.network "private_network", virtualbox__intnet: "link_1", ip: "10.0.1.100"
    control.vm.network "private_network", virtualbox__intnet: "link_2", ip: "10.0.2.100"
    # control.hostmanager.manage_host = true
    # control.hostmanager.manage_guest = true
    control.vm.provision "shell", inline: <<-SHELL
      set -x
      apt-get update
      # apt-get upgrade -y
      apt-get install lldpd -y
    SHELL

  end

  config.vm.define "eos" do |eos|
    eos.vm.box = VEOS_BOX
    eos.vm.network :forwarded_port, guest: 22, host: 12201, id: 'ssh'
    eos.vm.network :forwarded_port, guest: 443, host: 12443, id: 'https'
    eos.vm.network "private_network", virtualbox__intnet: "link_1", ip: "169.254.1.11", auto_config: false
    eos.vm.network "private_network", virtualbox__intnet: "link_2", ip: "169.254.1.11", auto_config: false
    # eos.hostmanager.manage_host = true
    # eos.hostmanager.manage_guest = true
  end

end
