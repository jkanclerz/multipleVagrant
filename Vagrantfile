1# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  config.vm.network "private_network", ip: "192.168.33.40"
  config.ssh.forward_agent = true
  config.ssh.private_key_path = "~/.ssh/id_rsa"
  config.ssh.username = "vagrant"
  config.ssh.password = "vagrant"


  require 'rbconfig'
  is_windows = (RbConfig::CONFIG['host_os'] =~ /mswin|mingw|cygwin/)
  if is_windows
    # Provisioning configuration for shell script.
    config.vm.provision "shell" do |sh|
      sh.path = "provisioning/windows.sh"
      sh.args = "provisioning/site.yml provisioning/hosts"
    end
  else
    # Provisioning configuration for Ansible (for Mac/Linux hosts).
    config.vm.provision "ansible" do |ansible|
      ansible.playbook = "provisioning/site.yml"
      ansible.inventory_path = "provisioning/hosts.real"
      ansible.sudo = true
      ansible.extra_vars = { ansible_ssh_user: 'vagrant' }
      ansible.limit = "vagrant"
      ansible.verbose = "vvvv"
    end
  end

  config.vm.provider "virtualbox" do |v|
    v.name = "Big Data m1"
    v.memory = 2048
    v.cpus = 3
  end
end

Vagrant.configure("2") do |config|
  # other config here
  config.vm.network "forwarded_port", guest: 80, host: 8081, auto_correct: true
  config.vm.network "forwarded_port", guest: 1080, host: 1081, protocol: 'tcp'
  config.vm.synced_folder "./", "/home/vagrant/bigdata", type: "nfs", mount_options: ['rw', 'tcp', 'fsc', 'nolock', 'udp', 'noatime'], nfs_version: 4
end
