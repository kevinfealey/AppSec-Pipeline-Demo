# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/trusty64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = true

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network "forwarded_port", guest: 8443, host: 8443 #ThreadFix
  config.vm.network "forwarded_port", guest: 8080, host: 8080 #Jenkins
  config.vm.network "forwarded_port", guest: 50000, host: 50000 #Jenkins
  config.vm.network "forwarded_port", guest: 8081, host: 8081 #Bugzilla
  config.vm.network "forwarded_port", guest: 5900, host: 5900 #Bugzilla
  config.vm.network "forwarded_port", guest: 8082, host: 8082 #Tomcat

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
   config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
     vb.memory = "2048"
   end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
   config.vm.provision "shell", inline: <<-SHELL
   	apt-get update
	apt-get upgrade
	
	########### Docker ###########
	apt-get -y install curl
	curl -fsSL https://get.docker.com/ | sh
	usermod -aG docker vagrant
	apt-get -y install python-pip
        pip install docker-compose
	
	########### Threadfix ###########
	#Create folders for Threadfix Docker volumes
	#mkdir /vagrant/Threadfix #This is ineffective, since /Threadfix will be populated when the container starts, so creating a volume there erases Threadfix...

	#Setup Threadfix
	#### Note that we'll need to fork this project and update the dockerfile to add the Jenkins API key to the Threadfix database ####
	docker run --name threadfix -d -p 8443:8443 jmbmxer/threadfix-docker start
	
	########### Jenkins ###########
	#Create folders for Jenkins Docker volumes
	mkdir /vagrant/jenkins_home
	
	#### Note that we'll need to fork this project and update dockerfile to install Maven ####
	#### We also need to script getting past the admin password entry ####
	#### Need to add code to install Jenkins plugins ####
	#### Need to add Threadfix config ####
	#Setup Jenkins
	docker run --name jenkins -d -p 8080:8080 -p 50000:50000 -v /vagrant/jenkins_home:/var/jenkins_home jenkins
	
	########### Bugzilla ###########
	#Create folders for Bugzilla Docker volumes
	mkdir /vagrant/bugzilla
	
	#Setup Bugzilla
	#Bugzilla image on Docker Hub is broken. Until it is fixed, we build from scratch
	#docker run --name bugzilla -d -p 8081:80 bugzilla/bugzilla-dev
  	git clone https://github.com/kevinfealey/docker-bugzilla-dev.git
  	cd docker-bugzilla-dev
  	docker build --no-cache -t bugzilla/bugzilla-dev .
  	docker-compose up -d
	
	########### Tomcat ###########
	#Create folders for Bugzilla Docker volumes
	mkdir /vagrant/tomcat
	
	#Setup Tomcat
	#### Note: do we need to update the Tomcat config? ####
	docker run --name tomcat -d -p 8082:8080 tomcat:8.0
	
	########### Add w3af, Eclipse, WebGoat, SQLMap ###########
   SHELL
  #config.vm.provision "file" source: "vagrant_data\config\VNC\xstartup", destination: ".vnc/xstartup"
  #config.vm.provision :file do |file|
  #  file.source      = "vagrant_data/config/VNC/xstartup"
  #  file.destination = "/home/vagrant/.vnc/xstartup"
  #end
  #config.vm.provision :shell, path: "bootstrap.sh", run: "always"
end
