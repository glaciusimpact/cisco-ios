# Cisco IOS XE security checklist

Below is a checklist of measures to strengthen the security of Cisco switches and routers (IOS and IOS XE).

## Security Checklist

- [ ] [1. Deferrence](#1-deferrence)
	- [ ] [1.1 Add a warning banner](#11-add-a-warning-banner)
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
	- [ ] 4.2 Radius access
- [ ] [5. Preventing loops](#5-preventing-loops)
	- [ ] 5.1 Spanning-tree
	- [ ] 5.2 Storm control
- [ ] [6. Limit Reconnaissance techniques]
	- [ ] 6.1 Disable CDP
	- [ ] 6.2 Disable LLDP
- [ ] [7. Denial of Service mitigation]
	- [ ] 7.1 MAC Address Flooding Attack
	- [ ] 7.2 DHCP Snooping
- [ ] [8. VLAN Attack Protection](#8-vlan-attack-protection)
	- [ ] 8.1 VTP mode transparent
	- [ ] 8.2 VLAN hopping
- [ ] [9. Monitoring Protection]
	- [ ] 9.1 SNMPv3

Reference:
- [Cisco IOS Encryption/Hash](#cisco-ios-encryptionhash)

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

There are 2 configurations possible with Console access. This one is the prefered configuration using an account with a more secure option (Type 5) than using only a password crypted using service password-encryption (Type 7 encryption). A 30 seconds timeout is set for security reason.

a) Secure option

Replace "myconsolepassword" with a more secure password. If scrypt (Type 9) is not supported then remove "algorithm-type scrypt" for Type 5 encryption/hash.

``` pascal
username myconsoleaccount algorithm-type scrypt secret myconsolepassword
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
Switch(config)#username myconsoleaccount algorithm-type scrypt secret myconsolepassword
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
username myconsoleaccount secret 9 $9$J19FIAftPZf7c3$kkL9xjX51NpUX13uMcG3XdQE4z2Q.G3NKlAiYtBov9c
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

SSH allows a remote secure access to the device. A hostname of the device has to be set (here SW1 but you can set the one you want) a key must be generated. A domain name, an account and its password must be set also (they can be modified). A timeout of 30 seconds is set but can be modified if needed. Add of course an IP address to a SVI to reach the device if not already set.

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

---

#### 4.2 Radius access

Instead of have several account on every devices an access with a Radius account simplify the management of the accounts. Below is a basic configuration. TACACS+ configuration is another option similar but proprietary (Cisco).

Note that the SSH account created previously can be removed or kept as a backup account in case the connection with the Radius server is no reacheable (this is why there is "aaa authentication login default group radius local").

Warning: as long as the device can make requests to the Radius server local accounts will not be usable for Console and SSH. Only Radius accounts will allow to access the device.

``` pascal
aaa new-model
radius server rad_config
address ipv4 10.0.1.254 auth-port 1812
key mys3cret
exit
aaa authentication login default group radius local
aaa authorization exec default group radius
line vty 0 15
login authentication default
```

<details>
<summary>Output</summary>

``` python
SW1(config)#aaa new-model
SW1(config)#radius server rad_config
SW1(config-radius-server)#address ipv4 10.0.1.254 auth-port 1812
SW1(config-radius-server)#key mys3cret
 WARNING: Command has been added to the configuration using a type 0 password. However, type 0 passwords will soon be deprecated. Migrate to a supported password type
*Feb 15 18:39:23.109: %AAAA-4-CLI_DEPRECATED: WARNING: Command has been added to the configuration using a type 0 password. However, type 0 passwords will soon be deprecated. Migrate to a supported password type
SW1(config-radius-server)#exit
SW1(config)#aaa authentication login default group radius local
SW1(config)#aaa authorization exec default group radius
SW1(config)#line vty 0 15
SW1(config-line)#login authentication default
SW1(config-line)#^Z
SW1#
%SYS-5-CONFIG_I: Configured from console by console

SW1#show running-config
Building configuration...

Current configuration : 2925 bytes
!
version 16.3.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!

[...]

!
aaa new-model
!
aaa authentication login default group radius local 
!
!
aaa authorization exec default group radius
!

[...]

!
radius server rad_config
 address ipv4 10.0.1.254 auth-port 1812
 key mys3cret
radius server 10.0.1.254
 address ipv4 10.0.1.254 auth-port 1812
 key mys3cret
!
!
!
line con 0
 exec-timeout 30 0
!
line aux 0
!
line vty 0 4
 login authentication default
 transport input ssh
line vty 5 15
 login authentication default
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

### 5. Preventing loops

#### 5.1 Spanning-tree

---


Port Security

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

---

### 8. VLAN Attack Protection


#### 8.1 VTP mode transparent

VTP is not recommended any longer by Cisco. It can potentially destroy the VLANs of a switch. If the VTP of a switch is configured in transparent mode it is not vulnerable to a VTP attack or misconfiguration and its VLAN configuration is stored in the running config and not any longer in vlan.dat.

``` pascal
SW1(config)#vtp mode transparent
```

<details>
<summary>Output</summary>

``` python
SW1(config)#vtp mode transparent
Setting device to VTP TRANSPARENT mode.
SW1(config)#
```
</details>

#### 8.2 VLAN hopping

---

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

