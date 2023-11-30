# Seguriad AAA

[regresar](./HappyDuck-network.MD)

```tcl
enable
conf terminal
enable secret cisco
security password min-length 4
user admin password local
aaa new-model
tacacs-server host 200.0.0.2 key happyduck
radius-server host 200.0.0.4 key happyduck
```

```tcl
aaa authentication login default group tacacs+ group radius local
aaa authentication enable default group tacacs+ group radius local

line vty 0 4
login authentication default

line console 0
login authentication default
exec-timeout 2 30
```