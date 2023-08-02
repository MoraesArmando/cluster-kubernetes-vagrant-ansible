# -*- mode: ruby -*-
# vi: set ft=ruby :

# Conf
box = "ubuntu/focal64" 
# Master
numeroMaster = 1
memoriaMaster = 2048
# Worker
numeroWorker = 1
memoriaWorker = 1024

# Instalando o Ansible
$script_ansible = <<-SCRIPT
apt-get update && \
apt-get install -y software-properties-common && \
apt-add-repository --yes ppa:ansible/ansible && \
apt-get update && \
apt-get install -y ansible
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = box
  
  (1..numeroMaster).each do |i|
    config.vm.define "master#{i}" do |master|
      master.vm.hostname = "master#{i}"
      master.vm.network "public_network", ip: "192.168.1.#{1+i}", bridge: "enp0s31f6", auto_config: true
     
      master.vm.provision "shell",
        inline: "cat /vagrant/roles/k8s-base/files/id_rsa.pub >> .ssh/authorized_keys"

      master.vm.provider "virtualbox" do |vmaster|
        vmaster.name = "master#{i}"
        vmaster.memory = memoriaMaster
        vmaster.gui = false
      end
    end
  end

  (1..numeroWorker).each do |i|
    config.vm.define "worker#{i}" do |worker|
      worker.vm.hostname = "worker#{i}"
      worker.vm.network "public_network", ip: "192.168.1.#{31+i}", bridge: "enp0s31f6", auto_config: true
     
      worker.vm.provision "shell",
        inline: "cat /vagrant/roles/k8s-base/files/id_rsa.pub >> .ssh/authorized_keys"
      
      worker.vm.provider "virtualbox" do |vworker|
        vworker.name = "worker#{i}"
        vworker.memory = memoriaWorker
        vworker.gui = false
      end
    end

  end
  
  config.vm.define "ansible" do |ansible|
    ansible.vm.network "public_network", ip: "192.168.1.99", bridge: "enp0s31f6", auto_config: true
    
    ansible.vm.provision "shell", inline: $script_ansible

    ansible.vm.provision "shell",
      inline: "cp /vagrant/roles/k8s-base/files/id_rsa /home/vagrant/.ssh/ \
      && chmod 600 /home/vagrant/.ssh/id_rsa \
      && chown vagrant:vagrant /home/vagrant/.ssh/id_rsa"            

    ansible.vm.provision "shell",
        inline: "ansible-playbook -i /vagrant/hosts /vagrant/k8s-playbook.yml"
    
    ansible.vm.provider "virtualbox" do |vansible|
      vansible.name = "ansible"
      vansible.memory = 1024
      vansible.gui = false
    end
  end

  if Vagrant.has_plugin?("vagrant-cachier")
    # Configure cached packages to be shared between instances of the same base box.
    # More info on http://fgrehm.viewdocs.io/vagrant-cachier/usage
    config.cache.scope = :box
  end

end