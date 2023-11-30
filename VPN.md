# VPN

[regresar](./HappyDuck-network.MD)

## EDIFICIO 1

```tcl
enable
configure terminal
interface Serial 0/1/1
no shutdown
ip address 192.178.0.1 255.255.255.252
description Enlace a Edificio-Externo (Help-desk:1-800-555-1234)
encapsulation frame-relay
exit

router ospf 10
network 192.178.0.0 0.0.0.3 area 0
exit

```

```tcl
crypto isakmp policy 10
authentication pre-share
hash sha
encryption aes 256
group 2
lifetime 86400
exit

crypto isakmp key toor address 192.178.0.2
crypto ipsec transform-set TSET esp-aes esp-sha-hmac
access-list 101 permit ip 192.168.10.0 0.0.0.255 200.10.0.0 0.0.0.255
access-list 101 permit ip 128.168.10.0 0.0.0.255 200.10.0.0 0.0.0.255


crypto map CMAP 10 ipsec-isakmp
set peer 192.178.0.2
match address 101
set transform-set TSET
exit

interface se0/1/1
crypto map CMAP
exit
```
### NAT

```tcl
access-list 5 permit 192.168.10.0 0.0.0.255
access-list 5 permit 128.168.10.0 0.0.0.255
ip nat inside source list 5 interface se 0/1/1
interface fast 0/0.10
ip nat inside
exit
interface fast 0/0.20
ip nat inside
exit
interf se 0/1/1
ip nat outside
exit
```

## EDIFICO EXTERNO

```tcl
enable
configure terminal
hostname home
interface Serial 0/1/1
no shutdown
ip address 192.178.0.2 255.255.255.252
description Enlace a Edificio-1 (Help-desk:1-800-555-1234)
encapsulation frame-relay
exit

interface fastEthernet 0/0
no shutdown
ip address 200.10.0.1 255.255.255.0
description Home-office
exit

ip dhcp pool datosHome
network 200.10.0.0 255.255.255.0
default-router 200.10.0.1
ip dhcp excluded-address 200.10.0.1 200.10.0.10

router ospf 10
network 200.10.0.0 0.0.0.255 area 0
network 192.178.0.0 0.0.0.3 area 0
exit
```

```tcl
crypto isakmp policy 10
authentication pre-share
hash sha
encryption aes 256
group 2
lifetime 86400
exit

crypto isakmp key toor address 192.178.0.1
crypto ipsec transform-set TSET esp-aes esp-sha-hmac
access-list 101 permit ip 200.10.0.0 0.0.0.255 192.168.10.0 0.0.0.255
access-list 101 permit ip 200.10.0.0 0.0.0.255 128.168.10.0 0.0.0.255


crypto map CMAP 10 ipsec-isakmp
set peer 192.178.0.1
match address 101
set transform-set TSET
exit

interface se0/1/1
crypto map CMAP
exit
```

[regresar](./HappyDuck-network.MD)
