# Edificio 3
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

## R3 configuracion

### Interfaces

```tcl
enable  
conf term
hostname EDIFICIO-3
banner motd #
***************************************************************
ADVERTENCIA: Solo personal authorizado, propiedad de happyDuck
***************************************************************
#
interface se 0/0/1
ip address 192.0.0.6 255.255.255.252
description Enlace a Edificio 2 (Help-desk:1-800-555-1234)
no shutdown
exit
interface se 0/0/0
ip address 192.0.0.9 255.255.255.252
description Enlace a Edificio 4 (Help-desk:1-800-555-1234)
no shutdown
exit
interface se 0/1/0
ip address 192.0.0.18 255.255.255.252
description Enlace a Edificio 1 (Help-desk:1-800-555-1234)
no shutdown
exit
interface faste 0/0
description Red de Oficina 5 (Help-desk:1-800-555-1234)
no shutdown
exit
interface faste 0/1
description Red de Oficina 6 (Help-desk:1-800-555-1234)
no shutdown
exit
```

### OFICINA 5

```tcl
interface fastEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.30.1 255.255.255.0
exit
interface fastEthernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.35.1 255.255.255.0
exit
```

```tcl
ip dhcp pool datosOfi5
network 192.168.30.0 255.255.255.0
default-router 192.168.30.1
ip dhcp excluded-address 192.168.30.1 192.168.30.10

ip dhcp pool vozOfi5
network 192.168.35.0 255.255.255.0
default-router 192.168.35.1
option 150 ip 192.168.35.1
ip dhcp excluded-address 192.168.35.1 192.168.35.10
```

### OFICINA 6

```tcl
interface fastEthernet 0/1.10
encapsulation dot1Q 10
ip address 128.168.30.1 255.255.255.0
exit
interface fastEthernet 0/1.20
encapsulation dot1Q 20
ip address 128.168.35.1 255.255.255.0
exit
```

```tcl
ip dhcp pool datosOfi6
network 128.168.30.0 255.255.255.0
default-router 128.168.30.1
ip dhcp excluded-address 128.168.30.1 128.168.30.10

ip dhcp pool vozOfi6
network 128.168.35.0 255.255.255.0
default-router 128.168.35.1
option 150 ip 128.168.35.1
ip dhcp excluded-address 128.168.35.1 128.168.35.10
```

### Telephony service

```tlc
telephony-service
max-dn 10
max-ephones 10
ip source-address 192.168.35.1 port 2000
auto assign 1 to 10
exit
ephone-dn 1
number 5001
exit

telephony-service
ip source-address 128.168.35.1 port 2000
ephone-dn 2
number 6001
exit
```

### Enrutamiento

```tcl
route osp 10
network 192.168.30.0 0.0.0.3 area 0
network 192.168.35.0 0.0.0.3 area 0
network 128.168.30.0 0.0.0.3 area 0
network 128.168.35.0 0.0.0.3 area 0
network 192.0.0.8 0.0.0.3 area 0
network 192.0.0.4 0.0.0.3 area 0
network 192.0.0.16 0.0.0.3 area 0
exit

route BGP 300
network 192.0.0.8 mask 255.255.255.252
neighbor 192.0.0.10 remote-as 400
exit

router rip
version 2
network 192.0.0.4
exit

```

#### Redistribucion

```tcl
router ospf 10
redistribute bgp 300
redistribute rip metric 10 subnets
redistribute eigrp 10 metric 10 subnets
exit
router bgp 400
resitribute ospf 10
exit
router rip
resitribute ospf metric 2
exit
```

### Dial peer

```tcl
dial-peer voice 3 voip
destination-pattern 100.
session target ipv4:192.0.0.17
exit
dial-peer voice 4 voip
destination-pattern 200.
session target ipv4:192.0.0.17
exit

dial-peer voice 7 voip
destination-pattern 300.
session target ipv4:192.0.0.5
exit
dial-peer voice 8 voip
destination-pattern 400.
session target ipv4:192.0.0.5
exit

dial-peer voice 11 voip
destination-pattern 700.
session target ipv4:192.0.0.10
exit
dial-peer voice 12 voip
destination-pattern 800.
session target ipv4:192.0.0.10
exit
```
[regresar](./HappyDuck-network.MD)
