# DSBD2021-ClusterA

## CLUSTER A - DSBD2021

## Authors:
    (ID 6) Marco Palumbo, Orazio Santonocito - (3C)Pagamenti_HeartBeat
    (ID 8) Andrea Arciprete, Daniela Chiavetta - (9B)ShippingSystem_PingAck
    (ID 9) Giuseppe Germano, Gianluca Cristaudo - (1A)Prodotti_PingAck
    (ID 10) Antonio Impalà, Kristian Di Blasi - (3A)Pagamenti_PingAck
    (ID 14) Irene Baldacchino, Salvatore Gambadoro - (2B)Ordini_PingAck
    (ID 25) Raffaele Mineo [SUPERVISOR] - (5A)PingAckFD_HeartBeat, API gateway Nginx
    (ID 27) Amelia Sorrenti - (5A)PingAckFD_PingAck
    (ID 28) Andrea Valentino Papa - (6A)HeartBeatFD_None

## Minikube commands:
```
    toDo
```
    
## Hosts config:
```
    echo "$(minikube ip) clustera.dsbd2021.it* | sudo tee /a /etc/hosts"
```

## Scheme:
<!--
@startuml diagram

actor endUser
interface ApiGateway

queue Kafka
component Zookeeper

artifact Pagamenti1
artifact ShippingSystem
artifact Prodotti
artifact Pagamenti2
artifact Ordini
artifact FaultDetectors

database Pagamenti1DB
database ShippingSystemDB
database ProdottiDB
database Pagamenti2DB
database OrdiniDB

storage Pagamenti1DBvolume
storage ShippingSystemDBvolume
storage ProdottiDBvolume
storage Pagamenti2DBvolume
storage OrdiniDBvolume

endUser --_> ApiGateway : http://clustera.dsbd.2021.it

ApiGateway --_> Pagamenti1
ApiGateway --_> ShippingSystem
ApiGateway --_> Prodotti
ApiGateway --_> Pagamenti2
ApiGateway --_> Ordini

Pagamenti1 --# Pagamenti1DB
ShippingSystem --# ShippingSystemDB
Prodotti --# ProdottiDB
Pagamenti2 --# Pagamenti2DB
Ordini --# OrdiniDB

Pagamenti1DB --# Pagamenti1DBvolume
ShippingSystemDB --# ShippingSystemDBvolume
ProdottiDB --# ProdottiDBvolume
Pagamenti2DB --# Pagamenti2DBvolume
OrdiniDB --# OrdiniDBvolume

Kafka --_> Zookeeper

Pagamenti1 ~~ Kafka
ShippingSystem ~~ Kafka
Prodotti ~~ Kafka
Pagamenti2 ~~ Kafka
Ordini ~~ Kafka
FaultDetectors ~~ Kafka

Pagamenti1 .. FaultDetectors
ShippingSystem .. FaultDetectors
Prodotti .. FaultDetectors
Pagamenti2 .. FaultDetectors
Ordini .. FaultDetectors
FaultDetectors .. FaultDetectors

@enduml
-->
![](diagram.svg)
