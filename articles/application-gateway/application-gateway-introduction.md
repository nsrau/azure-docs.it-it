<properties
   pageTitle="Introduzione al servizio Gateway applicazione | Microsoft Azure"
   description="Questa pagina offre una panoramica del servizio Gateway applicazione per il bilanciamento del carico di livello 7, incluse dimensioni del gateway, bilanciamento del carico HTTP, affinità di sessione basata su cookie e offload SSL."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace"/>  

# Panoramica del gateway applicazione

Il gateway applicazione di Microsoft Azure offre un servizio di bilanciamento del carico HTTP gestito da Azure basato sul bilanciamento del carico di livello 7. In breve, accetta il traffico e, in base alle regole appositamente definite, lo instrada alle istanze back-end appropriate.

Il bilanciamento del carico delle applicazioni consente agli sviluppatori e agli amministratori IT di creare regole di routing per il traffico di rete basato sul protocollo HTTP. Il servizio gateway applicazione ha una disponibilità elevata ed è basato sul consumo. Per il contratto di servizio e i prezzi, vedere le pagine [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/) e [Prezzi](https://azure.microsoft.com/pricing/details/application-gateway/).

Il gateway applicazione attualmente supporta la distribuzione di applicazioni di livello 7 con le funzionalità seguenti:

- **Bilanciamento del carico HTTP**. La funzionalità primaria del gateway applicazione è offrire il bilanciamento del carico. Il bilanciamento del carico viene eseguito al livello 7 e viene usato solo per il traffico HTTP.
- **Affinità di sessione basata su cookie**. Questa funzionalità è utile quando si vuole mantenere una sessione utente nello stesso back-end. Usando i cookie, il gateway applicazione può indirizzare il traffico successivo proveniente da una sessione utente allo stesso back-end per l'elaborazione. Questa funzionalità è importante nei casi in cui alcuni elementi vengono salvati in locale sul server back-end per una sessione utente.
- **[Offload SSL (Secure Sockets Layer)](application-gateway-ssl-arm.md)**. Questa funzionalità consente di evitare costose attività di decrittografia del traffico HTTPS nei server Web. Terminando la connessione SSL in corrispondenza del gateway applicazione e inoltrando al server la richiesta non crittografata, il server Web non deve gestire la decrittografia. Il gateway applicazione crittografa nuovamente la risposta prima di restituirla al client. Questa funzionalità è utile negli scenari in cui il back-end si trova nella stessa rete virtuale protetta del gateway applicazione in Azure.
- **[Routing del contenuto basato su URL](application-gateway-url-route-overview.md)**. Questa funzionalità offre la possibilità di usare server back-end diversi per traffico diverso. Il traffico per una cartella nel server Web o per una rete CDN potrebbe essere instradato a un back-end diverso, riducendo il carico non necessario nei back-end che non gestiscono contenuto specifico.
- **[Routing multisito](application-gateway-multi-site-overview.md)**. Il gateway applicazione consente di consolidare fino a 20 siti Web in un singolo gateway applicazione.
- **[Supporto di WebSocket](application-gateway-websocket.md)**. Un'altra eccellente funzionalità del gateway applicazione è il supporto nativo per WebSocket.


## Bilanciamento del carico di livello 7 HTTP

Il gateway applicazione applica le regole di routing al traffico HTTP, fornendo il bilanciamento del carico di livello 7 (HTTP). Quando si crea un gateway applicazione, un endpoint (indirizzo VIP) viene associato e usato come IP pubblico per il traffico di rete in ingresso. Azure fornisce il bilanciamento del carico di livello 4 tramite Azure Load Balancer con funzionamento a livello di trasporto (TCP/UDP) e bilanciamento del carico di tutto il traffico di rete in ingresso al servizio gateway applicazione. Il gateway applicazione instrada il traffico HTTP in base alla relativa configurazione, sia che si tratti di una macchina virtuale, di un servizio cloud, di un'app Web o di un indirizzo IP esterno.

Il bilanciamento del carico di livello 7 HTTP è utile per:

- Applicazioni che necessitano delle richieste provenienti dalla stessa sessione utente/client per raggiungere la stessa macchina virtuale back-end, ad esempio app carrello acquisti e server di posta Web.
- Applicazioni che hanno la funzione di liberare le server farm Web dal sovraccarico della terminazione SSL.
- Applicazioni, come la rete per la distribuzione di contenuti, per cui è necessario che più richieste HTTP nella stessa connessione TCP con esecuzione prolungata vengano instradate/bilanciate in server back-end diversi.
- Applicazioni che supportano il traffico WebSocket ma hanno comunque un endpoint HTTP per il monitoraggio.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## Istanze e dimensioni del gateway

Il servizio Gateway applicazione è attualmente disponibile in 3 dimensioni: Small, Medium e Large. Le dimensioni delle istanze piccole sono destinate a scenari di sviluppo e test.

È possibile creare fino a 50 gateway applicazione per sottoscrizione e ogni gateway applicazione può includere fino a 10 istanze. Il bilanciamento del carico del gateway applicazione come servizio gestito da Azure consente il provisioning di un servizio di bilanciamento del carico di livello 7 dietro il servizio di bilanciamento del carico del software di Azure.

La tabella seguente illustra una velocità effettiva media delle prestazioni per ogni istanza del gateway applicazione:

| Risposta della pagina di back-end | Small | Media | Large|
|---|---|---|---|
| 6 K | 7,5 Mbps | 13 Mbps | 50 Mbps |
|100 K | 35 Mbps | 100 Mbps| 200 Mbps |

>[AZURE.NOTE] Questi valori sono indicazioni approssimative della velocità effettiva di un gateway applicazione. La velocità effettiva dipende da vari dettagli ambientali come le dimensioni medie delle pagine, la posizione delle istanze back-end e il tempo di elaborazione per gestire una pagina.

## Monitoraggio dell’integrità

Il gateway applicazione di Azure monitora automaticamente l'integrità delle istanze back-end tramite probe di integrità di base o personalizzati. Per altre informazioni, vedere [Panoramica del monitoraggio dell'integrità del gateway applicazione](application-gateway-probe-overview.md).

## Configurazione e gestione

Quando viene configurato, il gateway applicazione può usare per l'endpoint un IP pubblico, un IP privato o entrambi. Il gateway applicazione viene configurato in una propria subnet all'interno di una rete virtuale. La subnet creata o usata per il gateway applicazione non può contenere altri tipi di risorse. Le uniche risorse consentite nella subnet sono altri gateway applicazione.

È possibile creare e gestire un gateway applicazione usando API REST, cmdlet di PowerShell, l'interfaccia della riga di comando di Azure o il [portale di Azure](https://portal.azure.com/).

## Passaggi successivi

Dopo aver acquisito familiarità con il gateway applicazione, è possibile [creare un gateway applicazione](application-gateway-create-gateway-portal.md) oppure [configurare un gateway applicazione per l'offload SSL](application-gateway-ssl-arm.md) per bilanciare il carico delle connessioni HTTPS.

Per informazioni su come creare un gateway applicazione usando il routing del contenuto basato su URL, vedere [Creare un gateway applicazione con il routing basato su URL](application-gateway-create-url-route-arm-ps.md).

<!---HONumber=AcomDC_0907_2016-->