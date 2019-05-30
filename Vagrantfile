Vagrant.configure(2) do |config|
  config.vm.define "eos" do |eos|
    eos.vm.box = "vEOS-lab-4.21.1.1F"
    eos.vm.network :forwarded_port, guest: 22, host: 12201, id: 'ssh'
    eos.vm.network :forwarded_port, guest: 443, host: 12443, id: 'https'
    eos.vm.provider "virtualbox" do |vbox|
      # This is needed to prevent the vEOS image from hanging at boot
      vbox.customize ["modifyvm", :id, "--uartmode1", "disconnected"]
    end
  end
end
