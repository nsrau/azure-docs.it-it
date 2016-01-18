<properties 
   pageTitle="Introduzione al servizio Gateway applicazione | Microsoft Azure"
   description="Questa pagina offre una panoramica del servizio Gateway applicazione per il bilanciamento del carico di livello 7, incluse dimensioni del gateway, bilanciamento del carico HTTP, affinità di sessione basata su cookie e offload SSL."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/09/2015"
   ms.author="joaoma"/>

# Informazioni sul servizio Gateway applicazione


Il servizio Gateway applicazione di Microsoft Azure fornisce una soluzione di bilanciamento del carico HTTP gestita da Azure basata sul bilanciamento del carico di livello 7.

Il bilanciamento del carico delle applicazioni consente agli amministratori e sviluppatori IT di creare regole di routing per il traffico di rete basato su HTTP. Il servizio gateway applicazione ha una disponibilità elevata ed è basato sul consumo. Per il contratto di servizio e i prezzi, fare riferimento alle pagine [Contratti di servizio](http://azure.microsoft.com/support/legal/sla/) e [Prezzi](https://azure.microsoft.com/pricing/details/application-gateway/).

Il servizio Gateway applicazione attualmente supporta la distribuzione di applicazioni di livello 7 per le funzionalità seguenti:

- Bilanciamento del carico HTTP
- Affinità di sessione basata su cookie
- Offload SSL

![Gateway applicazione](./media/application-gateway-introduction/appgateway1.png)

Il bilanciamento del carico di livello 7 HTTP è utile per:

- Applicazioni che necessitano delle richieste provenienti dalla stessa sessione utente/client per raggiungere la stessa VM back-end, ad esempio applicazioni carrello e server di posta Web.
- Applicazioni che hanno la funzione di liberare le server farm Web dal sovraccarico della terminazione SSL.
- Applicazioni, ad esempio la rete CDN, che necessitano che più richieste HTTP nella stessa connessione TCP con esecuzione prolungata vengano instradate/bilanciate in server back-end diversi.


## Istanze e dimensioni del gateway

Il servizio Gateway applicazione è attualmente disponibile in 3 dimensioni: Small, Medium e Large. Le dimensioni delle istanze piccole sono destinate a scenari di sviluppo e test.

È possibile creare fino a 50 gateway applicazione per sottoscrizione e ogni gateway applicazione può includere fino a 10 istanze. Il bilanciamento del carico del gateway applicazione come servizio gestito da Azure consente il provisioning di un servizio di bilanciamento del carico di livello 7 dietro il servizio di bilanciamento del carico del software di Azure.

Nella tabella seguente viene illustrata una velocità effettiva media delle prestazioni per ogni istanza del gateway dell’applicazione:


| Risposta della pagina di back-end | Piccola | Media | Grande|
|---|---|---|---|
| 6K | 7,5 Mbps | 13 Mbps | 50 Mbps |
|100k | 35 Mbps | 100 Mbps| 200 Mbps |


>[AZURE.NOTE]Si tratta di indicazioni approssimative per una velocità effettiva del gateway applicazione. La velocità effettiva dipende da vari dettagli ambientali come la dimensione media della pagina, la posizione di istanze di back-end, il tempo di elaborazione del server di una pagina, per citarne alcuni.

## Monitoraggio dell’integrità
 

Il gateway applicazione di Azure monitora automaticamente l'integrità delle istanze di back-end. Per altre informazioni, vedere la pagina relativa a [probe e monitoraggio dell'integrità del gateway applicazione](application-gateway-probe-overview.md).

## Configurazione e gestione

È possibile creare e gestire un gateway applicazione usando le API REST e i cmdlet PowerShell.



## Passaggi successivi

Creare un gateway applicazione. Vedere [Creare un gateway applicazione](application-gateway-create-gateway.md).

Configurare l'offload SSL. Vedere [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

<!---HONumber=AcomDC_0107_2016-->