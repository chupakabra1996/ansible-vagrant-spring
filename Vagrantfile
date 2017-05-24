# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = "2"

# --- Configuration variables ---
RAM = 1024

# network configuration
DOMAIN = ".kpfu.ru"

# Default virtualbox .box
BOX = "centos/7"

# Ansible configuration
ANSIBLE_PLAYBOOK = "provisioning/site.yml"


Vagrant.configure("2") do |config|

  config.vm.box = BOX

  # Disable the new default behavior introduced in Vagrant 1.7, to
  # ensure that all Vagrant machines will use the same SSH key pair.
  # See https://github.com/mitchellh/vagrant/issues/5005
  config.ssh.insert_key = false

  config.vm.provision "ansible" do |ansible|
    ansible.verbose = "v"                     # enable verbose output
    ansible.playbook = ANSIBLE_PLAYBOOK       # playbook to run
  end

  # --- Vagrant configuration ---

  config.vm.provider "virtualbox" do |vb|
     vb.memory = RAM     # specify RAM in MB
  end

  config.vm.define "production" do |prod|

    prod.vm.hostname = 'prod' + DOMAIN
  	prod.vm.network "forwarded_port", guest: 8080, host: 8081

  	prod.vm.provider :virtualbox do |vb|
          vb.name = "centos7-production"
    end

    prod.vm.network :forwarded_port, guest: 22, host: 20021, id: "ssh"

  end


  config.vm.define "test" do |test|
  	
    test.vm.hostname = 'test' + DOMAIN
  	test.vm.network "forwarded_port", guest: 8080, host: 8082

  	test.vm.provider :virtualbox do |vb|
        vb.name = "centos7-test"
    end

    test.vm.network :forwarded_port, guest: 22, host: 20022, id: "ssh"

  end
end
