Vagrant.configure("2") do |config|
  config.vm.box = "generic/rhel8"
  config.vm.hostname = "ntp.edu.tentixo.com"
  config.vm.define "ntp.edu.tentixo.com"
  config.vm.network "private_network", ip: "192.168.1.100"
  config.vm.box_check_update = false
  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 2048
    vb.cpus = 2
  end
  
 config.vm.provision "ansible" do |ansible|
   ansible.playbook = "playbook.yml"
   ansible.inventory_path = "inventory"
  end
  
end
