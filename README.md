# cisco-ios
Basic Network Cisco Switch IOS knowledge and configuration

# Table of contents

Configuration
- [Default Switch Configuration](#Default%20Switch%20Configuration)

Switch Management
- [Console Port Security - login](#Console%20Port%20Security%20-%20login)
- [Telnet with an account](#Telnet%20with%20an%20account)


# Default Switch Configuration

Switch
- WS-C3560-24PS
Cisco IOS
- 12.2(37)SE1

```
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


# Console Port Security - login

```
line console 0
password mypassword
login
```

``` pascal
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#line console 0
Switch(config-line)#password mypassword
Switch(config-line)#login
Switch(config-line)#^Z
Switch#
%SYS-5-CONFIG_I: Configured from console by console

Switch#show running-config ?
  |  Output Modifiers
  <cr>
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
-> Console login is in cleartext in the configuration


# Telnet with an account

```
enable secret cisco
username myaccount secret mypassword
line vty 0 15
login local
transport input telnet
```

``` pascal
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
-> Passwords are cyphered but Telnet is a non-secure protocol

