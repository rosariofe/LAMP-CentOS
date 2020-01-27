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

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  
  #MULTI-MACHINE 
 	config.vm.define "web" do |web|	
 	web.vm.box = "centos/7"
 	web.vm.hostname = 'web'
 	web.vm.network "private_network", ip: "192.168.33.10"
	web.vm.network "forwarded_port", guest:80, host:3000
	web.vm.provider "virtualbox" do |vb|
	vb.name = "web"
	vb.memory = "512" 
  	end
 end

  config.vm.define "db" do |db|
  db.vm.box = "centos/7" 
  db.vm.hostname = 'db'
  
  db.vm.network "private_network", ip: "192.168.33.25" 	 
  db.vm.network "forwarded_port", guest:3306, host:3306 	
   db.vm.provider "virtualbox" do |v|
   v.name = "db"
   v.memory = "512"  
   end
  end
end
