# vagrant_puppet
Vagrant setup spinning up single/multiple boxes and provisioning them with Puppet

You can choose operating system by setting environment variable `OS` or editing it directly in the top of the Vagrantfile:
```
- C6    Centos 6.6    (puppetlabs/centos-6.6-64-nocm)
- C7    Centos 7.2    (puppetlabs/centos-7.2-64-nocm)
- D7    Debian 7.8    (puppetlabs/debian-7.8-64-nocm)
- D8    Debian 8.2    (puppetlabs/debian-8.2-64-nocm)
- U12   Ubuntu 12.04  (puppetlabs/ubuntu-12.04-64-nocm)
- U14   Ubuntu 14.04  (puppetlabs/ubuntu-14.04-64-nocm)
```

The following variables can be further adjusted to your needs:
```
NUM_NODES = 1                         # Number of nodes to be created
IP_ADDR_PREFIX = "192.168.69.1"       # IP address prefix
DOMAIN = "domain"                     # Domain name
HOSTNAME_PREFIX = "node"              # Hostname prefix
CORES = 2                             # Number of CPU cores for each box
RAM = 2048                            # Amount of RAM for each box

EXTRA_DISK_SIZE = 2048                # Second hard drive (set 0 to disable)
EXTRA_DISK_FILE = "sdb.vdi"           # Hard drive filename
```

List of Puppet modules can be set by editing file `files/site.pp`.

The boxes will be set up in a private network, each box can communicate with all other boxes using both IP addresses and hostnames.

Passwordless SUDO has been set up for user vagrant.

Passwordless SSH between boxes has been set up for user root.
