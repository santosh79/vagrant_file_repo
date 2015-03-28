# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/edlab14"
  config.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/utopic/20150219.1/utopic-server-cloudimg-i386-vagrant-disk1.box"
  config.vm.provider "virtualbox" do |v|
    v.customize ["modifyvm", :id, "--memory", 384]
  end

  # config.vm.network :bridged
  config.vm.synced_folder ".", "/home/vagrant/mschool"

  $required_packages = %W{git g++ make python-setuptools curl build-essential}

  $forwarded_ports = [8000]
  setup_apache_port config, 8080
  setup_forwarded_ports config, $forwarded_ports
  install_default_packages config, $required_packages
  $global_custom_install_scripts = setup_global_custom_install_scripts
  install_custom_packages config, $global_custom_install_scripts
end

def setup_apache_port(config, forward_port)
# This forward all calls to forward_port on the host machine
# (i.e. our mac) to port 80 on the guest machine (i.e. the vm)
  config.vm.network :forwarded_port, host: forward_port, guest: 80
end

def setup_forwarded_ports(config, forwarded_ports)
  forwarded_ports.each do |forward_port|
    config.vm.network :forwarded_port, host: forward_port, guest: forward_port
  end
end

def install_default_packages(config, packages_to_install)
  config.vm.provision :shell, :inline => "cd && sudo apt-get update"
  packages_to_install.each do |pkg|
    script = <<SCRIPT
    cd
    sudo apt-get -y install #{pkg}
SCRIPT
    config.vm.provision :shell, :inline => script
  end
end

def install_custom_packages(config, custom_package_install_scripts)
  custom_package_install_scripts.each do |install_script|
    config.vm.provision :shell, :inline => install_script
  end
end

def setup_global_custom_install_scripts
  node_install_script = <<SCRIPT
    cd
    curl -sL https://deb.nodesource.com/setup | sudo bash -
    sudo apt-get install -y nodejs
SCRIPT

  mysql_script = <<SCRIPT
      sudo debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password password root'
      sudo debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password_again password root'
      sudo apt-get update
      sudo apt-get -y install mysql-server-5.5
SCRIPT
  php_stuff = <<SCRIPT
      sudo add-apt-repository -y ppa:ondrej/php5-5.6
      sudo apt-get update
      sudo apt-get install -y php5
SCRIPT
  pip_install_script = <<SCRIPT
      easy_install pip
SCRIPT
  solr_install_script = <<SCRIPT
      sudo apt-get -y install openjdk-7-jdk
      mkdir /usr/java
      ln -s /usr/lib/jvm/java-7-openjdk-amd64 /usr/java/default
      sudo apt-get -y install solr-tomcat
SCRIPT
  php_stuff = <<SCRIPT
      sudo add-apt-repository -y ppa:ondrej/php5-5.6
      sudo apt-get update
      sudo apt-get install -y php5
SCRIPT
  neo_install_script = <<SCRIPT
      cd
      sudo apt-get install -y graphviz maven make
      wget https://github.com/neo4j/neo4j/archive/master.zip
      unzip master.zip 
      rm master.zip 
      cd neo4j-master/
      mvn clean install
SCRIPT
  meteor_install_script = <<SCRIPT
    cd
    sudo npm install -g meteorite
    curl https://install.meteor.com/ | sh
SCRIPT

  elixir_install_script = <<SCRIPT
    cd
    wget http://packages.erlang-solutions.com/erlang-solutions_1.0_all.deb
    sudo dpkg -i erlang-solutions_1.0_all.deb
    sudo apt-get update
    sudo apt-get -y install elixir
SCRIPT

  [node_install_script, mysql_script, pip_install_script, solr_install_script, neo_install_script, meteor_install_script, elixir_install_script]
end

