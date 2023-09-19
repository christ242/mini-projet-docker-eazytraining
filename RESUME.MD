# 1. Création de la machine virtuelle 
Notre machine virtuelle se nommera "dockerm" elle sera le host qui permettra de faire fonctionner nos conteneurs.
Prerequis:
Une machine local sous Linux, Windows, MacOS
Avoir une connexion reseau
Installation de Vagrant et VirtualBox
Savoir utiliser Powershell
Avoir un editeur de texte (Notepad++, VSCode)
Creation d'une virtual machine avec un OS:Centos 7.6 depuis un vagrantfile
Le provisionement du syteme doit avoir au minimun ses paquets d'installer: git, docker, docker-compose

# 2. Depuis votre machine local (Host):
a.	Création d'un dossier qui va contenir mon vagrantfile
PS C:\Users\a883206\
mkdir C:\Users\a883206\student-list-christ-bagamboula\dockerm
b.	Initialisation d'un vagrantfile (option -m = configuration minimum)
PS C:\Users\a883206\student-list-christ-bagamboula
vagrant init -m

# 3.Copier/Coller ce vagrantfile

/# -*- mode: ruby -*-
/# vi: set ft=ruby :

RAM = 2048
CPU = 2
IP = "10.0.0.10"

\# Check Vagrant plugins
\# If you want to ensure that vagrant-winnfsd is installed and enabled every time you run your vagrant box, just add the following to your vagrant file, just before the code block
if Vagrant::Util::Platform.windows? then
  unless Vagrant.has_plugin?("vagrant-winnfsd")
    raise  Vagrant::Errors::VagrantError.new, "vagrant-winnfsd plugin is missing. Please install it using 'vagrant plugin install vagrant-winnfsd' and rerun 'vagrant up'"
  end
end
\# If you want to ensure that vagrant-vbguest is installed and enabled every time you run your vagrant box, just add the following to your vagrant file, just before the code block
if Vagrant::Util::Platform.windows? then
  unless Vagrant.has_plugin?("vagrant-vbguest")
    raise  Vagrant::Errors::VagrantError.new, "vagrant-vbguest plugin is missing. Please install it using 'vagrant plugin install vagrant-vbguest' and rerun 'vagrant up'"
  end
end
# If you want to ensure that vagrant-share is installed and enabled every time you run your vagrant box, just add the following to your vagrant file, just before the code block
if Vagrant::Util::Platform.windows? then
  unless Vagrant.has_plugin?("vagrant-share")
    raise  Vagrant::Errors::VagrantError.new, "vagrant-share plugin is missing. Please install it using 'vagrant plugin install vagrant-share' and rerun 'vagrant up'"
  end
end
# SCRIPT for provisioning the magic system :)
$no_update_system = <<SCRIPT
echo Stop Update System...
sudo systemctl stop packagekit.service && sudo systemctl disable packagekit.service
echo "For this virtual machine, no update will be made the version Centos 7.6 will remain fixed"
SCRIPT

$install_git = <<SCRIPT
sudo yum install -y yum-utils
echo Install and configure git...
sudo yum install git -y
git config --global user.name "christ242"
git config --global user.email "bagam_fleury@hotmail.fr"
git config --global core.editor vi
sleep 5
SCRIPT

$install_docker_script = <<SCRIPT
echo Installing Docker...
curl -sSL https://get.docker.com/ | sh
sudo usermod -aG docker $USER
sudo systemctl enable docker
sudo systemctl start docker
echo Docker has been installed...
echo Installing Docker-compose...
sudo mkdir -p /opt/bin/
sudo curl -L https://github.com/docker/compose/releases/download/1.21.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
echo Docker-Compose has been installed...
echo "For this Stack, you will use $(ip -f inet addr show eth1 | sed -En -e 's/.*inet ([0-9.]+).*/\1/p') IP Address"
sleep 5
echo "The Virtual machine Centos 7.6 has been installed >>> Last reboot !!!"
sudo reboot
SCRIPT

# The vagrant config for virtual machine named dockerm
Vagrant.configure('2') do |config|
  config.vm.define :dockerm, primary: true  do |dockerm|
    dockerm.vm.box = "komlevv/centos-7.6"
    dockerm.vm.box_version = "1.0.0"
    dockerm.vbguest.auto_update = false
    dockerm.vm.network :private_network, ip: IP
    dockerm.vm.hostname = "mpdocker"
    dockerm.vm.synced_folder ".", "/vagrant"
    dockerm.vm.provision "shell", inline: $install_git, privileged: true
    dockerm.vm.provision "shell", inline: $install_docker_script, privileged: true
    dockerm.vm.provider "virtualbox" do |vb|
      vb.name = "dockerm"
      vb.memory = RAM
  	  vb.cpus = CPU
    end
  end
end

4. Installation des plugins vagrant

