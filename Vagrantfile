# -*- mode: ruby -*-
# vi: set ft=ruby :

OS = ENV["OS"] || "C7"

case OS
when "C5"
  BOX = "wandisco/centos-5-64"
when "C6"
  BOX = "wandisco/centos-6-64"
when "C7"
  BOX = "wandisco/centos-7"
when "U12"
  BOX = "wandisco/ubuntu-12.04-64"
when "U14"
  BOX = "wandisco/ubuntu-14.04-64"
when "U16"
  BOX = "wandisco/ubuntu-16.04-64"
when "D6"
  BOX = "wandisco/debian-6-64"
when "D7"
  BOX = "wandisco/debian-7-64"
when "D8"
  BOX = "wandisco/debian-8-64"
when "D9"
  BOX = "wandisco/debian-9-64"
when "S11"
  BOX = "wandisco/sles-11.3-64"
when "S12"
  BOX = "wandisco/sles-12.1-64"
when "F25"
  BOX = "wandisco/fedora-25-64"
else
  puts "OS label #{OS} not supported, exiting..."
  exit 1
end

puts "==> OS: #{OS}, BOX: #{BOX}"

N = ENV["N"] || "1"
IP_ADDR_PREFIX = "192.168.254.11"
DOMAIN = "domain.com"
HOSTNAME_PREFIX = "#{OS}-node".downcase
CORES = 2
RAM = 2048

# Second hard drive. To disable, set its size to 0.
EXTRA_DISK_SIZE = 0
EXTRA_DISK_FILE = "sdb.vdi"

Vagrant.configure(2) do |config|

  config.ssh.insert_key = false

  config.vm.box = BOX
  config.vm.synced_folder "../puppet-qe/modules/", "/puppet-qe"
  config.vm.synced_folder "../puppet-common/", "/puppet-common"
  config.vm.synced_folder "../puppet-sensitive/", "/puppet-sensitive"
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

  $message = "List of #{BOX} nodes:"
  (1..N.to_i).each do |i|
    $message = $message + "\n - #{HOSTNAME_PREFIX}#{i}.#{DOMAIN} (#{IP_ADDR_PREFIX}#{i})"
  end
  config.vm.define "#{HOSTNAME_PREFIX}1.#{DOMAIN}" do |node|
    node.vm.post_up_message = $message
  end

  (1..N.to_i).each do |i|
    nodeName = HOSTNAME_PREFIX + "#{i}" + "." + DOMAIN
    ipAddr = IP_ADDR_PREFIX + "#{i}"
    config.vm.define nodeName do |node|
      node.vm.hostname = nodeName
      node.vm.network "private_network", ip: ipAddr
$script = <<SCRIPT
echo "127.0.0.1 localhost" > /etc/hosts
for i in {1..#{N}}; do
  echo "#{IP_ADDR_PREFIX}$i #{HOSTNAME_PREFIX}$i.#{DOMAIN} #{HOSTNAME_PREFIX}$i" >> /etc/hosts
done
cp -a /vagrant/files/.ssh ~/
chown -R root:root ~/.ssh
chmod 700 ~/.ssh
chmod 600 ~/.ssh/*
which apt-get &>/dev/null && apt-get update || true
SCRIPT
      node.vm.provision "shell", inline: $script
      node.vm.provision "puppet" do |puppet|
        puppet.module_path    = ["../puppet-common", "../puppet-sensitive", "../puppet-qe/modules"]
        puppet.options = "--disable_warnings deprecations"
        puppet.environment_path = "environments"
        puppet.environment = "default"
        #puppet.facter = {
        #  "docker_direct_lvm"   => "yes",
        #  "docker_jenkins_port" => "31337"
        #}
      end
    end
  end
end
