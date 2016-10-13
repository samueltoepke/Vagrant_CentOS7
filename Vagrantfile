## Copyright 2016 Samuel Lee Toepke
## 
## Licensed under the Apache License, Version 2.0 (the "License");
## you may not use this file except in compliance with the License.
## You may obtain a copy of the License at
## 
## http://www.apache.org/licenses/LICENSE-2.0
## 
## Unless required by applicable law or agreed to in writing, software
## distributed under the License is distributed on an "AS IS" BASIS,
## WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
## See the License for the specific language governing permissions and
## limitations under the License.
## 

# Samuel Lee Toepke
# samueltoepke@gmail.com
# Fall 2016
# Vagrantfile
#
# This Vagrantfile uses the default centos/7 box from "https://atlas.hashicorp.com/centos/boxes/7/".
# Various tasks are run to create an updated/secure CentOS development environment with a GUI, virtualized on VirtualBox.
# 
# Dependencies: VirtualBox 4.3.36+, Vagrant 1.8.6+
# Base OS: Ubuntu 14.04 LTS, any modern Linux distribution with Vagrant/VirtualBox should work.
#
# Standard login for box (uid/pwd):(root/vagrant)
# All the provisioning below can be put in its own .sh file, left it here to keep everything in one place.
# Below, make sure to change user_name, user_pwd and vbox_vers for Guest Additions.
# If you don't need Guest Additions, comment that section (#8) out, everything else will still work.
#
# Run/perform the following: 
#   $ cd .
#   $ vagrant box add centos/7
#   $ vagrant up
#
# Other useful Vagrant commands:
#   $ vagrant box list        # List available boxes.
#   $ vagrant global-status   # Get current status of all Vagrant machines.
#   $ vagrant destroy -f <id> # Destroy specific Vagrant instance.
#   $ vagrant rsync           # Forces a host->guest update of any synced folders.

Vagrant.configure("2") do |config|

  # 0. Set Variables
  user_name = "NewUser"
  user_pwd  = "newuserabc123"
  vbox_vers = "4.3.36"
 
  # 1. Configure VirtualBox.
  config.vm.box = "centos/7"
  config.vm.provider "virtualbox" do |v|
    v.cpus   = 2
    v.gui    = true
    v.memory = "2048"
    v.customize ["modifyvm", :id, "--vram", "64"]
    v.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
  end
  config.vm.synced_folder ".", "/vagrant", type: "rsync" #https://www.hashicorp.com/blog/feature-preview-vagrant-1-5-rsync.html

  # 2. Enable Firewall.
  config.vm.provision "shell", inline: "systemctl enable firewalld"
  config.vm.provision "shell", inline: "systemctl start firewalld"

  # 3. Immediate Login/Security. Make sure to enter your own username/password in the variable section above.
  config.vm.provision "shell", inline: "passwd vagrant -l" # Lockout 'vagrant' account.
  config.vm.provision "shell", inline: "useradd #{user_name}"   # Add new user.
  config.vm.provision "shell", inline: "echo \"#{user_name}:#{user_pwd}\" | chpasswd" # Change new user password.
  config.vm.provision "shell", inline: "echo '#{user_name}  ALL=(ALL:ALL) ALL' >> /etc/sudoers" # Add user to sudoers.
  config.vm.provision "shell", inline: "sed -i 's/^#PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config" # Disable root login from SSH.

  # 4. Install "wget", Add "Extra Packages for Enterprise Linux".
  config.vm.provision "shell", inline: "yum -y install wget"
  config.vm.provision "shell", inline: "wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-8.noarch.rpm"
  config.vm.provision "shell", inline: "rpm -ivh epel-release-7-8.noarch.rpm"
  
  # 5. Update/Upgrade The System.
  config.vm.provision "shell", inline: "yum -y update"
  config.vm.provision "shell", inline: "yum -y upgrade"
  
  # 6. Install/Enable GUI.
  config.vm.provision "shell", inline: "yum -y groupinstall \"GNOME Desktop\" \"Graphical Administration Tools\""
  config.vm.provision "shell", inline: "systemctl set-default graphical.target"

  # 7. Install Ancillary Packages.
  config.vm.provision "shell", inline: "yum -y groupinstall \"Development Tools\""
  config.vm.provision "shell", inline: "yum -y install elinks lynx nmap kernel-devel-$(uname -r) dkms yum-plugin-priorities"
  
  # 8. Install VirtualBox Guest Additions. Thanks: https://gist.github.com/fernandoaleman/5083680
  config.vm.provision "shell", inline: "wget -c http://download.virtualbox.org/virtualbox/#{vbox_vers}/VBoxGuestAdditions_#{vbox_vers}.iso -O VBoxGuestAdditions_#{vbox_vers}.iso"
  config.vm.provision "shell", inline: "mount VBoxGuestAdditions_#{vbox_vers}.iso -o loop /mnt"
  
  # Below command works perfectly from command line on provisioned machine if you login and execute,
  #   and also 'works' here as a provision (e.g. reboot after the 'fail', guest additions is installed 
  #   and funtioning), but still throws a Vagrant error when 'vagrant up' is run. Adding the "; true" 
  #   at the end of the command bypasses the error. ...resolution for future work.
  config.vm.provision "shell", inline: "/mnt/VBoxLinuxAdditions.run --nox11 ; true" 
  
  config.vm.provision "shell", inline: "rm -rf VBoxGuestAdditions_#{vbox_vers}.iso"

  # 9. Reboot
  config.vm.provision "shell", inline: "/sbin/reboot"

end





