# Variables for VM: haproxy1
haproxy1_box = 'ubuntu/trusty64'
haproxy1_hostname = 'haproxy1'
haproxy1_domain = 'lab.int'
haproxy1_ip_private = '10.10.100.10'
haproxy1_ip_public = '192.168.50.100'
haproxy1_cpus = '1'
haproxy1_ram = '512'

# Variables for VM: haproxy2
haproxy2_box = 'ubuntu/trusty64'
haproxy2_hostname = 'haproxy2'
haproxy2_domain = 'lab.int'
haproxy2_ip_private = '10.10.100.9'
haproxy2_ip_public = '192.168.50.99'
haproxy2_cpus = '1'
haproxy2_ram = '512'

# Variables for VM: web1 (git.lab.int)
web1_box = 'ubuntu/trusty64'
web1_hostname = 'web1'
web1_domain = 'devops.loc'
web1_ip_private = '10.10.100.110'
web1_cpus = '1'
web1_ram = '512'

# Variables for VM: web2 (prod.lb.lab.int)
web2_box = 'ubuntu/trusty64'
web2_hostname = 'web2'
web2_domain = 'devops.loc'
web2_ip_private = '10.10.100.111'
web2_cpus = '1'
web2_ram = '512'


#START VAGRANT MULTIPLE MACHINE CONFIG

Vagrant.configure("2") do |config|

# START GLOBAL PROVISION SECTION
  config.vm.provision "shell", inline: <<-SHELL
     
     echo "UPDATE REPO"
     apt-get update
  
     #SET DNS
	 chmod 666 /etc/hosts
	 echo "10.10.100.110 web1.lab.int" >> /etc/hosts
	 echo "10.10.100.111 web2.lab.int" >> /etc/hosts
	 echo "10.10.100.10 haproxy1.lab.int" >> /etc/hosts
	 echo "10.10.100.9 haproxy2.lab.int" >> /etc/hosts
	 chmod 644 /etc/hosts

  SHELL
# FINISH GLOBAL PROVISION SECTION

# ==========================
# Start  Config for VM: haproxy1

	config.vm.define "haproxy1" do |haproxy1|
		
		haproxy1.vm.box = haproxy1_box
	  	haproxy1.vm.hostname = haproxy1_hostname +'.'+ haproxy1_domain
	  	haproxy1.vm.network "private_network", ip: haproxy1_ip_private
  		haproxy1.vm.network "public_network", bridge: ["wlo1"], ip: haproxy1_ip_public

		haproxy1.vm.provider "virtualbox" do |haproxy1|
			haproxy1.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
			haproxy1.cpus = haproxy1_cpus
			haproxy1.memory = haproxy1_ram
  		end
    
	    # START haproxy1 PROVISION SECTION
	    haproxy1.vm.provision "shell", inline: <<-SHELL
	    	
	    	apt-get install -y haproxy mtr

	    	echo "Enable HAProxy service"
			sudo cp /vagrant/haproxy /etc/default/haproxy

			echo "Put HAProxy config"
			sudo cp /vagrant/haproxy-master.cfg /etc/haproxy/haproxy.cfg

			echo "PUT LINE TO sysctl.conf"
			sudo chmod 666 /etc/sysctl.conf 
			sudo echo "net.ipv4.ip_nonlocal_bind=1" >> /etc/sysctl.conf
			sudo chmod 644 /etc/sysctl.conf
			sudo sysctl -p
	    	
	    	echo "INSTALL KEEPALIVED"
	    	sudo apt-get -y install keepalived

	    	echo "COPY CONFIG FOR KEEPALIVED"
	    	sudo cp /vagrant/keepalived-master.conf /etc/keepalived/keepalived.conf

	    	service haproxy restart
	    	service keepalived restart

	    SHELL

    # FINISH haproxy1 PROVISION SECTION

  end

# End Config for VM: haproxy1
# ==========================

# ==========================
# Start  Config for VM: haproxy2

	config.vm.define "haproxy2" do |haproxy2|
		
		haproxy2.vm.box = haproxy2_box
	  	haproxy2.vm.hostname = haproxy2_hostname +'.'+ haproxy2_domain
	  	haproxy2.vm.network "private_network", ip: haproxy2_ip_private
		haproxy2.vm.network "public_network", bridge: ["wlo1"], ip: haproxy2_ip_public

		haproxy2.vm.provider "virtualbox" do |haproxy2|
			haproxy2.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
			haproxy2.cpus = haproxy2_cpus
			haproxy2.memory = haproxy2_ram
  		end
    
	    # START haproxy2 PROVISION SECTION
	    haproxy2.vm.provision "shell", inline: <<-SHELL
	        
	        apt-get install -y haproxy mtr

	    	echo "Enable HAProxy service"
			sudo cp /vagrant/haproxy /etc/default/haproxy

			echo "Put HAProxy config"
			sudo cp /vagrant/haproxy-backup.cfg /etc/haproxy/haproxy.cfg

			echo "PUT LINE TO sysctl.conf"
			sudo chmod 666 /etc/sysctl.conf 
			sudo echo "net.ipv4.ip_nonlocal_bind=1" >> /etc/sysctl.conf
			sudo chmod 644 /etc/sysctl.conf
			sudo sysctl -p

	    	echo "INSTALL KEEPALIVED"
	    	sudo apt-get -y install keepalived

	    	echo "COPY CONFIG FOR KEEPALIVED"
	    	sudo cp /vagrant/keepalived-backup.conf /etc/keepalived/keepalived.conf

	    	service haproxy restart
	    	service keepalived restart

	    SHELL
    	# FINISH haproxy2 PROVISION SECTION

  	end

# End Config for VM: haproxy2
# ==========================

# ==========================
# Start  Config for VM: web1

	config.vm.define "web1" do |web1|
  		
		web1.vm.box = web1_box
  		web1.vm.hostname = web1_hostname +'.'+ web1_domain
  		web1.vm.network "private_network", ip: web1_ip_private
  		web1.vm.synced_folder "web1", "/var/www/web1"

  		web1.vm.provider "virtualbox" do |web1|	
     		web1.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        	web1.cpus = web1_cpus
		    web1.memory = web1_ram
		end

	    # START web PROVISION SECTION
	    web1.vm.provision "shell", inline: <<-SHELL
	    
	    	apt-get install -y apache2   
	    	sed -i 's/html/web1/g' /etc/apache2/sites-available/000-default.conf
	    	apache2ctl restart

	    SHELL
	    # FINISH web PROVISION SECTION
	
	end
# End Config for VM: web1
# ==========================

# ==========================
# Start  Config for VM: web2

	config.vm.define "web2" do |web2|
  		
		web2.vm.box = web2_box
  		web2.vm.hostname = web2_hostname +'.'+ web2_domain
  		web2.vm.network "private_network", ip: web2_ip_private
  		web2.vm.synced_folder "web2", "/var/www/web2"

  		web2.vm.provider "virtualbox" do |web2|	
     		web2.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        	web2.cpus = web2_cpus
		    web2.memory = web2_ram
		end

	    # START web PROVISION SECTION
	    web2.vm.provision "shell", inline: <<-SHELL
	    
	    	apt-get install -y apache2   
	    	sed -i 's/html/web2/g' /etc/apache2/sites-available/000-default.conf
	    	apache2ctl restart

	    SHELL
	    # FINISH web PROVISION SECTION
		
  	end
# End Config for VM: web2
# ==========================

end
# END VAGRANT MULTIPLE MACHINE CONFIG