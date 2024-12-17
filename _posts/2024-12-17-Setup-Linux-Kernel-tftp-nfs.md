---
layout: post
---
The following article shows how to start Linux kernel debugging using `tftp` and `nfs`.
- **target: 192.168.1.10**
- **host: 192.168.1.3**

##  Configure dhcp server
for our purpose we `use isc-dhcp-server` then install it with the following command:

```
# install dhcp server on your host
sudo apt install isc-dhcp-server

# check the status
sudo systemctl start isc-dhcp-server
sudo systemctl status isc-dhcp-server

# include the following setting in order to bind  mac with ip-address you want
cat /etc/dhcp/dhcpd.conf 

host imx93 {
  hardware ethernet 00:04:9f:08:48:a8;
  fixed-address 192.168.1.10;
}

subnet 192.168.1.0 netmask 255.255.255.0 {
  range 192.168.1.10 192.168.1.14;
  option routers 192.168.1.3;
}

INTERFACESv4="enx28ee52020d72";
```

## Configure tftp 
```
# install tftp server
sudo apt install tftpd-hpa

# create folder
mkdir -p /tftp/boot

# Configure it
cat /etc/default/tftpd-hpa 

# /etc/default/tftpd-hpa

TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/tftp/boot"
TFTP_ADDRESS=":69"
TFTP_OPTIONS="--secure -v"


# check the status
sudo systemctl start tftpd-hpa
```

## Configure NFS
Install NFS and configure it using the followng commands:
```
# install NFS on the host
sudo apt install nfs-kernel-server

# verify the status
sudo systemctl start nfs-kernel-server.service
sudo systemctl status nfs-kernel-server.service

# create nfs rootfs folder
mkdir -p /tftp/root

# configure it
cat /etc/exports 

# /etc/exports: the access control list for filesystems which may be exported
#		to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
# 192.168.1.10 is the target device and not the host!
/tftp/root 192.168.1.10(rw,sync,no_root_squash,no_subtree_check)
```

## Configure u-boot environment
```
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.3
setenv loaddtb "tftp 0x80400000 imx93-11x11-evk.dtb"
setenv loadkernel "tftp 0x83000000 Image"
setenv netargs "setenv bootargs console=ttyLP0,115200 root=/dev/nfs ip=dhcp nfsroot=192.168.1.3:/tftp/root,v3,tcp"
setenv bootcmd "run loaddtb; run loadkernel; run netargs; booti 0x83000000 - 0x80400000;"
```
