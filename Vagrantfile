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
    config.vm.define "Master#{i}" do |master|
      master.vm.hostname = "Master#{i}"
      master.vm.network "private_network", ip: "192.168.56.#{1+i}", netmask: "255.255.255.0", auto_config: true
      master.vm.provision "shell",
        inline: "cat /vagrant/ansible/roles/k8s-base/files/id_rsa.pub >> .ssh/authorized_keys"

      master.vm.provider "virtualbox" do |vmaster|
        vmaster.name = "Master#{i}"
        vmaster.memory = memoriaMaster
        vmaster.gui = false
      end
    end
  end

  (1..numeroWorker).each do |i|
    config.vm.define "Worker#{i}" do |worker|
      worker.vm.hostname = "worker#{i}"
      worker.vm.network "private_network", ip: "192.168.56.#{31+i}", netmask: "255.255.255.0", auto_config: true
      worker.vm.provision "shell",
        inline: "cat /vagrant/ansible/roles/k8s-base/files/id_rsa.pub >> .ssh/authorized_keys"
    
      worker.vm.provider "virtualbox" do |vworker|
        vworker.name = "Worker#{i}"
        vworker.memory = memoriaWorker
        vworker.gui = false
      end
    end
  end
  
  config.vm.define "ansible" do |ansible|
    ansible.vm.network "private_network", ip: "192.168.56.99", netmask: "255.255.255.0", auto_config: true
    
    ansible.vm.provision "shell", inline: $script_ansible

    ansible.vm.provision "shell",
      inline: "cp /vagrant/ansible/roles/k8s-base/files/id_rsa /home/vagrant/.ssh/ \
      && chmod 600 /home/vagrant/.ssh/id_rsa \
      && chown vagrant:vagrant /home/vagrant/.ssh/id_rsa"            

    ansible.vm.provision "shell",
        inline: "ansible-playbook -i /vagrant/ansible/hosts /vagrant/ansible/k8s-playbook.yml"
    
    ansible.vm.provider "virtualbox" do |vansible|
      vansible.name = "ansible"
      vansible.memory = 1024
      vansible.gui = false
    end
  end

end