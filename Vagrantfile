Vagrant.configure("2") do |config|
  config.vm.define "postgres" do |vm1|
  vm1.vm.box = "bento/ubuntu-20.04"
  vm1.vm.hostname = "postgres.server"
  vm1.vm.network "public_network", ip: "192.168.1.100", bridge: "wlp0s20f3" 

  vm1.vm.box_check_update = false
  
  vm1.vm.provider "virtualbox" do |vb|
    vb.memory = 2048
    vb.cpus = 2
  end
  
  end
  
  config.vm.define "django" do |vm2|
  vm2.vm.box = "bento/ubuntu-20.04"
  vm2.vm.hostname = "django.server"
  vm2.vm.network "public_network", ip: "192.168.1.110", bridge: "wlp0s20f3" 
  vm2.vm.box_check_update = false
  
  vm2.vm.provider "virtualbox" do |vb|
    vb.memory = 2048
    vb.cpus = 2
  end
  end
end
