# Cisco IOS XE

Here is a checklist to secure a Cisco Switch IOS XE.



## Security Checklist

- [ ] [1. Deferrence](#1-deferrence)
	- [ ] 1.1 Add a warning banner
- [ ] [2. Attack Surface Reduction](#2-attack-surface-reduction)
	- [ ] 2.1 Disable Telnet
	- [ ] 2.2 Disable Web interfaces
	- [ ] 2.3 Disable FTP
- [ ] [3. Secure local management access](#3-secure-local-management-access)
	- [ ] 3.1 Use enable secret
	- [ ] 3.2 Disable enable password
	- [ ] 3.3 Secure Console access
- [ ] [4. Secure remote management access](#4-secure-remote-management-access)
	- [ ] 4.1 Enable and setup SSH
	- [ ] 4.2 Secure SSH local user Password
	- or
	- [ ] 4.3 Radius access


---

### 1. Deferrence

#### 1.1 Add a warning banner

The warning banner attempts to deter unauthorized individuals from logging into a device and informs them that their activity will be logged.

a) First banner

This banner appears before login for remote connections (SSH).

``` pascal
banner login $
###############################################################

WARNING: This is a private device. This service is restricted
to authorized users only. All connections are monitored and
recorded. Disconnect IMMEDIATELY if you are not an authorized
user! Unauthorized access will be fully investigated and
reported to the appropriate law enforcement agencies.

###############################################################$
```

b) Second banner

This banner appears before login for console and after login for remote connections (SSH).

``` pascal
banner motd $
###############################################################

WARNING: This is a private device. This service is restricted
to authorized users only. All connections are monitored and
recorded. Disconnect IMMEDIATELY if you are not an authorized
user! Unauthorized access will be fully investigated and
reported to the appropriate law enforcement agencies.

###############################################################$
```

---

### 2. Attack Surface Reduction

#### 2.1 Disable Telnet

Telnet is an insecure protocol and should no longer be used in production.

``` pascal
line vty 0 15
transport input ssh
```

#### 2.2 Disable Web interfaces

HTTP is an insecure protocol and should no longer be used in production.

``` pascal
no ip http server
no ip http secure-server
```

#### 2.3 Disable FTP

FTP is an insecure protocol and should no longer be used in production.

``` pascal
no ftp-server enable
```

---

### 3. Secure local management access

#### 3.1 Use enable secret

Enable secret command sets a encrypted/hashed password to access the enable mode when someone is in user mode.

(change "mysecret" with a more robust password)
```
enable secret mysecret
```

<details>
<summary>Output</summary>

You should get something like this in the running-configuration.

``` python
enable secret 5 $1$mERr$/x9VUDEedbClBAt8DhbGj0
```
</details>

#### 3.2 Disable enable password

In case a clear password was set for reaching enable mode this command will remove it.

```
no enable password
```

#### 3.3 Secure Console access

There are 2 configurations possible with Console access. This one is the prefered configuration using an account with a more secure option (Type 5) than using a password crypted using service password-encryption (Type 7 encryption). A 30 seconds timeout is set for security reason.

a) Secure option

Replace "myconsolepassword" with a more secure password.

``` pascal
username myconsoleaccount secret myconsolepassword
line console 0
no password
login local
exec-timeout 30
```

<details>
<summary>Output</summary>

``` python
Switch>en
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#username myconsoleaccount secret myconsolepassword
Switch(config)#line console 0
Switch(config-line)#no password
Switch(config-line)#login local
Switch(config-line)#exec-timeout 30
Switch(config-line)#^Z
Switch#
%SYS-5-CONFIG_I: Configured from console by console

Switch#show running-config 

[...]

!
username myconsoleaccount secret 5 $1$mERr$XzhDH/exGgLcaXa4fSWcG0
!

[...]

!
line con 0
 exec-timeout 30 0
 login local
!

[...]
```
</details>

b) Less secure option (Refrain from using)

Just for information here is the other Console access configuration but less secure. Prefer the more secure option.

```
line console 0
password 123456
login
exec-timeout 30
exit
service password-encryption
```

<details>
<summary>Output</summary>

