# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  #config.vm.box = "centos/7"
  config.vm.box = "centos-7-x86_64.box"
  config.vm.box_url = "https://github.com/vezzoni/vagrant-vboxes/releases/download/0.0.1/centos-7-x86_64.box"
  # config.vm.box_check_update = false
  # config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 15672, host: 15672
  # config.vm.network "private_network", ip: "192.168.33.10"
  # config.vm.network "public_network"
  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
  #config.vm.synced_folder "../data", "/vagrant_data"
  #config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
  #config.vm.synced_folder ".", "/vagrant", disabled: true

  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end

  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL

  #config.vm.provision "ansible" do |ansible|
  config.vm.provision "ansible_local" do |ansible|
    ansible.verbose = "v"
    ansible.playbook = "playbook.yaml"
  end

end
