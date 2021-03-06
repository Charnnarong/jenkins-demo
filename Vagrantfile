# -*- mode: ruby -*-
# vi: set ft=ruby :
required_plugins = %w(vagrant-vbguest)
required_plugins.each do |plugin|
  system "vagrant plugin install #{plugin}" unless Vagrant.has_plugin? plugin
end

Vagrant.configure("2") do |config|

  # Docker Node
  config.vm.define "centos-build" do |d|
    d.vm.box = "centos/7"
    d.vm.hostname = "centos-build"
    d.vm.network "private_network" , ip: "10.100.199.10"
    d.vm.synced_folder "./resources", "/vagrant"
    d.vm.provider "virtualbox" do |v|
      v.memory = 2048
    end
  end

  # Docker Node
  config.vm.define "centos-docker" do |d|
    d.vm.box = "centos/7"
    d.vm.hostname = "centos-docker"
    d.vm.network "private_network" , ip: "10.100.199.20"
    d.vm.synced_folder "./resources", "/vagrant"
    d.vm.provider "virtualbox" do |v|
      v.memory = 2048
    end
  end

  config.vm.define "centos-ansible" do |d|
    d.vm.box = "centos/7"
    d.vm.hostname = "centos-ansible"
    d.vm.network "private_network" , ip: "10.100.196.10"
    d.vm.synced_folder ".", "/vagrant", rsync__exclude: ".git/"
    d.vm.synced_folder ".vagrant", "/vagrants/pk", mount_options: ["dmode=700,fmode=600"]
    d.vm.provision :shell, path: "scripts/bootstrap_ansible.sh"

    playbooks = [["/vagrant/resources/ansible/provisionBuildSystem.yml","/vagrant/resources/ansible/hosts/build-systems"],
                 ["/vagrant/resources/ansible/provisionDockerNodes.yml","/vagrant/resources/ansible/hosts/docker-nodes"]]
    playbooks.each { | (playbook,inventory) |
      d.vm.provision "ansible_local" do |ansible|
        ansible.playbook = "#{playbook}"
        ansible.install = false
        ansible.verbose = true
        ansible.limit          = "all"
        ansible.inventory_path = "#{inventory}"

      end
    }
    d.vbguest.auto_update = true
  end

  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = false
    config.vbguest.no_remote = true
  end

end