``` python
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#line console 0
Switch(config-line)#password 123456
Switch(config-line)#login
Switch(config-line)#exec-timeout 30
Switch(config-line)#do sh run | i password
no service password-encryption
 password 123456
Switch(config-line)#exit
Switch(config)#service password-encryption
Switch(config)#do sh run | i password
service password-encryption
 password 7 08701E1D5D4C53
Switch(config)#
```
</details>

---

### 4. Secure remote management access

#### 4.1 Enable and setup SSH

SSH allows a remote secure access to the device. A hostname of the device has to be set (here SW1 but you can set the one you want) a key must be generated. A domain name, an account and its password must be set also (they can be modified). A timeout of 30 seconds is set but can be modified if needed.

``` pascal
hostname SW1
ip domain name mydomain.com
crypto key generate rsa

username myaccount secret mypassword
ip ssh version 2

line vty 0 15
login local
transport input ssh
exec-timeout 30
```

<details>
<summary>Output</summary>

``` python
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname SW1
SW1(config)#ip domain name mydomain.com
SW1(config)#crypto key generate rsa
The name for the keys will be: SW1.mydomain.com
Choose the size of the key modulus in the range of 360 to 4096 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 4096
% Generating 4096 bit RSA keys, keys will be non-exportable...[OK]

SW1(config)#
SW1(config)#username myaccount secret mypassword
*Mar 1 6:28:51.334: %SSH-5-ENABLED: SSH 1.99 has been enabled
SW1(config)#ip ssh version 2
SW1(config)#line vty 0 15
SW1(config-line)#login local
SW1(config-line)#transport input ssh
SW1(config-line)#^Z
SW1#
%SYS-5-CONFIG_I: Configured from console by console

SW1#show running-config 
Building configuration...

Current configuration : 1555 bytes
!
version 16.3.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname SW1
!
!
no profinet
!
!
!
!
!
!
no ip cef
no ipv6 cef
!
!
!
username myaccount secret 5 $1$mERr$7sOd0mgRuXYhHwfWsV4QZ/
!
!
!
!
!
!
!
!
!
!
ip ssh version 2
no ip domain-lookup
ip domain-name mydomain.com
!
!

[...]

!
line vty 0 4
 login local
 transport input ssh
line vty 5 15
 login local
 transport input ssh
!
!
!
!
end


SW1#
```
</details>








----





### Security Checklist

- [ ] Secure management access
	- [ ] Use enable secret
	- [ ] Disable enable password
	- [ ] Enable and setup SSH
	- [ ] Disable Telnet
	- [ ] Disable Web interfaces
	- [ ] Disable FTP
	- [ ] Secure Console access
	- [ ] Secure SSH local user Password
	- [ ] Radius access
- [ ] Various protocols
	- [ ] SNMPv3
	- [ ] Disable CDP
	- [ ] Disable LLDP
- [ ] Spanning-tree
- [ ] Port Security
- [ ] Storm control
- [ ] DHCP Snooping




---

## Table of contents

