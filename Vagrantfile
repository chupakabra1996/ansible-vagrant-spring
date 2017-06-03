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
HTTP_PORT = 8080


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = BOX

  # Disable the new default behavior introduced in Vagrant 1.7, to
  # ensure that all Vagrant machines will use the same SSH key pair.
  # See https://github.com/mitchellh/vagrant/issues/5005
  config.ssh.insert_key = false

  config.vm.provision "ansible" do |ansible|
    ansible.verbose = "v"                     # enable verbose output
    ansible.playbook = ANSIBLE_PLAYBOOK       # playbook to run
    
    # define host variables
    ansible.host_vars = {
      
      "production" => {
        "env" => "production"
      },
      "test" => {
        "env" => "test"
      }
    }

    # define group web with 2 hosts (test and production)
    ansible.groups = {
      "web": ["test", "production"]
    }

    # define extra vars
    ansible.extra_vars = {
      "http_port" => HTTP_PORT,
      "user" => "vagrant",
      "group" => "vagrant"
    }

    # ansible vault settings
    # ansible.ask_vault_pass = true
    ansible.vault_password_file = 'provisioning/.vault_pass.txt'
  end

  # --- Vagrant configuration ---

  config.vm.provider "virtualbox" do |vb|
     vb.memory = RAM     # specify RAM in MB
  end

  config.vm.define "production" do |prod|

    prod.vm.hostname = 'prod' + DOMAIN
  	prod.vm.network "forwarded_port", guest: HTTP_PORT, host: 8081

  	prod.vm.provider :virtualbox do |vb|
          vb.name = "centos7-production"
    end

    prod.vm.network :forwarded_port, guest: 22, host: 20021, id: "ssh"

  end


  config.vm.define "test" do |test|
  	
    test.vm.hostname = 'test' + DOMAIN
  	test.vm.network "forwarded_port", guest: HTTP_PORT, host: 8082

  	test.vm.provider :virtualbox do |vb|
        vb.name = "centos7-test"
    end

    test.vm.network :forwarded_port, guest: 22, host: 20022, id: "ssh"

  end
end
