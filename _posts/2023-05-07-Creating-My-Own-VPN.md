---
title: Creating my own VPN
published: true
---

*Hello guys, After a long time, I am here to create another post on how to create a VPN with a VPS.*

<img src="https://i.imgur.com/qeeZ3LA.png">

## Select a VPS Service
These days we have a lot of Hosting Services, and we can choose those that have the requirements we need.
In this case, for the deployment of a vpn service, we need to choose a hosting that can provide unlimit bandwith if is posible due to we don't know how much traffic can be transferred on our daily work.
So my recommendations are to select a hosting who can provide at minimun of 2TB of bandwith. 
I proportion a web-page of OffShore VPS to rent. In my recommendation I Choose a debian 11 OS.

[OffShoreDoxBin](https://xbdmhq.github.io/Offshore.CAT-Reworked/)

<img src="https://i.scdn.co/image/ab67616d0000b273855142ad12485b5db91d3224" width="200" height="200">

## Preconfigurations
First of all

``` sh
apt update && apt upgrade -y
```

So,  now is the moment to create a safe environment on the server.

First we need to create a username & a secure password for the new account.

``` sh
useradd -d /home/Anthony -m -s /bin/bash Anthony
passwd Anthony
apt install sudo 
usermod -aG sudo Anthony # Inserting Anthony into the SUDOERS group.
```

Now Anthony is a SUDOER User. After that we need to avoid to use the root account.

## SSH Like A Anthony

### Local Machine
Let's now create the id_rsa key, for this go to our local machine and create the key.

``` sh 
ssh-keygen -t rsa -b 4096

Name of the file: debVpn
PassPhrase: Generate a password with https://passwordsgenerator.net/
```

After that, You need to transfer the .pub to the vps. So now we need to transfer the .pub to the VPS, so one of the best options is scp.

``` sh
scp debVpn.pub Anthony@IP:
```

### Vps Machine
``` sh
su Anthony
cd ~/
mkdir .ssh
cat debVpn.pub >> .ssh/authorized_keys
rm debVpn.pub
```

Now we need to config the ssh service.

``` sh
sudo apt install vim
sudo vi /etc/ssh/sshd_config
```

Change the follow parameters to create a secure ssh config.

``` sh
PermitRootLogin no # Avoid the root account
PubkeyAuthentication yes
AuthorizedKeysFile  .ssh/authorized_keys .ssh/authorized_keys2
PasswordAuthentication no
```

After change those lines, we need to restart the sshd service.

``` sh
sudo systemctl restart sshd
```

After that the connection we have on the terminal are not going to drop.
Now we need to check that all is working fine, so we open a new terminal leaving the other alive, and we try to connect to see if all is good.

### Local Machine

Now to access we need to use the private key that we create in the past.

``` sh
ssh -i .ssh/debVpn Anthony@IP
Enter the passphrase for the key: PASSWORD
```

### VPS Machine

Also If you want to improve the security of the ssh I recommend to you modify the following parameters of the ssh config.

``` sh
MaxAuthTries 2 # Limit Tries    
MaxSessions 1 # Limit Sessions
```

Finally let's go to setup our openvpn server.

So First of all, install openvpn package.

``` sh
sudo apt install openvpn -y
```

After we need to install the wget package to then download the openvpn-install bash code.

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
Encryption: n (Change to "y" if you want to increase the bits of the encryption)
Client name: VPN # Name of the .ovpn file
1) Add a passwordless client
```
Ok, right now we have the ovpn configured and a .ovpn file.

On the next step, we need to configure the vpn with 0 log policy.

``` sh
sudo vi /etc/openvpn/server.conf

verb 0 # Default on 3, we change it to 0 to avoud the verbose on the log file.
log-append /dev/null # Add.
status /dev/null # Change

sudo systemctl restart openvpn
```

### Local Machine

Now download your .opvn using sftp.

``` sh
sftp -i .ssh/debVpn Anthony@IP
Enter passphrase: Password

sftp> get VPN.opvn
```

Now we got the .opvn on our local machine.

For establish the connection we need to run the following command:
``` sh
sudo openvpn --config VPN.ovpn 
```

## TroubleShoot

### DNS Leak
To prevent the dns leak we need to check the /etc/resolv.conf and add/modify with the following line.

``` sh
nameserver 1.1.1.1
```

With that we establish a permanent DNS server.

So after that the VPN is a working fine with a secure configuration. This config have a 128b encryption, that can be modify on the openvpn configure step, in the Encryption section.
We can change the "n" to "y" and we have the posibility to select the encryption to use and how many bits.

Signed By:
- d4rpell
