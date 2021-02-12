# DSBD2021-ClusterA

## CLUSTER A - DSBD2021

## Authors:
    (ID 6) Marco Palumbo, Orazio Santonocito - (3C)Pagamenti_HeartBeat
    (ID 8) Andrea Arciprete, Daniela Chiavetta - (9B)ShippingSystem_PingAck
    (ID 9) Giuseppe Germano, Gianluca Cristaudo - (1A)Prodotti_PingAck ***RITIRATI SENZA PREAVVISO il 05/02/2021***
    (ID xx) Irene Baldacchino - (xx)Fake_Prodotti ***RISOLUZIONE A RITIRO uService Prodotti***
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

endUser --_> ApiGateway : http://clustera.dsbd2021.it

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

## Testing

```
    Al fine di verificare il corretto funzionamento dei vari microservizi e delle varie interazioni i passaggi da
    seguire sono i successivi:
    
        1) Il microservizio ORDINI al momento della POST che riguarda l’inserimento di un nuovo ordine nel
            database, invia un messaggio sul topic ORDERS con chiave ORDER_COMPLETED.

            curl -X POST --header "Content-Type: application/json" --header "Accept:application/json" --
            header "X-User-ID: 0" http://clustera.dsbd2021.it/order/orders -d '{"orders": [{"product_id": 1,
            "quantity": 10, "price": 10.00}, { "product_id": 2, "quantity": 20, "price": 10.00}],
            "addressShipment": "Via1", "addressBilling": "Via2"}'

            I consumatori del messaggio sono rispettivamente il microservizio PRODUCTS e SHIPPING.

        2) Nel momento in cui viene consumato correttamente il messaggio da parte di PRODUCTS,
            conseguentemente ne produrrà un altro sul topic ORDERS con chiave ORDER_VALIDATION.
            I relativi consumatori saranno i microservizi ORDERS e SPEDIZIONI.

        3) I microservizi PAYMENT (1-2) produrranno in maniera separata un messaggio sul topic ORDER con
            chiave ORDER_PAID.
            
            Per quanto riguarda il microservizio PAYMENT (ID_6, 3C):
                L'invio dovrà essere effettuato mediante l'utilizzo di TALEND API TESTER.

                POST http://clustera.dsbd2021.it/payment/ipn
                Content-Type: application/x-www-form-urlencoded
                BODY: 
                    payment_type=echeck&payment_date=12%3A43%3A29%20Jan%2005%2C%202021%20PST&payment_status=Completed
                    &address_status=confirmed&payer_status=verified&first_name=John&last_name=Smith&payer_email=buyer@paypalsandbox.com
                    &payer_id=0&address_name=John%20Smith&address_country=United%20States&address_country_code=US
                    &address_zip=95131&address_state=CA&address_city=San%20Jose&address_street=123%20any%20street&business=seller@paypalsandbox.com
                    &receiver_email=orazio1997@outlook.it&receiver_id=seller@paypalsandbox.com&residence_country=US&item_name=something
                    &item_number=AK-1234&quantity=1&shipping=3.04&tax=2.02&mc_currency=USD&mc_fee=0.44&mc_gross=20
                    &mc_gross_1=12.34&txn_type=web_accept&txn_id=165345880&notify_version=2.1&auction_buyer_id=SomeFancyID&for_auction=TRUE
                    &custom=xyz123&invoice=60270d0f13098845f5e1511e&test_ipn=1&verify_sign=ADuIyIR0o6rLFJjTZ50BFLtfmE0QA7E.hF10j0kbUqzPStL5nsSXEESz

                Dove verranno rispettivamente modificati i seguenti parametri INVOICE (orderId), PAYER_ID (userId) e mc_gross (amountPaid) e renderli corrispondenti a quelli inseriti dal microservizio ORDERS.

                Il consumatore del messaggio sarà il microservizio ORDERS.

        4) Se i dati ricevuti da ORDERS hanno una corrispondenza all’interno del database riguardo
            l’orderId, userId e amountPaid, verrà prodotto un messaggio sul topic INVOICING con chiave
            ORDER_PAID.
            Il consumatore del messaggio sarà il microservizio SHIPPING.
        
    Al fine di verificare il corretto inserimento degli elementi nel database, vengono utilizzate le seguenti curl di
    supporto:
    
        1) ORDERS:
            curl -X GET --header "Content-Type: application/json" --header "Accept:application/json" --
            header "X-User-ID: 0" http://clustera.dsbd2021.it/order/orders
            
        2) SHIPPING:
            curl -X GET --header "Content-Type: application/json" --header "Accept:application/json" --
            header "X-User-ID: 0" --data "per_page=1&page=1"
            http://clustera.dsbd2021.it/shipping/shippings
            
            #ADDOTHERS
```
