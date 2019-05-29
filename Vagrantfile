# Please change this to match your installed version
# (use `vagrant box list` to see what you have installed).
VEOS_BOX = "vEOS-lab-4.21.1.1F"

Vagrant.configure(2) do |config|
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
#   config.vm.define "control" do |control|
#     control.vm.box = "ubuntu/bionic64"
#     control.hostmanager.manage_guest = true
#     control.vm.network :private_network, ip: "169.254.1.10"
#     control.vm.provision "shell", inline: <<-SHELL
#         set -x
#         apt-get update
#         apt-get -y install lldpd python-pip salt-master salt-minion ansible docker-compose git
#         # Install Netbox container
#         git clone -b master https://github.com/netbox-community/netbox-docker.git /root/netbox-docker
#         systemctl start docker
#         systemctl enable docker
#         pushd /root/netbox-docker || exit
#         docker-compose pull
#         docker-compose up -d
#         popd || exit
#         # FIXME: populate netbox static data
#         # Set up Salt
#         pip install napalm
#         #systemctl start salt-master
#         #systemctl enable salt-master
#     SHELL
#   end
  config.vm.define "eos" do |eos|
    eos.vm.box = VEOS_BOX
    eos.vm.network :forwarded_port, guest: 22, host: 12201, id: 'ssh'
    eos.vm.network :forwarded_port, guest: 443, host: 12443, id: 'https'
    eos.vm.network :private_network, virtualbox__intnet: "link_1", ip: "169.254.1.11", auto_config: false
    eos.vm.network :private_network, virtualbox__intnet: "link_2", ip: "169.254.1.12", auto_config: false
  end

end
