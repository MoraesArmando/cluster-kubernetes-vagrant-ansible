# -*- mode: ruby -*-
# vi: set ft=ruby :

# Conf
box = "ubuntu/focal64" 
#
vms = {
  'control-plane-1' => {'memory' => '2048', 'cpus' => 2, 'ip' => '10'}
}
#'worker-1' => {'memory' => '2048', 'cpus' => 1, 'ip' => '20'},

# Instalando o Ansible
$script_ansible = <<-SCRIPT
apt-get update && \
apt-get install -y software-properties-common && \
apt-add-repository --yes ppa:ansible/ansible && \
apt-get update && \
apt-get install -y ansible
SCRIPT

Vagrant.configure("2") do |config|
  # config.vm.box_check_update = false
  config.vm.box = box
  
  vms.each do |name, conf|
    config.vm.define "#{name}" do |v|
      v.vm.hostname = "#{name}.ama.dev" 
      v.vm.network 'private_network', ip: "192.168.56.#{conf['ip']}"
     
      v.vm.provision "shell",
          inline: "cat /vagrant/roles/k8s-base/files/id_rsa.pub >> .ssh/authorized_keys"

      v.vm.provider "virtualbox" do |vb|
        # vb.name = name
        vb.memory = conf['memory']
        vb.cpus = conf['cpus']
      end
    end
  end
  
  config.vm.define "ansible" do |ansible|
    ansible.vm.network "private_network", ip: "192.168.56.99"
    
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