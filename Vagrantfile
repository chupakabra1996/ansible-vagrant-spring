# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = "2"

# --- Configuration variables ---
DEFAULT_RAM = 1024
DEFAULT_CPUS = 1
DB_RAM = 2048

# network configuration
DOMAIN = ".kpfu.ru"

# Default virtualbox .box
BOX = "centos/7"

HTTP_PORT = 8080
POSTGRES_PORT = 5432


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = BOX

  # Disable the new default behavior introduced in Vagrant 1.7, to
  # ensure that all Vagrant machines will use the same SSH key pair.
  # See https://github.com/mitchellh/vagrant/issues/5005
  config.ssh.insert_key = false

  config.vm.provision "ansible" do |ansible|
    
    # enable verbose output
    ansible.verbose = "v"
    
    # playbook to run
    # ansible.playbook = "provisioning/plays/site.yml"
    ansible.playbook = "provisioning/site.yml" 

    ansible.config_file = "provisioning/.ansible.cfg"

    # define extra vars
    ansible.extra_vars = {}

    # define host variables
    ansible.host_vars = {}

    # define groups
    ansible.groups = {
      "webservers": ["webserver1", "webserver2"],
      "databases": ["neo4j"]
    }

    # ansible vault settings
    ansible.vault_password_file = 'provisioning/.vault_pass'

    # hack to be able to use multiple roles paths
    # See https://stackoverflow.com/questions/36918516/change-ansible-role-path-with-vagrant-provision
    vagrant_root = File.dirname(__FILE__) 
    ENV['ANSIBLE_ROLES_PATH'] = "#{vagrant_root}/provisioning/roles/internal:#{vagrant_root}/provisioning/roles/external"

  end

  # --- Vagrant configuration ---

  config.vm.provider "virtualbox" do |vb|
     vb.memory = DEFAULT_RAM     # specify RAM in MB
     vb.cpus   = DEFAULT_CPUS    # specify cpus number
  end

  # host №1 
  config.vm.define "webserver1" do |host|

    host.vm.hostname = 'webserver1' + DOMAIN
  	
  	host.vm.provider :virtualbox do |vb|
          vb.name = "centos7-webserver1"
    end

    host.vm.network "private_network", ip: "192.168.50.2"

    host.vm.network "forwarded_port", guest: HTTP_PORT, host: 8081
    host.vm.network :forwarded_port, guest: 22, host: 20021, id: "ssh"

  end

  # host №2
  config.vm.define "webserver2" do |host|
  	
    host.vm.hostname = 'webserver2' + DOMAIN

  	host.vm.provider :virtualbox do |vb|
        vb.name = "centos7-webserver2"
    end

    host.vm.network "private_network", ip: "192.168.50.3"

    host.vm.network "forwarded_port", guest: HTTP_PORT, host: 8082
    host.vm.network :forwarded_port, guest: 22, host: 20022, id: "ssh"

  end

  # host №3
  config.vm.define "neo4j" do |host|

    host.vm.hostname = 'neo4j' + DOMAIN

    host.vm.provider :virtualbox do |vb|
          vb.name = "centos7-neo4j"
          vb.memory = DB_RAM
    end

    host.vm.network "private_network", ip: "192.168.50.4"

    host.vm.network :forwarded_port, guest: 22, host: 20023, id: "ssh"

  end

end
