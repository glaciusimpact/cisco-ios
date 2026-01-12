# Cisco IOS XE

Basic Network Cisco Switch IOS XE knowledge and configuration.


## Table of contents

- [Configuration](#Configuration)
	- [Default Switch Configuration](#default-switch-configuration)
	- Basic Setup
	- [Erase Configuration](erase-configuration)

- [Switch Management](#Switch-Management)
	- [Console Port Security - login](#console-port-security---login)
	- [Telnet access with an account](#telnet-access-with-an-account)
	- SSH access

- [Security](#Security)
	- [Security Checklist](#security-checklist)
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

## Console Port Security - login

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
	- [ ] Enable SSH
	- [ ] Disable Telnet
	- [ ] Disable HTTP
	- [ ] Secure Console Password
	- [ ] Secure SSH local user password
- [ ] Other protocols
	- [ ] SNMPv3
- [ ] Discovery protocols
	- [ ] Disable CDP
	- [ ] Disable LLDP
- [ ] DHCP Snooping
- [ ] Port Security
#### Enable secure management access

*Enable secret*

```
enable secret mysecret
```

You should get something like this in the running-configuration

``` python
enable secret 5 $1$mERr$/x9VUDEedbClBAt8DhbGj0
```

*Disable enable password*

```
no enable password
```

*Enable SSH*

```

```
