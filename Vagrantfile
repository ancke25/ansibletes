############################### ##################################### 
#  this is script vagrant to run tree VM centos                      #
#  with one master and two node the default range IP is 192.168.56.  #
######################################################################
ip_master = "192.168.56.2"
ip_machine1 = "192.168.56.10"
ip_machine2 = "192.168.56.11"
disks = "C:\dd"
Vagrant.configure("2") do |config|

     config.vm.define "node1" do |node1|
      node1.vm.disk :disk, size: "50GB", name: "extra_storage"
      node1.vm.provision "shell", run: "always", inline: <<-SHELL
      yum list all
      yum update
      yum install epel* -y
      ssh-keygen -t rsa -b 4096 -N '' <<<$'\n' >/dev/null
      sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/'  /etc/ssh/sshd_config
      systemctl restart sshd
      SHELL
       node1.vm.box = "centos/7"
       node1.vm.network "private_network", ip: ip_machine1
       node1.vm.hostname = "node1"
       node1.vm.provider "virtualbox" do |vb|
               vb.memory = "4048"
               vb.cpus = "2"
               vb.name = "client"
       
       end
     end
   
     config.vm.define "node2" do |node2|
     node2.vm.provision "shell", run: "always", inline: <<-SHELL
      yum list all
      yum update
      yum install epel* -y
      ssh-keygen -t rsa -b 4096 -N '' <<<$'\n' >/dev/null
      sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/'  /etc/ssh/sshd_config
      systemctl restart sshd
      SHELL
      node2.vm.box = "centos/7"
      node2.vm.network "private_network", ip: ip_machine2
      node2.vm.hostname = "node2"
      node2.vm.provider "virtualbox" do |vb|
              vb.memory = "4048"
              vb.cpus = "1"
              vb.name = "client2"
       end   
     end
     config.vm.define "master" do |master|
     
     master.vm.provision "shell", run: "always", inline: <<-SHELL
     yum list all
       yum update
        yum install epel* -y
         yum install nano htop python3 python-pip  ansible -y
          # ansible-playbook /home/vagrant/awx.yml
     SHELL
     master.vm.provision "shell", run: "always", inline: <<-SHELL
     ssh-keygen -t rsa -b  4096 -N '' <<<$'\n' >/dev/null
     sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/'  /etc/ssh/sshd_config
     systemctl restart sshd
         ssh -o "StrictHostKeyChecking no"   'root@#{ip_machine1}'  exit >  /dev/null 2>&1
         ssh -o "StrictHostKeyChecking no"   'root@#{ip_machine2}'  exit >  /dev/null 2>&1
       sshpass -p "vagrant" ssh-copy-id  root@#{ip_machine1}
       sshpass -p "vagrant" ssh-copy-id  root@#{ip_machine2}
       ssh 
     SHELL
        master.vm.box = "centos/7"
        unless Vagrant.has_plugin?("vagrant-disksize") 
        raise  Vagrant::Errors::VagrantError.new, "vagrant-disksize plugin is missing. Please install it using 'vagrant plugin install vagrant-disksize' and rerun 'vagrant up'"
        end
        master.disksize.size = '90GB'
        master.vm.network "private_network", ip: ip_master
        master.vm.hostname = "master"
        master.vm.provider "virtualbox" do |vb|
                vb.memory = "4048"
                vb.cpus = "2"
                vb.name = "server"
        end  
      end
   end
 