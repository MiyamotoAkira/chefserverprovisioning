# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "geerlingguy/centos7"

  config.vm.network :forwarded_port, guest: 80, host: 8081
  config.vm.network :forwarded_port, guest: 443, host: 8082

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network :private_network, ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network :public_network

  # If true, then any SSH connections made will enable agent forwarding.
  # Default value: false
  # config.ssh.forward_agent = true

   config.vm.synced_folder "../installations", "/installations"
   
   config.vm.provider :virtualbox do |vb|
     vb.name="chefserver"
     vb.memory=1024
     vb.cpus=1 
   end

   config.vm.provision "installation chef", type:"shell", inline: "sudo rpm -Uvh /installations/chef-server-core-12.6.0-1.el7.x86_64.rpm"
   config.vm.provision "installation chef manage", type:"shell", inline: "chef-server-ctl install chef-manage"
   config.vm.provision "start", type: "shell", inline: "chef-server-ctl reconfigure"
   config.vm.provision "setup chef manage", type: "shell", inline: "chef-manage-ctl reconfigure --accept-license"
   config.vm.provision "save folder", type: "shell", inline: "sudo mkdir /chefconfig"
   config.vm.provision "create user", type:"shell", inline: "sudo chef-server-ctl user-create #{Secret.username} #{Secret.firstname} #{Secret.lastname} #{Secret.email} '#{Secret.password}' --filename /chefconfig/#{Secret.username}.pem"
   config.vm.provision "create organization", type: "shell", inline: "sudo chef-server-ctl org-create #{Secret.orgname} '#{Secret.org}' --association_user #{Secret.username} --filename /chefconfig/#{Secret.orgname}.pem"
   
   config.vm.provision :host_shell do |host_shell|
     host_shell.inline = "vagrant scp :/chefconfig/*.pem ../chefconfig"
   end
end
