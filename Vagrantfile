# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV['VAGRANT_DEFAULT_PROVIDER'] = 'virtualbox'

VAGRANT_BOX               = "ubuntu/jammy64"
VAGRANT_BOX_VERSION       = "12.20240503.1"
VAGRANT_BOX_TIMEOUT       = 600
VAGRANT_CLUSTER_NAME      = "Development Container Engine Podman REST API 01"
VM_TOTAL_CPU              = 8
VM_TOTAL_MEMORY           = 8192
VM_NAME                   = "DEVPODMAN-01-ENV01"
VM_IP_ADDRESS             = "192.168.1.10"


Vagrant.configure("2") do |config|

  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box           = VAGRANT_BOX
  config.vm.boot_timeout  = VAGRANT_BOX_TIMEOUT

  ## Configuration To Disable automatic box update checking
  config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port

  ## Configuration for forwarded port without mapping
  # config.vm.network "forwarded_port", guest: 80, host: 80
  # config.vm.network "forwarded_port", guest: 443, host: 443
  # config.vm.network "forwarded_port", guest: 8080, host: 8080
  # config.vm.network "forwarded_port", guest: 10001, host: 10001

  ## Configuration for a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  config.vm.network "forwarded_port", guest: 80, host: 80, host_ip: VM_IP_ADDRESS
  config.vm.network "forwarded_port", guest: 443, host: 443, host_ip: VM_IP_ADDRESS
  config.vm.network "forwarded_port", guest: 8080, host: 8080, host_ip: VM_IP_ADDRESS
  config.vm.network "forwarded_port", guest: 8888, host: 8888, host_ip: VM_IP_ADDRESS
  config.vm.network "forwarded_port", guest: 10001, host: 10001, host_ip: VM_IP_ADDRESS

  ## Configuration for a private network, which allows host-only access to the machine using a specific IP.
  config.vm.network "private_network", ip: VM_IP_ADDRESS

  ## Configuration for Mount Volume / Share an additional folder to the guest VM. 
  # The first argument is the path on the host to the actual folder. 
  # The second argument is the path on the guest to mount the folder. 
  # And the optional third argument is a set of non-required options.
  config.vm.synced_folder ".", "/vagrant"

  ## Configuration For VM Name
  config.vm.define "devpodman-01" do |lb|
  end

  ## Configuration For VM Specifications
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = false
  
    # Customize the amount of memory on the VM:
    vb.name   = VM_NAME
    vb.cpus   = VM_TOTAL_CPU
    vb.memory = VM_TOTAL_MEMORY
    vb.customize ["modifyvm", :id, "--groups", ("/" + VAGRANT_CLUSTER_NAME )]
  end

  ## Configuration For Ansible from the Vagrant VM
  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "ansible-playbook.yml"
    ansible.galaxy_role_file = "ansible-requirements.yml"
    ansible.galaxy_command = "ansible-galaxy collection install -r %{role_file}"
  end
  # try to tune boot timeout value
  # config.vm.boot_timeout = 600

end
