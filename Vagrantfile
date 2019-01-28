# -*- mode: ruby -*-
# vi: set ft=ruby :
require 'yaml'

current_dir = File.dirname(File.expand_path(__FILE__))

if File.file?("#{current_dir}/localsettings.yml")
    settings = YAML.load_file("#{current_dir}/localsettings.yml")
else
    settings = YAML.load_file("#{current_dir}/localsettings.yml.dist")
end


Vagrant.configure("2") do |config|

   config.vm.define "sles12box" do |sles12box|
        sles12box.vm.box = "sles12sp2.box"
        sles12box.vm.network "private_network", ip:"192.168.100.41"
        sles12box.vm.network "forwarded_port", guest: 80, host: 8080
        sles12box.vm.hostname = "sles12box"

        sles12box.vm.provision "shell", inline: <<-SHELL
            sudo rm /etc/zypp/repos.d/*
            echo "#{ settings['repo_server'] }"
            sudo zypper ar --refresh http://#{ settings['repo_server'] }/repository/sles12-sp2/dvd/ dvd1
            sudo zypper ar --refresh http://#{ settings['repo_server'] }/repository/sles12-sp2/sdk/ sdk1
            sudo zypper ar --refresh https://ftp5.gwdg.de/pub/opensuse/repositories/devel:/languages:/python/SLE_12_SP2/ python
            sudo zypper ar --refresh https://ftp5.gwdg.de/pub/opensuse/repositories/Apache:/Modules/SLE_12_SP2/ apache
            sudo zypper --gpg-auto-import-keys refresh
        SHELL


        sles12box.vm.provision :ansible do |ansible|
            ansible.playbook = "playbook.yml"
            ansible.host_key_checking = false
            ansible.become = true
            ansible.tags="install"
            ansible.extra_vars={
                PI_PACKAGE_VERSION: "v2.23.3"
            }
        end
   end

   config.vm.define "sles15box" do |sles15box|
        sles15box.vm.box = "sles15c.box"
        sles15box.vm.network "private_network", ip:"192.168.100.42"
        sles15box.vm.hostname = "sles15box"

        sles15box.vm.provision "shell", inline: <<-SHELL
            sudo rm /etc/zypp/repos.d/*
            sudo zypper ar --refresh http://#{ settings['repo_server'] }/repository/sles15/Installer/ Installer
            sudo zypper ar --refresh http://#{ settings['repo_server'] }/repository/sles15/Product-SLES/ Product-SLES
            sudo zypper ar --refresh http://#{ settings['repo_server'] }/repository/sles15/Module-Basesystem/ Module-Basesystem
            #mod_wsgi
            sudo zypper ar --refresh http://#{ settings['repo_server'] }/repository/sles15/Module-Public-Cloud/ Module-Public-Cloud
            #apache
            sudo zypper ar --refresh http://#{ settings['repo_server'] }/repository/sles15/Module-Server-Applications/ Module-Server-Applications
            sudo zypper ar --refresh https://ftp5.gwdg.de/pub/opensuse/repositories/devel:/languages:/python/SLE_15/ OSB-Python-Devel
            sudo zypper --gpg-auto-import-keys refresh

            sudo zypper --non-interactive install python2 python2-xml

        SHELL

        sles15box.vm.provision :ansible do |ansible|
            ansible.playbook = "playbook.yml"
            ansible.tags="install"
            ansible.extra_vars={
                PI_PACKAGE_VERSION: "v2.23.3"
            }
            ansible.host_key_checking = false
            ansible.become = true
        end
   end

end
