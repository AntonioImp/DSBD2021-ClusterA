# DSBD2021-ClusterA

## CLUSTER A - DSBD2021

## Authors:
    (ID 6) Marco Palumbo, Orazio Santonocito - (3C)Pagamenti_HeartBeat
    (ID 8) Andrea Arciprete, Daniela Chiavetta - (9B)ShippingSystem_PingAck
    (ID 9) Giuseppe Germano, Gianluca Cristaudo - (1A)Prodotti_PingAck ***RITIRATI SENZA PREAVVISO il 05/02/2021***
    (ID xx) Irene Baldacchino, Salvatore Gambadoro - (xx)Fake_Prodotti ***RISOLUZIONE A RITIRO uService Prodotti***
    (ID 10) Antonio Impalà, Kristian Di Blasi - (3A)Pagamenti_PingAck
    (ID 14) Irene Baldacchino, Salvatore Gambadoro - (2B)Ordini_PingAck
    (ID 25) Raffaele Mineo [SUPERVISOR] - (5A)PingAckFD_HeartBeat, Ingress Nginx
    (ID 27) Amelia Sorrenti - (5A)PingAckFD_PingAck
    (ID 28) Andrea Valentino Papa - (6A)HeartBeatFD_None

## Minikube commands:
```
    ### GENERAL ###
    kubectl apply -f k8s
    
    ### PAYMENT-MICROSERVICE ###
    Accertarsi che il POD contenente il database "payment-microservice-db..." sia la REPLICA PRIMARY:
    1) Accedere al POD con il comando: -> kubectl exec --stdin --tty <NOME_DEL_POD> -- /bin/sh
    2) Eseguire il comando con le seguenti credenziali di test (di base un replica set di una sola replica che è quella che è contenuta nel POD): -> mongo -u root -p 1208 --eval 'rs.initiate()'
    3) Uscire dal container: -> exit

    ### toDo ###
```
    
## Hosts config:
```
    echo "$(minikube ip) clustera.dsbd2021.it" | sudo tee -a /etc/hosts
    kubectl create clusterrolebinding cluster-system-anonymous --clusterrole=cluster-admin --user=system:anonymous
    minikube addons enable ingress
```

## Scheme:
![Scheme](http://www.plantuml.com/plantuml/png/RPCnJ_Cm48Rt-nMMlhdfruuTK44L30mWLImiw9fSMwj979mBg4j_EscDTSwPCUzZwtcLLrxtZ9w7fWOisNb3RVNMamVRClazbcJkE_k4JDzu1lWSQ23pZFiJGkcQphNKidbtxaJKaGT0ptQynUOL3zYCq2uasnvjrzdsi3ttJ4rorhlACTi_RYybU_6LRPCpZpZKl26cQ-yUe4B67VQKg3SFaaxbAOdwD9C2QHegZ0dqenCFKzUNguU68b92ZSMgWQWrYluOm-zOPZRxk4jsCYjpPEvMytbP3wFSOr7rkwfO_byk2ii0SZHSJSPII6-ci4oN0LbGeJWaY7NC_qrdcsglF1ymkgyq7L7KWQHJ1lSjbp5JSxoYLzNsl99CB5rKjSYbaVZKvA5TuDdkjmSOVgQylVH_NYxXaQhQaiTEaaW7oO9NlgTFJl9KNHJwPQeEsG4faoDfbSDOBmUgPtvWJMqrDFKF)

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
