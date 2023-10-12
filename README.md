# MINI-PROJET-DOCKER-FROM-EASYTRAINING :
This mini project of docker was carried during my participation of a bootcamp (Session 15 From September to November 2023)  from Eazytraining in order to demonstrate my ability to manage a docker infrastructure . The information about the project can be found here [https://github.com/diranetafen/student-list](https://github.com/diranetafen/student-list)


## Context and objectives 
I'm working as a devops engineer for Pozos company located in France  and I have been asked to build a POC and show how docker can help  and how much this technology is efficient. The different themes will be evaluated during my work :
- improve an existed application deployment process
- versioning your infrastructure release
- address best practice when implementing docker infrastructure
- Infrastructure As Code


### Application

The application that you will be working on is named "*student_list*", this application is very basic and enables POZOS to show the list of the student with their age.

student_list has two modules:

- the first module is a REST API (with basic authentication needed) who send the desire list of the student based on JSON file
- The second module is a web app written in HTML + PHP who enable end-user to get a list of students

Your work is to build one container for each module an make them interact with each other

Application source code can be found [here](https://github.com/diranetafen/student-list.git "here")


### Infrastructure :
For this infrastruce , we will only need one single machine with a docker installed on it . The build and the deployment will be made on it . We will use the centos 7.6 OS because it's the POZOS's will, therefore all it's employees use it .  
We will run the centos's machine though Vagrant in order to have an isoproduction environment and here's the prerequistes :

- A local machine running Linux, Windows or MacOS
- A network connection
- Installation of Vagrant and VirtualBox
- Know how to use Powershell
- Have a text editor (Notepad++, VSCode)
- Creating a virtual machine with OS:Centos 7.6 from a vagrantfile named dockerm
- System provisioning must have at least the following packages installed: git, docker, docker-compose

These are the following steps :
# From my localhost , I do this:
   1. Creating a folder containing my Vagrant File
```bash
PS C:\Users\a883206\mini-projet-docker> 
```
```bash
PS C:\Users\a883206\mini-projet-docker> mkdir dockerm


    Répertoire : C:\Users\a883206\mini-projet-docker


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        12/10/2023     11:29                dockerm
```
2. Initialize the vagrant file with -m option (option -m = configuration minimum)
```bash
PS C:\Users\a883206\mini-projet-docker\dockerm>
```
```bash 
vagrant init -m

3. Copy/Paste the vagrantfile I created containing all the information needed
```bash
 # -*- mode: ruby -*-
# vi: set ft=ruby :


RAM = 2048
CPU = 2
IP = "10.0.0.10"

# Check Vagrant plugins
# If you want to ensure that vagrant-winnfsd is installed and enabled every time you run your vagrant box, just add the following to your vagrant file, just before the code block
if Vagrant::Util::Platform.windows? then
  unless Vagrant.has_plugin?("vagrant-winnfsd")
    raise  Vagrant::Errors::VagrantError.new, "vagrant-winnfsd plugin is missing. Please install it using 'vagrant plugin install vagrant-winnfsd' and rerun 'vagrant up'"
  end
end
# If you want to ensure that vagrant-vbguest is installed and enabled every time you run your vagrant box, just add the following to your vagrant file, just before the code block
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

4. Installation of Vagrant's plugins
```bash

PS C:\Users\a883206\mini-projet-docker\dockerm> 
```
```bash
vagrant plugin install vagrant-winnfsd
```
```bash
PS C:\Users\a883206\mini-projet-docker\dockerm> 
```
```bash
vagrant plugin install vagrant-vbguest
```
```bash
PS C:\Users\a883206\mini-projet-docker\dockerm> 
```
```bash
vagrant plugin install vagrant-share
```	
5. Verifying Vagrantfile
PS C:\Users\a883206\mini-projet-docker\dockerm> 
```
```bash
  
PS C:\Users\a883206\mini-projet-docker\dockerm> vagrant validate
Vagrantfile validated successfully.
PS C:\Users\a883206\mini-projet-docker\dockerm> 

```
6. Creating the virtual machine"mpdocker"
```bash
PS C:\Users\a883206\mini-projet-docker\dockerm>
vagrant up
7. Connecting via ssh from the prompt 
```bash
PS C:\Users\a883206\mini-projet-docker\dockerm>
```
```bash
vagrant ssh mpdocker
```
9. Verifying after set up
```bash
cat /etc/redhat-release
```
```bash
CentOS Linux release 7.6.1810 (Core)
```
```bash
docker -v
```
```bash
Docker version 24.0.6, build ed223bc
```
```bash
docker-compose -v
```
```bash
docker-compose version 1.21.0, build 5920eb0
```
```bash
ip a
```
```bash
...
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:81:b4:92 brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.10/24 brd 10.0.0.255 scope global noprefixroute eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe81:b492/64 scope link
       valid_lft forever preferred_lft forever

...
```
## Retrieving the code from Git
1. From mpdocker (Host) we gitclone the code from the root like this "/"
```bash
cd /
```
```bash
sudo git clone https://github.com/diranetafen/student-list.git
```
2. Checking code location
```bash
ls -alh /student-list/
```
Output:
```bash
[vagrant@mpdocker ~]$ ls -alh student-list/
total 8.0K
drwxr-xr-x. 5 root    root      94 Oct 12 10:38 .
drwx------. 4 vagrant vagrant   94 Oct 12 10:38 ..
-rw-r--r--. 1 root    root       0 Oct 12 10:38 docker-compose.yml
drwxr-xr-x. 8 root    root     163 Oct 12 10:38 .git
-rw-r--r--. 1 root    root    7.0K Oct 12 10:38 README.md
drwxr-xr-x. 2 root    root      70 Oct 12 10:38 simple_api        
drwxr-xr-x. 2 root    root      23 Oct 12 10:38 website
[vagrant@mpdocker ~]$
```
#

## Creating the Dockerfile 
We are going to create the Dockerfile according to the information given by POZOS in order to buy the API container , see the source code [https://github.com/diranetafen/student-list](https://github.com/diranetafen/student-list)
1. Edition of Dockerfile
```bash
sudo vi /student-list/simple_api/Dockerfile
```
NB: La touche I c'est la vie ! :)
```bash
FROM python:2.7-stretch
LABEL maintainer=Christ-Bagamboula mail=bagam_fleury@hotmail.fr  
# Dependencies for the system
RUN DEBIAN_FRONTEND=noninteractive apt-get update -y && apt-get install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y
# Installation Flask
RUN pip install flask==1.1.2 flask_httpauth==4.1.0 flask_simpleldap python-dotenv==0.14.0
# Configure network for API
EXPOSE 5000
# Configuration volume /data
VOLUME [ "/data" ]
# Copy the script student_age.py to /
COPY student_age.py /
# Run the server python and start api
CMD [ "python", "./student_age.py" ]
```
##
## Updating Index File 
1.  Updating Index File
```bash
We need to update the index file before carrying out the build of the image because we need to update the api name and port in order to fit the deployment  . This is line which should be update : $url = 'http://<api_ip_or_name:port>/pozos/api/v1.0/get_student_ages';
[vagrant@mpdocker student-list]$ sudo sed -i 's/<api_ip_or_name:port>/student-list-api:5000/g' ./website/index.php
[vagrant@mpdocker student-list]$ cat ./website/index.php

![alt text](https://github.com/christ242/mini-projet-docker-eazytraining/assets/60726494/3d9c67e5-7215-41f4-89b8-4af9056e6a6f)

```
#
## Creating Bridge Network 
[vagrant@mpdocker student-list]$ sudo docker network create --driver=bridge student-list-network
e4ab897bdc88d361b0d547fb29e171ca50ccf39babab9580d6c2c4845289aab7
[vagrant@mpdocker student-list]$ sudo docker network ls
NETWORK ID     NAME                   DRIVER    SCOPE
ad96ba2f34a1   bridge                 bridge    local
6a90b08eee20   host                   host      local
0f49e3367c81   none                   null      local
e4ab897bdc88   student-list-network   bridge    local
[vagrant@mpdocker student-list]$ 

#
## Building and Running Image
1. Creating the image for the api's container
```bash
cd /student-list/simple_api/
```
```bash
sudo docker build -t student-list_api:v1.0 .
```
2. Verification de l'image
```bash
sudo docker images
```
```bash
REPOSITORY         TAG           IMAGE ID       CREATED         SIZE
student-list_api   v1.0          4c056fe48362   6 seconds ago   1.13GB
python             2.7-stretch   e71fc5c0fcb1   18 months ago   928MB
```






## Infrastructure As Code (5 points)

After testing your API image, you need to put all together and deploy it, using docker-compose.

The ***docker-compose.yml*** file will deploy two services :

- website: the end-user interface with the following characteristics
   - image: php:apache
   - environment: you will provide the USERNAME and PASSWORD to enable the web app to access the API through authentication
   - volumes: to avoid php:apache image run with the default website, we will bind the website given by POZOS to use. You must have something like
`./website:/var/www/html`
   - depend on: you need to make sure that the API will start first before the website
   - port: do not forget to expose the port
- API: the image builded before should be used with the following specification
   - image: the name of the image builded previously
   - volumes: You will mount student_age.json file in /data/student_age.json
   - port: don't forget to expose the port

Delete your previous created container

Run your docker-compose.yml

Finally, reach your website and click on the bouton "List Student"

**If the list of the student appears, you are successfully dockerizing the POZOS application! Congratulation (make a screenshot)**

## Docker Registry (4 points)

POZOS need you to deploy a private registry and store the built images

So you need to deploy :

- a docker [registry](https://docs.docker.com/registry/ "registry")
- a web [interface](https://hub.docker.com/r/joxit/docker-registry-ui/ "interface") to see the pushed image as a container

Or you can use [Portus](http://port.us.org/ "Portus") to run both

Don't forget to push your image on your private registry and show them in your delivery.

## Delivery (4 points)

Your delivery must be zip named firstname.zip (replace firstname by your own) that contain:

- A doc or PDF file with your screenshots and explanations.
- Configuration files used to realize the graded exercise (docker-compose.yml and Dockerfile).

Your delivery will be evaluated on:

- Explanations quality
- Screenshots quality (relevance, visibility)
- Presentation quality

Send your delivery at ***eazytrainingfr@gmail.com*** and we will provide you the link of the solution.

![good luck](https://user-images.githubusercontent.com/18481009/84582398-cad38100-adeb-11ea-95e3-2a9d4c0d5437.gif)
