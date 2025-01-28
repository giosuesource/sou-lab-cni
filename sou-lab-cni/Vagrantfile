Vagrant.configure("2") do |config|
  config.vm.box = "almalinux/9"
  config.vm.provider "virtualbox"
  config.vm.define "soufe1" do |soufe1|
    soufe1.vm.hostname = "soufe1"
    soufe1.vm.network "private_network", ip: "192.168.56.2"
  end

  config.vm.define "soufe2" do |soufe2|
    soufe2.vm.hostname = "soufe2"
    soufe2.vm.network "private_network", ip: "192.168.56.3"

  end
 
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
  end



end


