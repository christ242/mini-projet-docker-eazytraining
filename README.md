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
##
### Retrieving the code from Git


## Build and test (7 points)

POZOS will give you information to build the API container

- Base image

To build API image you must use "python:2.7-buster"

- Maintainer

Please don't forget to specify the maintainer information

- Add the source code

You need to copy the source code of the API in the container at the root "/" path

- Prerequisite

The API is using FLASK engine,  here is a list of the package you need to install
```
apt update -y && apt install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y
pip install flask==1.1.2 flask_httpauth==4.1.0 flask_simpleldap python-dotenv==0.14.0
```
- Persistent data (volume)

Create data folder at the root "/" where data will be stored and declare it as a volume

You will use this folder to mount student list

- API Port

To interact with this API expose 5000 port

- CMD

When container start, it must run the student_age.py (copied at step 4), so it should be something like

`CMD [ "python", "./student_age.py" ]`

Build your image and try to run it (don't forget to mount *student_age.json* file at */data/student_age.json* in the container), check logs and verify that the container is listening and is  ready to answer

Run this command to make sure that the API correctly responding (take a screenshot for delivery purpose)

`curl -u toto:python -X GET http://<host IP>:<API exposed port>/pozos/api/v1.0/get_student_ages`

**Congratulation! Now you are ready for the next step (docker-compose.yml)**

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
