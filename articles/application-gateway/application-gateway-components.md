---
title: Componenti di gateway applicazione
description: Questo articolo fornisce informazioni sui vari componenti in un gateway applicazione
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: f5dfa34760bcef23bf54d65b35e3ad8f48cc2ee5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008070"
---
# <a name="application-gateway-components"></a>Componenti di gateway applicazione

 Un gateway applicazione funge da singolo punto di contatto per i client. Distribuisce il traffico in ingresso dell'applicazione in più pool di back-end, che includono macchine virtuali di Azure, set di scalabilità di macchine virtuali, servizio App di Azure e i server in locale ed esterni. Per distribuire il traffico, un gateway applicazione vengono usati numerosi componenti descritti in questo articolo.

![I componenti utilizzati in un gateway applicazione](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Indirizzi IP Frontend

Un indirizzo IP front-end è l'indirizzo IP associato a un gateway applicazione. È possibile configurare un gateway applicazione con un indirizzo IP pubblico, un indirizzo IP privato o entrambi. Un gateway applicazione supporta una pubblica o un indirizzo IP privato. La rete virtuale e indirizzo IP pubblico deve essere nella stessa località del gateway applicazione. Dopo averlo creato, un indirizzo IP front-end è associato a un listener.

### <a name="static-versus-dynamic-public-ip-address"></a>Statico e indirizzo IP pubblico dinamico

Lo SKU di Gateway applicazione di Azure v2 supporta entrambi interni e statici indirizzi IP pubblici statici, anche se lo SKU di versione 1 supporta solo indirizzi IP interni statici. Se un gateway applicazione viene arrestato e avviato, possibile modificare l'indirizzo IP virtuale (VIP).

Il nome DNS associato a un gateway applicazione non cambia durante il ciclo di vita del gateway. Di conseguenza, è necessario utilizzare un alias CNAME e puntarlo all'indirizzo DNS del gateway applicazione.

## <a name="listeners"></a>Listener

Un listener è un'entità logica che controlla le richieste di connessione in ingresso. Un listener accetta una richiesta se il protocollo, porta, host e indirizzo IP associato alla richiesta corrisponde agli stessi elementi associati con la configurazione del listener.

Prima di usare un gateway applicazione, è necessario aggiungere almeno un listener. Possono esserci più listener collegato a un gateway applicazione e possono essere utilizzati per lo stesso protocollo.

Dopo che un listener rileva le richieste in ingresso dai client, il gateway applicazione instrada le richieste ai membri del pool back-end. Il gateway applicazione usa le regole di routing della richiesta è definite per il listener che ha ricevuto la richiesta in ingresso.

I listener supportano le seguenti porte e protocolli.

### <a name="ports"></a>Porte

È una porta in cui un listener è in ascolto per la richiesta del client. È possibile configurare le porte compreso tra 1 e 65502 per lo SKU di versione 1 e 1 per 65199 per lo SKU v2.

### <a name="protocols"></a>Protocolli

Il Gateway applicazione supporta quattro protocolli: HTTP, HTTPS, HTTP/2 e WebSocket:

- Specificare tra HTTP e HTTPS protocolli nella configurazione del listener.
- Supporto per [protocolli WebSockets e HTTP/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) viene fornito in modo nativo, e [supporto di WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) è abilitato per impostazione predefinita. Non esistono impostazioni configurabili dall'utente per abilitare o disabilitare in modo selettivo il supporto di WebSocket. Usare i WebSocket con listener HTTP e HTTPS.
- Il supporto del protocollo HTTP/2 è disponibile per i client che si connettono solo a listener del gateway applicazione. La comunicazione con i pool di server back-end avviene tramite HTTP/1.1. Per impostazione predefinita, il supporto di HTTP/2 è disabilitato. È possibile scegliere di abilitarlo.

Usare un listener HTTPS per la terminazione SSL. Un listener HTTPS trasferisce il carico di lavoro di crittografia e decrittografia al gateway applicazione, in modo che i server web non sono sovraccaricati da un sovraccarico. Le app sono quindi liberi di concentrarsi sulla logica di business.

### <a name="custom-error-pages"></a>Pagine di errore personalizzate

Il Gateway applicazione consente di creare pagine di errore personalizzate invece di visualizzare pagine di errore predefinito. Se si usa una pagina di errore personalizzata, è possibile usare il proprio layout e marchio aziendali. Il Gateway applicazione consente di visualizzare una pagina di errore personalizzata quando una richiesta non riesce a raggiungere il back-end.

Per altre informazioni, vedere [pagine di errore personalizzato per il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Tipi di listener

Esistono due tipi di listener:

- **Basic**. Questo tipo di listener è in ascolto su un sito singolo dominio, in cui ha un solo mapping DNS all'indirizzo IP del gateway applicazione. Questa configurazione del listener è necessaria quando si ospita un singolo sito dietro un gateway applicazione.

- **Multisito**. Questa configurazione del listener è necessaria quando si configura più di un'applicazione web nella stessa istanza del gateway applicazione. Consente di configurare una topologia più efficiente per le distribuzioni aggiungendo fino a 100 siti Web a un unico gateway applicazione. Ogni sito Web può essere indirizzato al proprio pool back-end. Ad esempio, tre sottodomini abc.contoso.com, xyz.contoso.com e pqr.contoso.com, puntare all'indirizzo IP del gateway applicazione. Si potrebbe creare tre i listener multisito e configurare ogni listener per la rispettiva porta e l'impostazione del protocollo.

    Per altre informazioni, vedere [che ospita più siti](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Dopo aver creato un listener, è associarlo a una regola di routing di richiesta. Questa regola determina la modalità richiesta ricevuta nel listener deve essere indirizzata al back-end.

Il Gateway applicazione elabora listener nell'ordine indicato. Se il listener di base corrisponde a una richiesta in ingresso, viene elaborato per primo. Per instradare il traffico al back-end corretto, configurare un listener multisito prima di un listener di base.

## <a name="request-routing-rules"></a>Richiedere le regole di routing

Una regola di routing di richiesta è un componente chiave di un gateway applicazione, perché determina come instradare il traffico nel listener. La regola associa il listener, il pool di server back-end e le impostazioni HTTP di back-end.

Quando un listener accetta una richiesta, la regola di routing richiesta inoltra la richiesta al back-end o lo reindirizza altrove. Se la richiesta viene inoltrata al back-end, la regola di routing richiesta definisce quale pool di server back-end per l'inoltro a. Inoltre, la regola di routing richiesta determina anche se le intestazioni nella richiesta devono essere riscritte. Un listener può essere collegato a una regola.

Esistono due tipi di regole di routing di richiesta:

- **Basic**. Tutte le richieste sul listener associati (ad esempio, blog.contoso.com/*) vengono inoltrate al pool back-end associati usando l'impostazione HTTP associato.

- **Basata sul percorso**. Questa regola di routing consente di indirizzare le richieste sul listener associati a un pool back-end specifico, basato sull'URL nella richiesta. Se il percorso dell'URL in una richiesta corrisponde al modello di percorso in una regola basata sul percorso, la regola esegue il routing che la richiesta. Si applica il modello di percorso solo il percorso dell'URL, non per i parametri di query. Se il percorso dell'URL in una richiesta del listener non corrisponde ad alcuna delle regole basate sul percorso, lo instrada la richiesta per il pool back-end e le impostazioni HTTP.

Per altre informazioni, vedere [routing basato su URL](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Supporto per il reindirizzamento

La regola di routing richiesta consente inoltre di reindirizzare il traffico sul gateway applicazione. Si tratta di un meccanismo di reindirizzamento generico, pertanto è possibile reindirizzare a e da qualsiasi porta è definire l'utilizzo di regole.

È possibile scegliere la destinazione di reindirizzamento deve essere un altro listener (che consente di abilitare automatico da HTTP per il reindirizzamento HTTPS) o un sito esterno. È anche possibile scegliere di utilizzare il reindirizzamento temporaneo o permanente o accodare la stringa di query e percorso URI per l'URL reindirizzato.

Per altre informazioni, vedere [reindirizzare il traffico sul gateway applicazione](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="rewrite-http-headers"></a>Riscrivere le intestazioni HTTP

Usando le regole di routing di richiesta, è possibile aggiungere, rimuovere o aggiornare la richiesta HTTP (S) e spostano le intestazioni di risposta come pacchetti di richiesta e risposta tra il client e back-end pool tramite il gateway applicazione.

Le intestazioni possono essere impostate su valori statici o ad altre intestazioni e le variabili del server. In questo modo con importanti casi d'uso, ad esempio l'estrazione di indirizzi IP client, la rimozione di informazioni riservate sul back-end, l'aggiunta di sicurezza superiore e così via.

Per altre informazioni, vedere [intestazioni di HTTP riscrivere nel gateway applicazione](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>Impostazioni HTTP

Un gateway applicazione instrada il traffico ai server back-end (specificati nella regola di routing di richieste che includono le impostazioni HTTP) con il numero di porta, protocollo e altre impostazioni descritte in dettaglio in questo componente.

La porta e protocollo usato per le impostazioni HTTP di determinare se il traffico tra i server gateway e back-end dell'applicazione è crittografato (con SSL end-to-end) o non crittografati.

Questo componente viene usato anche per:

- Determinare se una sessione utente deve rimanere nello stesso server usando il [affinità di sessione basata su cookie](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity).

- Normalmente rimuovere i membri del pool back-end tramite [esaurimento delle connessioni](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining).

- Associare un probe personalizzato per monitorare l'integrità back-end, impostare l'intervallo di timeout della richiesta, sostituire il nome host e il percorso nella richiesta e garantire la semplicità di un solo clic per specificare le impostazioni per il back-end di servizio App.

## <a name="backend-pools"></a>Pool back-end

Un pool back-end instrada richiesta ai server back-end che fornisce la richiesta. Pool back-end può contenere:

- Schede di interfaccia di rete
- set di scalabilità di macchine virtuali
- Indirizzi IP pubblici
- Indirizzi IP interni
- FQDN
- Back-end multi-tenant (ad esempio servizio App)

I membri del pool back-end Gateway applicazione non sono legati a un set di disponibilità. Un gateway applicazione può comunicare con istanze all'esterno della rete virtuale che si trova in. Di conseguenza, i membri dei pool di back-end possono essere tra i cluster, tra Data Center o all'esterno di Azure, purché vi sia connettività IP.

Se si usa indirizzi IP interni come membri del pool back-end, è necessario utilizzare [peering reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) o una [gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Peering reti virtuali è supportato ed è utile per traffico di bilanciamento del carico in altre reti virtuali.

Un gateway applicazione può anche comunicare con ai server on-premises quando sono connessi da tunnel VPN o ExpressRoute di Azure se il traffico è consentito.

È possibile creare pool di back-end diversi per diversi tipi di richieste. Ad esempio, creare un pool di back-end per le richieste generale e un altro pool back-end per le richieste per i microservizi per l'applicazione.

## <a name="health-probes"></a>Probe di integrità

Per impostazione predefinita, un gateway applicazione monitora l'integrità di tutte le risorse nel pool back-end e quelli non integro viene rimosso automaticamente. Quindi esegue il monitoraggio di istanze non integre e li aggiunge al pool back-end integro quando diventano disponibili e rispondere ai probe di integrità.

Oltre al monitoraggio del probe di integrità predefinito, è anche possibile personalizzare il probe di integrità in base ai requisiti dell'applicazione. I probe personalizzati consentono un controllo più granulare sul monitoraggio dell'integrità. Quando si usano i probe personalizzati, è possibile configurare l'intervallo di probe, l'URL e percorso da testare e il numero di risposte non riuscito da accettare prima che l'istanza del pool back-end sia contrassegnata come non integro. È consigliabile configurare i probe personalizzati per monitorare l'integrità di ogni pool back-end.

Per altre informazioni, vedere [monitorare l'integrità del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Passaggi successivi

Creare un gateway applicazione:

* [Nel portale di Azure](quick-create-portal.md)
* [Usando Azure PowerShell](quick-create-powershell.md)
* [Tramite la CLI di Azure](quick-create-cli.md)
