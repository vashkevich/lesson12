# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "puppet-srv" do |srv|
	srv.vm.hostname = "puppet-srv.epam.com"
	srv.vm.box = "centos-VAGRANTSLASH-7"
	srv.vm.network "private_network", ip: "192.168.33.200"
	srv.vm.provider "virtualbox" do |server|
	  server.name = "puppet-srv"
	  server.cpus = 2
	  server.memory = 2560
	end
	
	srv.vm.provision "shell", inline: <<-SHELL
	sudo rpm -Uvh https://yum.puppetlabs.com/puppetlabs-release-pc1-el-7.noarch.rpm
	sudo yum install -y puppetserver
        echo "192.168.33.205 puppet-n1-srv.epam.com" >> /etc/hosts
	sudo systemctl restart network
 	sudo cp /vagrant/autosign.conf /etc/puppetlabs/puppet 	
	sudo systemctl start puppetserver
        sudo puppet module install puppetlabs-mysql --version 3.10.0
	sudo cp /vagrant/site.pp /etc/puppetlabs/code/environments/production/manifests
        SHELL
  end

  config.vm.define "node1" do |node1|
	node1.vm.hostname = "puppet-n1-srv.epam.com"
	node1.vm.box = "centos-VAGRANTSLASH-7"
	node1.vm.network "private_network", ip: "192.168.33.210"
	node1.vm.provider "virtualbox" do |node|
	  node.name = "node1"
	  node.cpus = 2
	  node.memory = 2048
	end
	
	node1.vm.provision "shell", inline: <<-SHELL
	sudo rpm -Uvh https://yum.puppetlabs.com/puppetlabs-release-pc1-el-7.noarch.rpm
	yum install -y puppet-agent
	echo "server = puppet-srv.epam.com" >> /etc/puppetlabs/puppet/puppet.conf
	echo "192.168.33.200 puppet-srv.epam.com" >> /etc/hosts
	sudo /opt/puppetlabs/bin/puppet resource service puppet ensure=running enable=true
	sudo /opt/puppetlabs/bin/puppet agent --test	
        SHELL
  end
end
