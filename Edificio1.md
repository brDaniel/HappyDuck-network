# Edificio 1 
[regresar](./HappyDuck-network.MD)
## configuracion del switch

```tcl
enable
conf term
hostname OFICINA1
vlan 10
name datos
exit
vlan 20
name voz
interface range fast 0/1-20
switchport mode access
switchport access vlan 10
switchport voice vlan 20
exit
interfa fast 0/24
switchport mode trunk
no shutdown
exit
```

```tcl
enable
conf term
hostname OFICINA2
vlan 10
name datos
exit
vlan 20
name voz
interface range fast 0/1-20
switchport mode access
switchport access vlan 10
switchport voice vlan 20
exit
interfa fast 0/24
switchport mode trunk
no shutdown
exit
```

## R1 configuracion

### Interfaces

```tcl
enable  
conf term
hostname EDIFICIO-1
banner motd #
***************************************************************
ADVERTENCIA: Solo personal authorizado, propiedad de happyDuck
***************************************************************
#
interface se 0/1/0
ip address 192.0.0.17 255.255.255.252
description Enlace a Edificio 3 (Help-desk:1-800-555-1234)
no shutdown
interface se 0/0/1
ip address 192.0.0.14 255.255.255.252
description Enlace a Edificio 2 (Help-desk:1-800-555-1234)
no shutdown
exit
interface se 0/0/0
ip address 192.0.0.1 255.255.255.252
description Enlace a Edificio 4 (Help-desk:1-800-555-1234)
no shutdown
exit
interface faste 0/0
description Red de Oficina  1(Help-desk:1-800-555-1234)
no shutdown
exit
interface faste 0/1
description Red de Oficina 2 (Help-desk:1-800-555-1234)
no shutdown
exit
```

### OFICINA 1

```tcl
interface fastEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit
interface fastEthernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.15.1 255.255.255.0
exit
```

```tcl
ip dhcp pool datosOfi1
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
ip dhcp excluded-address 192.168.10.1 192.168.10.10

ip dhcp pool vozOfi1
network 192.168.15.0 255.255.255.0
default-router 192.168.15.1
option 150 ip 192.168.15.1
ip dhcp excluded-address 192.168.15.1 192.168.15.10
```

### OFICINA 2

```tcl
interface fastEthernet 0/1.10
encapsulation dot1Q 10
ip address 128.168.10.1 255.255.255.0
exit
interface fastEthernet 0/1.20
encapsulation dot1Q 20
ip address 128.168.15.1 255.255.255.0
exit
```

```tcl
ip dhcp pool datosOfi2
network 128.168.10.0 255.255.255.0
default-router 128.168.10.1
ip dhcp excluded-address 128.168.10.1 128.168.10.10

ip dhcp pool vozOfi2
network 128.168.15.0 255.255.255.0
default-router 128.168.15.1
option 150 ip 128.168.15.1
ip dhcp excluded-address 128.168.15.1 128.168.15.10
```

### Telephony service

```tlc
telephony-service
max-dn 10
max-ephones 10
ip source-address 192.168.15.1 port 2000
auto assign 1 to 10
exit
ephone-dn 1
number 1001
exit

telephony-service
ip source-address 128.168.15.1 port 2000
ephone-dn 2
number 2001
exit
```

### Enrutamiento

```tcl
route eigrp 10
network 192.168.10.0 0.0.0.255
network 192.168.15.0 0.0.0.255
network 128.168.10.0 0.0.0.255
network 128.168.15.0 0.0.0.255 
network 192.0.0.12 0.0.0.3
network 192.178.0.0 0.0.0.3
network 192.0.0.16 0.0.0.3
network 192.0.0.0 0.0.0.3
auto-summary

exit

route ospf 10
router ospf 10
network 192.178.0.0 0.0.0.3 area 0
network 192.0.0.16 0.0.0.3 area 0
network 192.0.0.0 0.0.0.3 area 0

route ospf 10
network 192.168.10.0 0.0.0.255 area 0
network 192.168.15.0 0.0.0.255 area 0
network 128.168.10.0 0.0.0.255 area 0
network 128.168.15.0 0.0.0.255 area 0
network 192.0.0.12 0.0.0.3 area 0
network 192.0.0.16 0.0.0.3 area 0
network 192.0.0.0 0.0.0.3 area 0
exit
```

#### Redistribucion

```tcl
router eigrp 10
redistribute rip metric 1658031 514560 255 255 1500
exit 

```

### Access list

> no hay conexion entre las oficinas:
 (1 -> 3),
 (2 -> 6)

```tcl
access-list 1 deny 192.168.20.0 0.0.0.255
access-list 1 deny 192.168.25.0 0.0.0.255
access-list 1 permit any
interface fastEthernet 0/0.10
ip access-group 1 out
exit
interface fastEthernet 0/0.20
ip access-group 1 out
exit
```

```tcl
access-list 2 deny 128.168.30.0 0.0.0.255
access-list 2 deny 128.168.35.0 0.0.0.255
access-list 2 permit any
interface fastEthernet 0/1.10
ip access-group 2 out
exit
interface fastEthernet 0/1.20
ip access-group 2 out
exit
```

### Dial peer

```tcl
dial-peer voice 1 voip
destination-pattern 300.
session target ipv4:192.0.0.2
exit
dial-peer voice 2 voip
destination-pattern 400.
session target ipv4:192.0.0.2
exit

dial-peer voice 3 voip
destination-pattern 500.
session target ipv4:192.0.0.18
exit
dial-peer voice 4 voip
destination-pattern 600.
session target ipv4:192.0.0.18
exit

dial-peer voice 5 voip
destination-pattern 700.
session target ipv4:192.0.0.13
exit
dial-peer voice 6 voip
destination-pattern 800.
session target ipv4:192.0.0.13
exit
```
[regresar](./HappyDuck-network.MD)
