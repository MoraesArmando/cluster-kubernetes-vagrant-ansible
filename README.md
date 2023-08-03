# Cluster Kubernetes (Vagrant+Ansible)

## Requerimentos
### VirtualBox
#### Instalação
```
$ echo -e "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] https://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib"|sudo tee /etc/apt/sources.list.d/virtualbox.list

$ wget -O- https://www.virtualbox.org/download/oracle_vbox_2016.asc | sudo gpg --dearmor --yes --output /usr/share/keyrings/oracle-virtualbox-2016.gpg

$sudo apt-get install binutils build-essential dkms linux-headers-$(uname -r) make

$ sudo apt-get update && sudo apt-get install virtualbox-6.1
```
### Vagrant
#### Instalação
```
$ wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

$ echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

$ sudo apt update && sudo apt install vagrant
```
#### Plugins    
```
$ vagrant plugin install vagrant-cachier
```