---
title: Creating my own VPN
published: true
---

*Hello guys, After a long time, I'm here to create another post to how to create a vpn with a vps.*

<img src="https://i.imgur.com/qeeZ3LA.png">

## Select a VPS Service
On this days a lot of vps service are online, I prefere to choose does who are offshore vps services to **avoid the police**(this is for educational only for my safety!!!). If you want a recommendation, buy a vps with unlimited traffic(or 2 TB). And 1Gbps velocity is good. And the other like cpu and ram doesn't matter.


I give a link of my friend of Doxbin who run a site who select a few good vps services. I Choose a debian 11 OS

[OffShoreDoxBin](https://offshore.xbdm.fun)

<img src="https://i.scdn.co/image/ab67616d0000b273855142ad12485b5db91d3224" width="200" height="200">

## Preconfigurations
First of all

``` sh
apt update && apt upgrade -y
```

So, Now is the moment to create a safe environment on our server.

First we need to create a username and put a passwd for the acc.

``` sh
useradd -m GOD
passwd GOD
apt install sudo 
usermod -aG sudo GOD
```

Now we have on sudoers GOD. After that we need to change to GOD to not use a the root acc.

## SSH Like A GOD

### Local Machine
Let's now create the id_rsa key, for this go to our local machine and create the key.

``` sh 
ssh-keygen -t rsa -b 4096

Name of the file: debVpn
PassPhrase: PUTONEPLS https://passwordsgenerator.net/
```

After that, U need to transfer the .pub to the vps. So how we are GigaChads, we transfer that with scp.

``` sh
scp debVpn.pub GOD@IP:
```

### Vps Machine
``` sh
su GOD
cd ~/
mkdir .ssh
cat debVpn.pub >> .ssh/authorized_keys
rm debVpn.pub
```

Now let's to configure ssh config,

``` sh
sudo apt install vim
sudo vi /etc/ssh/sshd_config
```

Change the follow parameters to create a sec ssh(don't forget that the FBI need to launch a 0day to ur ssh)

``` sh
PermitRootLogin no # Root acc is trash
PubkeyAuthentication yes
AuthorizedKeysFile  .ssh/authorized_keys .ssh/authorized_keys2
PasswordAuthentication no
```

After that restart the sshd service

``` sh
sudo systemctl restart sshd
```

And u can now exit from the vps, and probe the new conf.

### Local Machine

Now for the access need to type the follow command.

``` sh
ssh -i .ssh/debVpn GOD@IP
Enter the passphrase for the key: THEPASS
```

### Vps Machine

Also If u want to put a more restrict mode on the ssh I recommend to you touch the follow parameters on the ssh config

``` sh
MaxAuthTries 2 # Limit Tries    
MaxSessions 1 # Limit Sessions
```

Finally let's go to setup our openvpn server.

So First of all, install openvpn

``` sh
sudo apt install openvpn -y
```

After we need to see this git repository who is a beautiful bash code.

``` sh
sudo apt install wget
wget https://raw.githubusercontent.com/angristan/openvpn-install/master/openvpn-install.sh
```

Now execute this .sh with sudo privileges.

``` sh
chmod +x ./openvpn-install.sh
sudo ./openvpn-install.sh
IPV6: n
port: 1194
Protocol: 1 UDP
DNS Resolvers: 11 AdGuard DNS
Compression: n
Encryption: n
Client name: VPN # Name of the .ovpn file
1) Add a passwordless client
```

So after that put our openvpn with 0log, for if the police raid the vps.

``` sh
sudo vi /etc/openvpn/server.conf

verb 0 # Change 3 to 0, this is literally no log.
log-append /dev/null # Add
status /dev/null # Change

sudo systemctl restart openvpn
```

### Local Machine

Now download ur .opvn with sftp.

``` sh
sftp -i .ssh/debVpn GOD@IP
Enter passphrase: putpass

sftp> get VPN.opvn
```

So now we got the .opvn on our local machine xd.

For the connection type the follow command line

``` sh
sudo openvpn --config VPN.ovpn 
```

## TroubleShoot

### DNS Leak

If u have the problem of the DNS Leak, maybe check ur /etc/resolv.conf and put inside of this the follow line

``` sh
nameserver 8.8.8.8 # Change if u have one put
```

So finally u make a **GigaChad vpn service**. So now, relax and still waiting the interpol raiding ur server xd.

**But Pls let to Breached a tribute and for pom too.**

<img src="https://i.imgur.com/QmfKNw6.jpg">
