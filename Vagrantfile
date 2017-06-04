# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = "2"

# --- Configuration variables ---
DEFAULT_RAM = 1024
DB_RAM = 2048

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
      
      "webserver1" => {
        "env" => "staging"
      },
      "webserver2" => {
        "env" => "production"
      },
      "neo4j" => {
        "env" => "neo4j"
      }
    }

    # define groups
    ansible.groups = {
      "webservers": ["webserver1", "webserver2"],
      "databases": ["neo4j"]
    }

    # define extra vars
    ansible.extra_vars = {
      "http_port" => HTTP_PORT,
      "vagrant_user" => "vagrant",
      "vagrant_group" => "vagrant"
    }

    # ansible vault settings
    ansible.vault_password_file = 'provisioning/.vault_pass.txt'
  end

  # --- Vagrant configuration ---

  # host №1 
  config.vm.provider "virtualbox" do |vb|
     vb.memory = DEFAULT_RAM     # specify RAM in MB
  end

  config.vm.define "webserver1" do |host|

    host.vm.hostname = 'webserver1' + DOMAIN
  	
  	host.vm.provider :virtualbox do |vb|
          vb.name = "centos7-webserver1"
    end

    host.vm.network "forwarded_port", guest: HTTP_PORT, host: 8081
    host.vm.network :forwarded_port, guest: 22, host: 20021, id: "ssh"

  end

  # host №2
  config.vm.define "webserver2" do |host|
  	
    host.vm.hostname = 'webserver2' + DOMAIN

  	host.vm.provider :virtualbox do |vb|
        vb.name = "centos7-webserver2"
    end

    host.vm.network "forwarded_port", guest: HTTP_PORT, host: 8082
    host.vm.network :forwarded_port, guest: 22, host: 20022, id: "ssh"

  end

  # host №3
  config.vm.define "neo4j" do |host|

    host.vm.hostname = 'neo4j' + DOMAIN

    host.vm.provider :virtualbox do |vb|
          vb.name = "centos7-neo4j"
    end

    # host.vm.network "forwarded_port", guest: HTTP_PORT, host: 8081
    host.vm.network :forwarded_port, guest: 22, host: 20023, id: "ssh"

  end
end
