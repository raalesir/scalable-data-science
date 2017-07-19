Installing and configuring Cobbler
==================================

The installation and configuration of Cobbler for CentOS is
 very good described at:
http://cobbler.github.io/manuals/quickstart/

Here we will just show th settings viable for out setup.


### Cobbler settings

The main configuration file `/etc/cobbler/settings` should contain:

    # default, localhost
    next_server: 10.200.1.1
    # default, localhost
    server: 10.200.1.1
    # set to 1 to enable Cobbler's DHCP management features.
    # the choice of DHCP management engine is in /etc/cobbler/modules.conf
    manage_dhcp: 1
    # set to 1 to enable Cobbler's DNS management features.
    # the choice of DNS mangement engine is in /etc/cobbler/modules.conf
    manage_dns: 1


Make sure `/etc/cobbler/modules.conf` contains:

    [dns]
    module = manage_dnsmasq
    [dhcp]
    module = manage_dnsmasq


Finally, the contents of the `/etc/dnsmasq.conf` is the following, edit if needed:

```
[root@c2gw xadmin]# cat /etc/dnsmasq.conf
# Cobbler generated configuration file for dnsmasq
# Tue May 16 08:46:07 2017

interface=vlan2
dhcp-option=3,10.200.1.1
domain=c2.domain.com,10.200.1.0/24,local
dhcp-range=10.200.1.100,10.200.1.199
dhcp-sequential-ip
dhcp-script=/var/lib/misc/dnsmasq-assign.sh
dhcp-hostsfile=/var/lib/misc/dnsmasq.hosts
dhcp-leasefile=/var/lib/misc/dnsmasq.leases
server=8.8.8.8
dhcp-boot=pxelinux.0 #,boothost,10.200.1.101
enable-tftp
tftp-root=/var/lib/tftpboot
```



What is important here is the  IP leasing range for  `vlan2` (`10.200.1.100-10.200.1.199`).


Check that the both are running:

    systemctl status dnsmasq
    systemctl status cobblerd

After making sure that both `cobblerd` and `dnsmasq` are running,
we can attach an  empty NUC and try to PXE boot it.

Here we assume that one has configured Cobbler, imported OS image, created distro, edited the kickstart file as needed. All the files for PXE-booting must be in the `/var/lib/tftpboot` folder.

**This tutorial relies on the well documented process of importing Linux distros to he Cobbler and editing kickstart templates.
There are also some HOW-TO formed during the installation. Those have to be added later!**

### Result

Now a bare metal box could be provisioned with OS in the automatic manner!
