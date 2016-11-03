# vagrant_puppet
Vagrant setup spinning up single/multiple boxes and provisioning them with Puppet

You can choose operating system by setting environment variable `OS` or editing it directly in the top of the Vagrantfile:
```
- C6    Centos 6      (centos/6)
- C7    Centos 7      (centos/7)
- D7    Debian 7      (debian/wheezy64)
- D8    Debian 8      (debian/jessie64)
- U12   Ubuntu 12.04  (ubuntu/precise64)
- U14   Ubuntu 14.04  (ubuntu/trusty64)
- U16   Ubuntu 16.04  (ubuntu/xenial64)
```

The following variables can be further adjusted to your needs:
```
N = 1                                 # Number of nodes to be created
IP_ADDR_PREFIX = "192.168.69.1"       # IP address prefix
DOMAIN = "domain.com"                 # Domain name
HOSTNAME_PREFIX = "node"              # Hostname prefix
CORES = 2                             # Number of CPU cores for each box
RAM = 2048                            # Amount of RAM for each box

EXTRA_DISK_SIZE = 2048                # Second hard drive (set 0 to disable)
EXTRA_DISK_FILE = "sdb.vdi"           # Hard drive filename
```

List of Puppet modules to be provisioned can be set by editing file `files/site.pp`.

The boxes will be set up in a private network, each box can communicate with all other boxes using both IP addresses and hostnames.

Passwordless SUDO has been set up for user vagrant.

Passwordless SSH between boxes has been set up for user root.

## Example
To spin up a Centos 6 environment with 3 nodes, run:

```
$ N=3 OS=C6 vagrant up
```
