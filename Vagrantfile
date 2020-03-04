# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Multi-Machine
  # This project uses Vagrant's multi-machine Vagrantfile
  # go google it
  
  # -- cheat by modifying the hosts resolv.conf to have these goods. 
  #    figure this out, you hack
  # trying to turn off automatic rewriting of my resolv.conf
  #config.vm.provider :virtualbox do |vb|
  #  vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"] 
  #end

  # DNS
  config.vm.define "dns" do |dns|

    dns.vm.box = "centos/8"
    dns.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
    end

    dns.vm.network "private_network", ip: "192.168.50.2"

    dns.vm.provision "shell", inline: <<-SHELL
      dnf install -y bind bind-utils
      cp -r /vagrant/files/dns/etc /
      systemctl restart named
      systemctl enable named
      cp -rv /vagrant/files/common/etc / && echo "common files copied"
    SHELL
  end
  # END DNS

  # AUTO
  config.vm.define "auto", primary: true do |auto|

    auto.vm.box = "centos/8"
    auto.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
    end

    auto.vm.network "private_network", ip: "192.168.50.10"

    auto.vm.provision "shell", inline: <<-SHELL
      dnf install -y python3
      dnf install -y python3-pip
      su -c 'pip3 install --user ansible' vagrant
      cp -rv /vagrant/files/common/etc / && echo "files copied"
      cp -rv /vagrant/files/auto/etc / && echo "auto files copied"
      su -c 'git clone https://github.com/OPI-doug/kubic.git /home/vagrant/kubic' vagrant
      su -c 'git config --global user.email "doug.hernandez@objectpartners.com"' vagrant
      su -c 'git config --global user.name "Yeti"' vagrant
      bash /home/vagrant/kubic/ansible/bin/deploy-ansible
    SHELL
  end
  # END AUTO

  # CI
  config.vm.define "ci", primary: true do |auto|

    auto.vm.box = "centos/8"
    auto.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
    end

    auto.vm.network "private_network", ip: "192.168.50.20"

    auto.vm.provision "shell", inline: <<-SHELL
      cp -rv /vagrant/files/common/etc / && echo "files copied"
      # cp -rv /vagrant/files/ci/etc / && echo "ci files copied"
    SHELL
  end
  # END CI

  # GLOBAL PROVISIONING -- Doug
  config.vm.provision "shell", inline: <<-SHELL
    # This will be done by ansible
    # dnf upgrade -y
    dnf install -y \
      bind-utils \
      git \
      jq \
      net-tools \
      vim
    useradd -s /bin/bash golem
    echo golemwerd | passwd --stdin golem
    mkdir /home/golem/.ssh
    mkdir /home/vagrant/.ssh
    cp -v /vagrant/files/common/golem/ssh-keys/* /home/golem/.ssh && echo "golem user ssh files copied"
    cp -v /vagrant/files/common/golem/ssh-keys/* /home/vagrant/.ssh && echo "vagrant user ssh files copied"
    chown -R golem:golem /home/golem/.ssh
    chmod 700 /home/golem/.ssh
    chmod 600 /home/golem/.ssh/id_rsa*
    su -c 'cp /home/golem/.ssh/id_rsa.pub /home/golem/.ssh/authorized_keys' golem
    chown -R vagrant:vagrant /home/vagrant/.ssh
    chmod 700 /home/vagrant/.ssh
    chmod 600 /home/vagrant/.ssh/id_rsa*
  SHELL

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
