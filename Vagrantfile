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


Vagrant.configure("2") do |config|
  config.vm.box = box
  
  (1..numeroMaster).each do |i|
    config.vm.define "Master-#{i}" do |master|
      master.vm.hostname = "Master#{i}"
      master.vm.network "private_network", ip: "192.168.56.#{i}"

      master.vm.provision "shell",
        inline: "cat /vagrant/configs/ssh/id_rsa.pub >> .ssh/authorized_keys"
    end
  end

  (1..numeroWorker).each do |i|
    config.vm.define "Worker-#{i}" do |worker|
      worker.vm.hostname = "worker#{i}"
      worker.vm.network "private_network", ip: "192.168.56.#{30+i}"


      worker.vm.provision "shell",
        inline: "cat /vagrant/configs/ssh/id_rsa.pub >> .ssh/authorized_keys"
    end
  end
  
  config.vm.define "ansible" do |ansible|
    ansible.vm.network "public_network", ip: "192.168.56.99", bridge: "enp0s31f6"
    
    ansible.vm.provision "shell",
      inline: "apt-get update && \
              apt-get install -y software-properties-common && \
              apt-add-repository --yes ppa:ansible/ansible && \
              apt-get update && \
              apt-get install -y ansible"

    ansible.vm.provision "shell",
      inline: "cp /vagrant/configs/ssh/id_rsa /home/vagrant/.ssh/ \
      && chmod 600 /home/vagrant/.ssh/id_rsa \
      && chown vagrant:vagrant /home/vagrant/.ssh/id_rsa"            
      
    ansible.vm.provision "shell",
        inline: "ansible-playbook -i /vagrant/ansible/hosts /vagrant/ansible/k8s-playbook.yml"
  end

end