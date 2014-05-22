# -*- mode: ruby -*-
# vi: set ft=ruby :

#####################################################################################
# start config
#####################################################################################

# define some structs
ChefDef = Struct.new(:node_name, :roles, :recipes)
BoxDef = Struct.new(:name, :host_name, :private_ip, :chefdef)

#####
# Define your chef configs here for the new vms
#
chefdef1=ChefDef.new("ubuntu-chef", %w(base), %w())
chefdef2=ChefDef.new("ubuntu-chef2", %w(base), %w())

#####
# Define your box customizations here
#
boxes = [
  BoxDef.new('box1', 'vagrant-vm', '192.168.56.160', chefdef1),
  BoxDef.new('box2', 'vagrant-vm2', '192.168.56.161', chefdef2)
]
orgName=ENV['CHEF_ORG']

#####################################################################################
# end config
#####################################################################################

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

#####################################################################################
#
# mac magic to avoid 
#
is_osx=false

if (/darwin/ =~ RUBY_PLATFORM) != nil
  is_osx=true
end

if is_osx
  pref_interface = ['en0: Wi-Fi (AirPort)','en3: Thunderbolt 1', 'en4: Thunderbolt 2']
  vm_interfaces = %x( VBoxManage list bridgedifs | grep ^Name ).gsub(/Name:\s+/, '').split("\n")
  pref_interface = pref_interface.map {|n| n if vm_interfaces.include?(n)}.compact
  $network_interface = pref_interface[0]
end
#####################################################################################

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = 'ubuntu-12.04'
  config.vm.box_url = "http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_#{config.vm.box}_chef-provisionerless.box"

  # set the size of your created VM here by uncommenting and setting param
  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "512"]
  end

  if is_osx
    config.vm.network "public_network", :bridge => $network_interface
  else
    config.vm.network "public_network"
  end
  config.omnibus.chef_version = :latest

  boxes.each do |box|
    config.vm.define box.name do |vmbx|
      vmbx.vm.hostname = box.host_name
      if is_osx
        vmbx.vm.network "public_network", :bridge => $network_interface
      else
        vmbx.vm.network "public_network"
      end
      vmbx.vm.network "private_network", ip: box.private_ip
      if box.chefdef
        vmbx.vm.provision :chef_client do |chef|
          chef.chef_server_url = "https://api.opscode.com/organizations/#{orgName}"
          chef.validation_client_name = "#{orgName}-validator"
          chef.validation_key_path = "#{ENV['HOME']}/.chef/#{orgName}-validator.pem"

          box.chefdef.roles.each do |role|
            chef.add_role role
          end

          box.chefdef.recipes.each do |recipe|
            chef.add_recipe recipe
          end

          chef.node_name = box.chefdef.node_name
          chef.provisioning_path = "/etc/chef"
          chef.log_level = :info
          chef.delete_node = true
          chef.delete_client = true
        end
      end
    end
  end
end
