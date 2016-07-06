# -*- mode: ruby -*-
# vi: set ft=ruby :

OS = ENV["OS"] || "C7"

case OS
when "C6"
  BOX = "puppetlabs/centos-6.6-64-nocm"
when "C7"
  BOX = "puppetlabs/centos-7.2-64-nocm"
when "U12"
  BOX = "puppetlabs/ubuntu-12.04-64-nocm"
when "U14"
  BOX = "puppetlabs/ubuntu-14.04-64-nocm"
when "D7"
  BOX = "puppetlabs/debian-7.8-64-nocm"
when "D8"
  BOX = "puppetlabs/debian-8.2-64-nocm"
else
  puts "OS label #{OS} not supported, exiting..."
  exit 1
end

NUM_NODES = ENV["NUM_NODES"] || "1"
IP_ADDR_PREFIX = "192.168.69.1"
DOMAIN = "domain"
HOSTNAME_PREFIX = "node"
CORES = 2
RAM = 2048

# Second hard drive. To disable, set its size to 0.
EXTRA_DISK_SIZE = 0
EXTRA_DISK_FILE = "sdb.vdi"

Vagrant.configure(2) do |config|

  config.ssh.insert_key = false

  config.vm.box = BOX
  config.vm.synced_folder "~/git/puppet-common/", "/puppet-common"
  config.vm.synced_folder "~/git/puppet-sensitive/", "/puppet-sensitive"
  config.vm.provider "virtualbox" do |vb|
    if EXTRA_DISK_SIZE > 0
      unless File.exist?(EXTRA_DISK_FILE)
        vb.customize ["createhd", "--filename", EXTRA_DISK_FILE, "--size", EXTRA_DISK_SIZE]
        vb.customize ["storagectl", :id, "--name", "SATA Controller", "--add", "sata"]
      end
      vb.customize ["storageattach", :id, "--storagectl", "SATA Controller", "--port", 1, "--device", 0, "--type", "hdd", "--medium", EXTRA_DISK_FILE]
    end
    vb.customize ["modifyvm", :id, "--ioapic", "on"]
    vb.customize ["modifyvm", :id, "--cpus", CORES]
    vb.customize ["modifyvm", :id, "--memory", RAM]
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  (1..NUM_NODES.to_i).each do |i|
    nodeName = HOSTNAME_PREFIX + "#{i}" + "." + DOMAIN
    ipAddr = IP_ADDR_PREFIX + "#{i}"
    config.vm.define nodeName do |node|
      node.vm.hostname = nodeName
      node.vm.network "private_network", ip: ipAddr
$script = <<SCRIPT
echo "127.0.0.1 localhost" > /etc/hosts
for i in {1..#{NUM_NODES}}; do
  echo "#{IP_ADDR_PREFIX}$i #{HOSTNAME_PREFIX}$i.#{DOMAIN} #{HOSTNAME_PREFIX}$i" >> /etc/hosts
done
cp -a /vagrant/files/.ssh ~/
chown -R root:root ~/.ssh
chmod 700 ~/.ssh
chmod 600 ~/.ssh/*
OS="#{OS}"
case "$OS" in
"C6")
  if ! type puppet &>/dev/null; then
    yum -y install https://yum.puppetlabs.com/puppetlabs-release-el-6.noarch.rpm
    yum -y install puppet
  fi
  ;;
"C7")
  if ! type puppet &>/dev/null; then
    yum -y install https://yum.puppetlabs.com/puppetlabs-release-el-7.noarch.rpm
    yum -y install puppet
  fi
  ;;
"D7" | "D8" | "U12" | "U14")
  if ! type puppet &>/dev/null; then
    apt-get update
    apt-get -y install puppet
  fi
  ;;
esac
SCRIPT
      node.vm.provision "shell", inline: $script
      node.vm.provision "puppet" do |puppet|
        puppet.module_path    = ["~/git/puppet-common", "~/git/puppet-sensitive"]
        puppet.manifests_path = "files"
        puppet.manifest_file  = "site.pp"
      end
    end
  end
end
