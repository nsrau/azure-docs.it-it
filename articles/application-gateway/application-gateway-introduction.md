<properties 
   pageTitle="Introduzione al servizio Gateway applicazione | Microsoft Azure"
   description="Questa pagina offre una panoramica del servizio Gateway applicazione per il bilanciamento del carico di livello 7, incluse dimensioni del gateway, bilanciamento del carico HTTP, affinità di sessione basata su cookie e offload SSL."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/03/2015"
   ms.author="joaoma"/>

# Informazioni sul servizio Gateway applicazione


Il servizio Gateway applicazione di Microsoft Azure fornisce una soluzione di bilanciamento del carico HTTP gestita da Azure basata sul bilanciamento del carico di livello 7.

Il bilanciamento del carico delle applicazioni consente agli amministratori e sviluppatori IT di creare regole di routing per il traffico di rete basato su HTTP. Il servizio gateway applicazione ha una disponibilità elevata ed è basato sul consumo. Per il contratto di servizio e i prezzi, fare riferimento alle pagine [Contratti di servizio](http://azure.microsoft.com/support/legal/sla/) e [Prezzi](https://azure.microsoft.com/pricing/details/application-gateway/).

Il servizio Gateway applicazione attualmente supporta la distribuzione di applicazioni di livello 7 per le funzionalità seguenti:

- Bilanciamento del carico HTTP
- Affinità di sessione basata su cookie
- Offload SSL

![Gateway applicazione](./media/application-gateway-introduction/appgateway1.png)

## Bilanciamento del carico di livello 7 HTTP

Azure fornisce il servizio di bilanciamento del carico di livello 4 che funziona a livello di trasporto (TCP/UDP) e il bilanciamento del carico di tutto il traffico di rete in ingresso al servizio Gateway applicazione. Il gateway applicazione applicherà quindi le regole di routing al traffico HTTP, fornendo il bilanciamento del carico di livello 7. Quando si crea un gateway applicazione, un endpoint (indirizzo VIP) verrà associato e usato come IP pubblico per il traffico di rete in ingresso.

Il gateway applicazione instraderà il traffico HTTP in base alla relativa configurazione, sia che si tratti di una macchina virtuale, di servizio cloud, di un'app Web o di un indirizzo IP esterno.

Il diagramma seguente illustra il flusso di traffico per il servizio Gateway applicazione:

 
![Application Gateway2](./media/application-gateway-introduction/appgateway2.png)

Il bilanciamento del carico di livello 7 HTTP è utile per:


- Applicazioni che necessitano delle richieste provenienti dalla stessa sessione utente/client per raggiungere la stessa VM back-end, ad esempio applicazioni carrello e server di posta Web.
- Applicazioni che hanno la funzione di liberare le server farm Web dal sovraccarico della terminazione SSL.
- Applicazioni, ad esempio la rete CDN, che necessitano che più richieste HTTP nella stessa connessione TCP con esecuzione prolungata vengano instradate/bilanciate in server back-end diversi.

## Istanze e dimensioni del gateway

Il servizio Gateway applicazione è attualmente disponibile in 3 dimensioni: Small, Medium e Large. Le dimensioni delle istanze piccole sono destinate a scenari di sviluppo e test.

È possibile creare fino a 10 gateway applicazione per sottoscrizione e ogni gateway applicazione può includere fino a 10 istanze. Il bilanciamento del carico del gateway applicazione come servizio gestito da Azure consente il provisioning di un servizio di bilanciamento del carico di livello 7 dietro il servizio di bilanciamento del carico del software di Azure.

Nella tabella seguente viene illustrata una velocità effettiva media delle prestazioni per ogni istanza del gateway dell’applicazione:

| Risposta della pagina di back-end | Piccola | Media | Grande|
|---|---|---|---|
| 6K | 7,5 Mbps | 13 Mbps | 50 Mbps |
|100k | 35 Mbps | 100 Mbps| 200 Mbps |


>[AZURE.NOTE]I numeri della prestazione dipendono anche dalla risposta HTTP di applicazione web al gateway dell’applicazione.


## Monitoraggio
 
Gateway applicazione monitora lo stato di integrità delle istanze di back-end utilizzando porte probe, verificando periodicamente la risposta HTTP dalle sezioni HttpSettings del gateway. Il probe prevede una risposta HTTP con esito positivo nell'intervallo di codici di risposta 200 390 e verifica gli indirizzi IP di back-end ogni 30 secondi per verificare la risposta HTTP.

Quando viene ricevuta una risposta HTTP con esito positivo, l'indirizzo IP è contrassegnato come integro. Se il probe ha esito negativo, l'indirizzo IP viene rimosso da un pool di back-end integro e si interrompe il flusso del traffico nel server. Il probe integro continua ogni 30 secondi per l'istanza web non riuscita finché non torna alla modalità online. Quando l'istanza web risponde correttamente al probe di integrità, viene aggiunta di nuovo al pool di back-end integro e il flusso del traffico in questa istanza riprenderà.

## Configurazione e gestione

È possibile creare e gestire un gateway applicazione usando le API REST e i cmdlet PowerShell.


## Passaggi successivi

Creare un gateway applicazione. Vedere [Creare un gateway applicazione](application-gateway-create-gateway.md).

Configurare l'offload SSL. Vedere [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

<!---HONumber=Nov15_HO2-->