# vagrant_puppet
Vagrant setup spinning up single/multiple boxes and provisioning them with Puppet

You can choose operating system by setting environment variable `OS` or editing it directly in the top of the Vagrantfile:
```
- C5    Centos 5      (wandisco/centos-5-64)
- C6    Centos 6      (wandisco/centos-6-64)
- C7    Centos 7      (wandisco/centos-7-64)
- D6    Debian 6      (wandisco/debian-6-64)
- D7    Debian 7      (wandisco/debian-7-64)
- D8    Debian 8      (wandisco/debian-8-64)
- U12   Ubuntu 12.04  (wandisco/ubuntu-12.04-64)
- U14   Ubuntu 14.04  (wandisco/ubuntu-14.04-64)
- U16   Ubuntu 16.04  (wandisco/ubuntu-16.04-64)
- S11   SLES 11 SP3   (wandisco/sles-11.3-64)
```

The following variables can be further adjusted to your needs:
```
N = 1                                 # Number of nodes to be created
IP_ADDR_PREFIX = "192.168.254.1"      # IP address prefix
DOMAIN = "domain.com"                 # Domain name
HOSTNAME_PREFIX = "node"              # Hostname prefix
CORES = 2                             # Number of CPU cores for each box
RAM = 2048                            # Amount of RAM for each box

EXTRA_DISK_SIZE = 2048                # Second hard drive (set 0 to disable)
EXTRA_DISK_FILE = "sdb.vdi"           # Hard drive filename
```

List of Puppet modules to be provisioned can be set by editing file `manifests/default.pp`.

The machines are deployed in a private network, each one can communicate with others using both IP addresses and hostnames.

Passwordless SUDO is configured for user vagrant.

Passwordless SSH between boxes is configured for user root.

## Example
To spin up a Centos 6 environment with 3 nodes, run:

```
$ N=3 OS=C6 vagrant up
```
