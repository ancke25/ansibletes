
# Introduction :


Ce document technique décrit la configuration d'un script Vagrant permettant de créer trois machines virtuelles CentOS - une machine maître et deux machines clientes - et de les connecter sur un réseau privé avec une plage d'adresses IP 192.168.56.X. qui peut être changer a votre choix  Ce document détaille la configuration requise et les étapes à suivre pour mettre en place les machines virtuelles via Vagrant, ainsi que les spécifications des adresses IP et les commandes SSH pour la connexion.

# Configuration Vagrant :

Le fichier Vagrantfile contient la configuration de notre environnement de développement. Voici son contenu :
     ###################################################################################

                 # ############################### #####################################
                 #    This is a vagrant script to run a centos VM                      #
                 #    with one master and two node the default range IP is 192.168.56.  #
                 #     you can change this  range  if you want                           #
                 ########################################################################

ip_master = "192.168.56.2"

ip_machine1 = "192.168.56.10"

ip_machine2 = "192.168.56.11"

Vagrant.configure("2") do |config|

     config.vm.define "node1" do |node1|
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
               vb.memory = "2048"
               vb.cpus = "1"
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
              vb.memory = "2048"
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
                vb.memory = "2048"
                vb.cpus = "2"
                vb.name = "server"
        end  
      end
      end

      ############################################################################################

![image](https://user-images.githubusercontent.com/131200275/236227221-c3a8c387-c667-4b49-b56c-0c94bcdae6a3.png)


# Pré-requis :

Afin d'utiliser ce script Vagrant, vous devrez installer les éléments suivants :
·	Vagrant
·	VirtualBox

Configuration :

Le script Vagrant est configuré avec les adresses IP suivantes :

IP de la machine maître : 192.168.56.2

IP de la machine nodale 1 : 192.168.56.10

IP de la machine nodale 2 : 192.168.56.11

Les spécifications des machines virtuelles sont les suivantes :

Machine nodale 1 : 2048MB de RAM et 1 CPU.

Machine nodale 2 : 2048MB de RAM et 1 CPU.

Machine maître : 2048MB de RAM et 2 CPU.

Chaque machine virtuelle est configurée avec une image CentOS/7.
Le script Vagrant installe également les paquets nécessaires pour les outils de gestion à distance, tels que SSH, Ansible, Python3 et Python-pip.

La commande "sshpass" est utilisée pour copier la clé SSH vers les machines client.
Le script configure également un disque de 90 Go pour la machine maître il faut pense à installer le plugin suivant si pendant l’installation vous aviez un souci sur la machine maitre

 
  vagrant plugin install vagrant-disksize
 
# Instructions d'utilisation :

Assurez-vous que Vagrant et VirtualBox sont installés sur votre système.
Copiez le script Vagrant ci-dessus et sauvegardez-le dans un fichier nommé "Vagrantfile".
Ouvrez un terminal et accédez au répertoire contenant le fichier "Vagrantfile".
Exécutez la commande suivante pour démarrer les machines virtuelles :

 Vagrant up 
<<<<<<< HEAD
# NB
Après l'installation vous pouvais vous connecter a une des machines en utilisant la commande 
ssh root@ip_machine1, ssh vagrant@ip_machine1 mot de passe 'vagrant'
soit en utilisant la connexion directe à une des machines comme illustrer suivant. 
vagrant ssh node1 ceci est possible si vous restez dans le répertoire où se trouve le fichier vagranfile
=======

# NB
Après l'installation vous pouvais vous connecter a une des machines en utilisant la commande 
  ssh root@ip_machine1, ssh vagrant@ip_machine1 mot de passe 'vagrant'  
soit en utilisant la connexion directe à une des machines comme illustrer suivant.
  vagrant ssh node1    ceci est possible si vous restez dans le répertoire où se trouve le fichier vagranfile  
>>>>>>> e25ab0655e5e9493ca2547be0c7b98d34f824a42

# Conclusion :

La configuration de Vagrant décrite dans ce document vous permettra de déployer deux machines virtuelles avec des adresses IP spécifiques. Vous pourrez vous connecter à ces machines en utilisant les commandes SSH fournies. N'hésitez pas contacter support-tech@momentum-tech.ca si vous avez des questions supplémentaires ou des problèmes lors de la configuration de l'environnement de développement.
