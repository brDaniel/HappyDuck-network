# Edificio 2
[regresar](./HappyDuck-network.MD)
## configuracion del switch

```tcl
enable
conf term
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
```

## R2 configuracion

### Interfaces

```tcl
enable  
conf term
hostname EDIFICIO-2
banner motd #
***************************************************************
ADVERTENCIA: Solo personal authorizado, propiedad de happyDuck
***************************************************************
#
interface se 0/0/1
ip address 192.0.0.5 255.255.255.252
description Enlace a Edificio 3 (Help-desk:1-800-555-1234)
no shutdown
exit
interface se 0/0/0
ip address 192.0.0.2 255.255.255.252
description Enlace a Edificio 1 (Help-desk:1-800-555-1234)
no shutdown
exit
interface se 0/1/1
ip address 192.0.10.1 255.255.255.252
description Enlace a  Corporativo (Help-desk:1-800-555-1234)
no shutdown
exit
interface faste 0/0
description Red de Oficina 3 (Help-desk:1-800-555-1234)
no shutdown
exit
interface faste 0/1
description Red de Oficina 4 (Help-desk:1-800-555-1234)
no shutdown
exit
```

### OFICINA 3

```tcl
interface fastEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.20.1 255.255.255.0
exit
interface fastEthernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.25.1 255.255.255.0
exit
```

```tcl
ip dhcp pool datosOfi3
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
ip dhcp excluded-address 192.168.20.1 192.168.20.10

ip dhcp pool vozOfi3
network 192.168.25.0 255.255.255.0
default-router 192.168.25.1
option 150 ip 192.168.25.1
ip dhcp excluded-address 192.168.25.1 192.168.25.10
```

### OFICINA 4

```tcl
interface fastEthernet 0/1.10
encapsulation dot1Q 10
ip address 128.168.20.1 255.255.255.0
exit
interface fastEthernet 0/1.20
encapsulation dot1Q 20
ip address 128.168.25.1 255.255.255.0
exit
```

```tcl
ip dhcp pool datosOfi4
network 128.168.20.0 255.255.255.0
default-router 128.168.20.1
ip dhcp excluded-address 128.168.20.1 128.168.20.10

ip dhcp pool vozOfi4
network 128.168.25.0 255.255.255.0
default-router 128.168.25.1
option 150 ip 128.168.25.1
ip dhcp excluded-address 128.168.25.1 128.168.25.10
```

### Telephony service

```tlc
telephony-service
max-dn 10
max-ephones 10
ip source-address 192.168.25.1 port 2000
auto assign 1 to 10
exit
ephone-dn 1
number 3001
exit

telephony-service
ip source-address 128.168.25.1 port 2000
ephone-dn 2
number 4001
exit
```

### Enrutamiento

```tcl
route ospf 10
network 192.0.10.0 0.0.0.3 area 0
network 192.0.0.0 0.0.0.3 area 0
network 192.168.20.0 0.0.0.255 area 0
network 192.168.25.0 0.0.0.255 area 0
network 128.168.20.0 0.0.0.255 area 0
network 192.168.25.0 0.0.0.255 area 0
exit

router rip
version 2
network 192.0.0.4
exit
```

#### Redistribucion

```tlc
router ospf 10
redistribute rip metric 30 subnets
redistribute eigrp 10 metric 30 subnets

```

### Dial peer

```tcl
dial-peer voice 1 voip
destination-pattern 100.
session target ipv4:192.0.0.1
exit
dial-peer voice 2 voip
destination-pattern 200.
session target ipv4:192.0.0.1
exit

dial-peer voice 7 voip
destination-pattern 500.
session target ipv4:192.0.0.6
exit
dial-peer voice 8 voip
destination-pattern 600.
session target ipv4:192.0.0.6
exit

dial-peer voice 9 voip
destination-pattern 700.
session target ipv4:192.0.0.13
exit
dial-peer voice 10 voip
destination-pattern 800.
session target ipv4:192.0.0.13
exit
```
[regresar](./HappyDuck-network.MD)