- [Security](#Security)

- [Configuration](#Configuration)
	- [Default Switch Configuration](#default-switch-configuration)
	- Basic Setup
	- [Erase Configuration](#erase-configuration)

- [Switch Management](#Switch-Management)
	- [Console Port password protection](#console-port-password-protection)
	- [Telnet access with an account](#telnet-access-with-an-account)
	- SSH access

- [Security](#Security)
	- [Security Checklist](#security-checklist)
	- [Cisco IOS Encryption/Hash](#cisco-ios-encryptionhash)
	- Access Security
	- Password Security
	- Protocol Security
	- L2 Network Attack Security
	- L3 Network Attack Security

## Configuration

### Default Switch Configuration

---
#### Switch Model

|                      |               |
| -------------------- | ------------- |
| Switch Model         | WS-C3650-24PS |
| Cisco IOS XE version | 16.3.2        |
| Number of ports      | 24+4          |

*Show switch model and software version*

```
show version
```

<details>
<summary>Output</summary>

``` python
Switch#show version
Cisco IOS Software [Denali], Catalyst L3 Switch Software (CAT3K_CAA-UNIVERSALK9-M), Version 16.3.2, RELEASE SOFTWARE (fc4)
Technical Support : http://www.cisco.com/techsupport
Copyright(c) 1986 - 2016 by Cisco Systems, Inc.
Compiled Tue 08 - Nov - 16 17:31 by pt_team


Cisco IOS-XE software, Copyright(c) 2005 - 2016 by cisco Systems, Inc.
All rights reserved.Certain components of Cisco IOS - XE software are
licensed under the GNU General Public License("GPL") Version 2.0.The
software code licensed under GPL Version 2.0 is free software that comes
with ABSOLUTELY NO WARRANTY.You can redistribute and / or modify such
GPL code under the terms of GPL Version 2.0.For more details, see the
documentation or "License Notice" file accompanying the IOS - XE software,
or the applicable URL provided on the flyer accompanying the IOS - XE
software.


ROM: IOS-XE ROMMON
BOOTLDR: CAT3K_CAA Boot Loader (CAT3K_CAA-HBOOT-M) Version 4.26, RELEASE SOFTWARE (P)

test uptime is 7 hours, 33 minutes
Uptime for this control processor is 7 hours, 36 minutes
System returned to ROM by Power Failure
System image file is "flash:/cat3k_caa-universalk9.16.03.02.SPA.bin"
Last reload reason : Power Failure


This product contains cryptographic features and is subject to United
States and local country laws governing import, export, transfer and
use. Delivery of Cisco cryptographic products does not imply
third-party authority to import, export, distribute or use encryption.
Importers, exporters, distributors and users are responsible for
compliance with U.S. and local country laws. By using this product you
agree to comply with applicable laws and regulations. If you are unable
to comply with U.S. and local laws, return this product immediately.

A summary of U.S. laws governing Cisco cryptographic products may be found at:
http://www.cisco.com/wwl/export/crypto/tool/stqrg.html

If you require further assistance please contact us by sending email to
export@cisco.com.



Technology Package License Information :

---------------------------------------------------------------- -
Technology - package                   Technology - package
Current             Type             Next reboot
------------------------------------------------------------------
ipservicesk9        Permanent        ipservicesk9

cisco WS-C3650-24PS (MIPS) processor (revision N0) with 865815K/6147K bytes of memory.
Processor board ID FDO2031E1Q6
1 Virtual Ethernet interface
28 Gigabit Ethernet/IEEE 802.3 interface(s)
2048K bytes of non-volatile configuration memory.
4194304K bytes of physical memory.
250456K bytes of Crash Files at crashinfo : .
1609272K bytes of Flash at flash : .
0K bytes of  at webui : .

Base ethernet MAC Address       : 00:0C:CF:4B:0C:A1
Motherboard assembly number     : 73-15899-06
Motherboard serial number       : FDO20311WHP
Model revision number           : N0
Motherboard revision number     : A0
Model number                    : WS-C3650-24PS
System serial number            : FDO2031Q0TD



Switch   Ports  Model              SW Version              SW Image              Mode
------   -----  -----              ----------              ----------            ----
*    1   28     WS-C3650-24PS      16.3.2                  CAT3K_CAA-UNIVERSALK9 BUNDLE

Configuration register is 0x102


Switch#
``` 
</details>

#### Default running configuration

*Show configuration*

```
show running-config
```

<details>
<summary>Output</summary>

``` python
Switch#show running-config 
Building configuration...

Current configuration : 1136 bytes
!
version 12.2(37)SE1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Switch
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
 no ip address
 shutdown
!
ip classless
!
ip flow-export version 9
!
!
!
!
!
!
!
!
line con 0
!
line aux 0
!
line vty 0 4
 login
!
!
!
!
end


Switch#
```
</details>

#### Default Interface status

*Show interface status*

```
show interfaces status
```

<details>
<summary>Output</summary>

``` python
Switch#show interfaces status 
Port      Name               Status       Vlan       Duplex  Speed Type
Fa0/1                        notconnect   1          auto    auto  10/100BaseTX
Fa0/2                        notconnect   1          auto    auto  10/100BaseTX
Fa0/3                        notconnect   1          auto    auto  10/100BaseTX
Fa0/4                        notconnect   1          auto    auto  10/100BaseTX
Fa0/5                        notconnect   1          auto    auto  10/100BaseTX
Fa0/6                        notconnect   1          auto    auto  10/100BaseTX
Fa0/7                        notconnect   1          auto    auto  10/100BaseTX
Fa0/8                        notconnect   1          auto    auto  10/100BaseTX
Fa0/9                        notconnect   1          auto    auto  10/100BaseTX
Fa0/10                       notconnect   1          auto    auto  10/100BaseTX
Fa0/11                       notconnect   1          auto    auto  10/100BaseTX
Fa0/12                       notconnect   1          auto    auto  10/100BaseTX
Fa0/13                       notconnect   1          auto    auto  10/100BaseTX
Fa0/14                       notconnect   1          auto    auto  10/100BaseTX
Fa0/15                       notconnect   1          auto    auto  10/100BaseTX
Fa0/16                       notconnect   1          auto    auto  10/100BaseTX
Fa0/17                       notconnect   1          auto    auto  10/100BaseTX
Fa0/18                       notconnect   1          auto    auto  10/100BaseTX
Fa0/19                       notconnect   1          auto    auto  10/100BaseTX
Fa0/20                       notconnect   1          auto    auto  10/100BaseTX
Fa0/21                       notconnect   1          auto    auto  10/100BaseTX
Fa0/22                       notconnect   1          auto    auto  10/100BaseTX
Fa0/23                       notconnect   1          auto    auto  10/100BaseTX
Fa0/24                       notconnect   1          auto    auto  10/100BaseTX
Gig0/1                       notconnect   1          auto    auto  10/100BaseTX
Gig0/2                       notconnect   1          auto    auto  10/100BaseTX

Switch#
```
</details>

#### Default VLANs

*Show VLAN status*

```
show vlan
```

<details>
<summary>Output</summary>

``` python
Switch#show vlan 

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1, Gig0/2
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0   
1003 tr    101003     1500  -      -      -        -    -        0      0   
1004 fdnet 101004     1500  -      -      -        ieee -        0      0   
1005 trnet 101005     1500  -      -      -        ibm  -        0      0   

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------

Remote SPAN VLANs
------------------------------------------------------------------------------

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------
Switch#
```
</details>

#### Default L3 interfaces

Show L3 interfaces status

```
show ip interface brief
```

<details>
<summary>Output</summary>

``` python
Switch#show ip interface brief 
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet1/0/1   unassigned      YES unset  down                  down 
GigabitEthernet1/0/2   unassigned      YES unset  down                  down 
GigabitEthernet1/0/3   unassigned      YES unset  down                  down 
GigabitEthernet1/0/4   unassigned      YES unset  down                  down 
GigabitEthernet1/0/5   unassigned      YES unset  down                  down 
GigabitEthernet1/0/6   unassigned      YES unset  down                  down 
GigabitEthernet1/0/7   unassigned      YES unset  down                  down 
GigabitEthernet1/0/8   unassigned      YES unset  down                  down 
GigabitEthernet1/0/9   unassigned      YES unset  down                  down 
GigabitEthernet1/0/10  unassigned      YES unset  down                  down 
GigabitEthernet1/0/11  unassigned      YES unset  down                  down 
GigabitEthernet1/0/12  unassigned      YES unset  down                  down 
GigabitEthernet1/0/13  unassigned      YES unset  down                  down 
GigabitEthernet1/0/14  unassigned      YES unset  down                  down 
GigabitEthernet1/0/15  unassigned      YES unset  down                  down 
GigabitEthernet1/0/16  unassigned      YES unset  down                  down 
GigabitEthernet1/0/17  unassigned      YES unset  down                  down 
GigabitEthernet1/0/18  unassigned      YES unset  down                  down 
GigabitEthernet1/0/19  unassigned      YES unset  down                  down 
GigabitEthernet1/0/20  unassigned      YES unset  down                  down 
GigabitEthernet1/0/21  unassigned      YES unset  down                  down 
GigabitEthernet1/0/22  unassigned      YES unset  down                  down 
GigabitEthernet1/0/23  unassigned      YES unset  down                  down 
GigabitEthernet1/0/24  unassigned      YES unset  down                  down 
GigabitEthernet1/1/1   unassigned      YES unset  down                  down 
GigabitEthernet1/1/2   unassigned      YES unset  down                  down 
GigabitEthernet1/1/3   unassigned      YES unset  down                  down 
GigabitEthernet1/1/4   unassigned      YES unset  down                  down 
Vlan1                  unassigned      YES unset  administratively down down
Switch#
```
</details>

###  Basic Setup



### Erase Configuration

```
write erase
```

> [!NOTE]
> Instead of typing `write erase` we can type 2 other commands to erase the switch configuration:
> - `erase startup-config`
> - `erase nvram:`

---

# Switch Management

## Console Port password protection

```
line console 0
password mypassword
login
```

<details>
<summary>Output</summary>

``` python
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#line console 0
Switch(config-line)#password mypassword
Switch(config-line)#login
Switch(config-line)#^Z
Switch#
%SYS-5-CONFIG_I: Configured from console by console

Switch#show running-config 
Building configuration...

[...]
!
!
line con 0
 password mypassword
 login
!
line aux 0
!
line vty 0 4
 login
!
!
!
!
end


Switch#
```
</details>

> [!WARNING]
> 
> Console login is in cleartext in the configuration.

## Telnet access with an account

```
enable secret cisco
username myaccount secret mypassword
line vty 0 15
login local
transport input telnet
```

<details>
<summary>Output</summary>

``` bash
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#enable secret cisco
Switch(config)#username myaccount secret mypassword
Switch(config)#line vty 0 15
Switch(config-line)#login local
Switch(config-line)#transport input telnet
Switch(config-line)#^Z
Switch#
%SYS-5-CONFIG_I: Configured from console by console
Switch#show running-config 
Building configuration...

Current configuration : 1355 bytes
!
version 12.2(37)SE1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Switch
!
!
enable secret 5 $1$mERr$hx5rVt7rPNoS4wqbXKX7m0
!
!
!
!
!
!
!
!
username myaccount secret 5 $1$mERr$7sOd0mgRuXYhHwfWsV4QZ/
!
!
[...]
!
line vty 0 4
 login local
 transport input telnet
line vty 5 15
 login local
 transport input telnet
!
!
!
!
end


Switch#
```
</details>

> [!WARNING]
> 
> Passwords are encrypted but Telnet is a non-secure protocol. Do not use Telnet if security is needed.


## Security

### Security Checklist

- [ ] Secure management access
	- [ ] Use enable secret
	- [ ] Disable enable password
	- [ ] Enable and setup SSH
	- [ ] Disable Telnet
	- [ ] Disable Web interfaces
	- [ ] Disable FTP
	- [ ] Secure Console access
	- [ ] Secure SSH local user Password
	- [ ] Radius access
- [ ] Various protocols
	- [ ] SNMPv3
	- [ ] Disable CDP
	- [ ] Disable LLDP
- [ ] Spanning-tree
- [ ] Port Security
- [ ] Storm control
- [ ] DHCP Snooping

#### Secure management access

*Use enable secret*

-> hide the enable password
```
enable secret mysecret
```

<details>
<summary>Output</summary>

You should get something like this in the running-configuration

``` python
enable secret 5 $1$mERr$/x9VUDEedbClBAt8DhbGj0
```
</details>

*Disable enable password*

```
no enable password
```

*Enable and setup SSH*

```
hostname SW1
ip domain name mydomain.com
crypto key generate rsa

username myaccount secret mypassword
ip ssh version 2

line vty 0 15
login local
transport input ssh
exec-timeout 30
```

<details>
<summary>Output</summary>

``` python
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname SW1
SW1(config)#ip domain name mydomain.com
SW1(config)#crypto key generate rsa
The name for the keys will be: SW1.mydomain.com
Choose the size of the key modulus in the range of 360 to 4096 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 4096
% Generating 4096 bit RSA keys, keys will be non-exportable...[OK]

SW1(config)#
SW1(config)#username myaccount secret mypassword
*Mar 1 6:28:51.334: %SSH-5-ENABLED: SSH 1.99 has been enabled
SW1(config)#ip ssh version 2
SW1(config)#line vty 0 15
SW1(config-line)#login local
SW1(config-line)#transport input ssh
SW1(config-line)#^Z
SW1#
%SYS-5-CONFIG_I: Configured from console by console

SW1#show running-config 
Building configuration...

Current configuration : 1555 bytes
!
version 16.3.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname SW1
!
!
no profinet
!
!
!
!
!
!
no ip cef
no ipv6 cef
!
!
!
username myaccount secret 5 $1$mERr$7sOd0mgRuXYhHwfWsV4QZ/
!
!
!
!
!
!
!
!
!
!
ip ssh version 2
no ip domain-lookup
ip domain-name mydomain.com
!
!

[...]

!
line vty 0 4
 login local
 transport input ssh
line vty 5 15
 login local
 transport input ssh
!
!
!
!
end


SW1#
```
</details>

*Disable Telnet*

```
line vty 0 15
transport input ssh
```

*Disable Web interfaces*

```
no ip http server
no ip http secure-server
```

*Disable FTP*

```
no ftp-server enable
```

*Secure Console access*

(prefered configuration)

``` pascal
username myconsoleaccount secret myconsolepassword
line console 0
no password
login local
exec-timeout 30
```

<details>
<summary>Output</summary>

``` python
Switch>en
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#username myconsoleaccount secret myconsolepassword
Switch(config)#line console 0
Switch(config-line)#no password
Switch(config-line)#login local
Switch(config-line)#exec-timeout 30
Switch(config-line)#^Z
Switch#
%SYS-5-CONFIG_I: Configured from console by console

Switch#show running-config 

[...]

!
username myconsoleaccount secret 5 $1$mERr$XzhDH/exGgLcaXa4fSWcG0
!

[...]

!
line con 0
 exec-timeout 30 0
 login local
!

[...]
```
</details>

(other Console access configuration but less secure)

```
line console 0
password 123456
login
exec-timeout 30
exit
service password-encryption
```

<details>
<summary>Output</summary>

``` python
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#line console 0
Switch(config-line)#password 123456
Switch(config-line)#login
Switch(config-line)#exec-timeout 30
Switch(config-line)#do sh run | i password
no service password-encryption
 password 123456
Switch(config-line)#exit
Switch(config)#service password-encryption
Switch(config)#do sh run | i password
service password-encryption
 password 7 08701E1D5D4C53
Switch(config)#
```
</details>

 *Secure SSH local user Password*

Replace all user accounts using "password" with "secret".

#### Various protocols

*SNMPv3*






Various protocols
	- [ ] SNMPv3
	- [ ] Disable CDP
	- [ ] Disable LLDP



*Port Security*

``` pascal
interface FastEthernet0/1
 switchport mode access
 switchport port-security
!
interface FastEthernet0/2
 switchport mode trunk
 switchport port-security
 switchport port-security maximum 8
```

















### Cisco IOS Encryption/Hash

| Type | Cisco Encryption/Hash                       | Ability to crack | NSA recommendations (2022)                                                     |
| :--: | ------------------------------------------- | :--------------: | :----------------------------------------------------------------------------- |
|  0   | Clear                                       |    Immediate     | $${\color{red}Do \space not \space use}$$                                      |
|  4   | SHA-256                                     |       Easy       | $${\color{red}Do \space not \space use}$$                                      |
|  5   | MD5                                         |      Medium      | Not NIST approved, use only when Types 6, 8, and 9 are not available           |
|  6   | AES-128 Reversible Encryption               |    Difficult     | Use only when reversible encryption is needed, or when Type 8 is not available |
|  7   | Encrypted (Reversible Vigenere obfuscation) |    Immediate     | $${\color{red}Do \space not \space use}$$                                      |
|  8   | SHA-256                                     |    Difficult     | $${\color{green}Recommended}$$                                                 |
|  9   | Scrypt                                      |    Difficult     | Not NIST approved                                                              |

- Links:
	- NSA Best practice
https://media.defense.gov/2022/Feb/17/2002940795/-1/-1/1/CSI_CISCO_PASSWORD_TYPES_BEST_PRACTICES_20220217.PDF
	- Cisco Type 7 Password Decrypt / Decoder / Crack online Tool
https://www.firewall.cx/cisco/cisco-routers/cisco-type7-password-crack.html

