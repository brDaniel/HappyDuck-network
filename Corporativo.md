# CORPORATIVO
[regresar](./HappyDuck-network.MD)


```tcl
enable
configure terminal
hostname CORPORATIVO
interface se 0/1/1
no shutdown
ip address 192.0.10.2 255.255.255.252
description Enlace a Edificios (Help-desk:1-800-555-1234)
exit



interface fastEthernet 0/1
no shutdown
ip address 200.0.0.1 255.255.255.0
description Corporativo
exit

```

```
route ospf 10
network 192.0.10.2 0.0.0.3 area 0
network 200.0.0.0 0.0.0.3 area 0
exit
```

[regresar](./HappyDuck-network.MD)