PS C:\Users\a883206\student-list-christ-bagamboula\dockerm
vagrant plugin install vagrant-winnfsd
PS C:\Users\a883206\student-list-christ-bagamboula\dockerm
vagrant plugin install vagrant-vbguest
PS C:\Users\a883206\student-list-christ-bagamboula\dockerm
vagrant plugin install vagrant-share
Verification de son vagrantfile
PS C:\Users\a883206\student-list-christ-bagamboula\dockerm
Output:

vagrant validate
Vagrantfile validated successfully.
Creation de la machine virtuelle "mpdocker"
PS C:\Users\a883206\student-list-christ-bagamboula\dockerm
vagrant up
Connexion en ssh depuis votre prompt
PS C:\Users\a883206\student-list-christ-bagamboula\dockerm
vagrant ssh dockerm
Verification apres installation
cat /etc/redhat-release
CentOS Linux release 7.6.1810 (Core)
docker -v
Docker version 20.10.10, build b485636
docker-compose -v
docker-compose version 1.29.2, build 5becea4c
ip a
...
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:24:c7:d8 brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.200/24 brd 10.0.0.255 scope global noprefixroute eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe24:c7d8/64 scope link
       valid_lft forever preferred_lft forever
...

. RECUPERATION DU CODE GIT


Depuis dockerm(Host) copier le code de l' API a la racine "/"
cd /
sudo git clone https://github.com/diranetafen/student-list.git
Verification de l'emplacement du code
ls -alh /student-list/
Output:

total 48K
drwxr-xr-x.  8 root root  193 Nov 11 13:52 .
dr-xr-xr-x. 19 root root  259 Nov 11 13:52 ..
-rw-r--r--.  1 root root  800 Nov 11 13:52 docker-compose.yml
drwxr-xr-x.  8 root root  163 Nov 11 13:52 .git
-rw-r--r--.  1 root root 7.1K Nov 11 13:52 README.md
drwxr-xr-x.  2 root root   70 Nov 11 13:52 simple_api
drwxr-xr-x.  2 root root   23 Nov 11 13:52 website

.BUILD AND RUN DE L'IMAGE 

Edition du Dockerfile
sudo vi /student-list/simple_api-christ-bagamboula/Dockerfile
FROM python:2.7-buster
LABEL MAINTAINER christ BAGAMBOULA (bagam_fleury)
# Install Packages for dependencies 
RUN DEBIAN_FRONTEND=noninteractive apt-get update -y && apt-get install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y
# Flask Installation 
RUN pip install flask==1.1.2 flask_httpauth==4.1.0 flask_simpleldap python-dotenv==0.14.0
# Expose the API flask
EXPOSE 5000
# Configure Volume /data
VOLUME /data
# Copy the script student_age.py to /
COPY student_age.py /
# Run the server python and start api
CMD [ "python", "./student_age.py" ]

. Construction de l'image docker API
docker build -t api .
. Affichage des images

[vagrant@mpdocker simple_api]$ sudo docker images
REPOSITORY   TAG       IMAGE ID       CREATED             SIZE  
api          latest    ac4f4a96ce04   About an hour ago   1.14GB
[vagrant@mpdocker simple_api]$ 

. Création du conteneur api-test pour effectuer le test
[vagrant@mpdocker simple_api]$ sudo docker images
REPOSITORY   TAG       IMAGE ID       CREATED             SIZE  
api          latest    ac4f4a96ce04   About an hour ago   1.14GB
 [vagrant@mpdocker simple_api]$ sudo docker images
REPOSITORY   TAG       IMAGE ID       CREATED             SIZE  
api          latest    ac4f4a96ce04   About an hour ago   1.14GB
sudo docker container run -d -p 80:5000 --name api-test -v /home/vagrant/student-list-christ-bagamboula/simple_api/student_age.json:/data/student_age.json  api

[vagrant@mpdocker simple_api]$ sudo docker volume ls  ( Affichage du volume docker local crée)
DRIVER    VOLUME NAME
local     1e0fd7a2cfc6c735578bd2f3040f6005a6f5aee37fa67206f7fb23f1b9a08873
[vagrant@mpdocker simple_api]$ 
. Mise à jour du fichier index.php

Edition de la page index.php
sudo vi /student-list/website/index.php
Modifer les lignes suivantes
  $username = getenv('USERNAME');
  $password = getenv('PASSWORD');
  if ( empty($username) $username = 'Y29kYWMtcm8=';
  if ( empty($password) $password = 'IUMwZEBjUjA=';
  $context = stream_context_create(array(
    "http" => array(
    "header" => "Authorization: Basic " . base64_decode("$username:$password"),
  )));
Lien pour encoder ou decoder un mot de passe: Encode-Decode Base64

$url = 'http://<api_ip_or_name:port>/pozos/api/v1.0/get_student_ages';
par
$url = 'http://10.0.0.10:80/pozos/api/v1.0/get_student_ages';
Enregistrer les modifications part 1 ;)

Test de l'API

