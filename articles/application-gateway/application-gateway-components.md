---
title: Componenti del gateway applicazione
description: In questo articolo vengono fornite informazioni sui vari componenti in un gateway applicazione
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 90b3c3fd18bc9211c731ccf16dd646a64a4a1116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133103"
---
# <a name="application-gateway-components"></a>Componenti del gateway applicazione

 Un gateway applicazione funge da singolo punto di contatto per i client. Distribuisce il traffico delle applicazioni in ingresso tra più pool back-end, tra cui macchine virtuali di Azure, set di scalabilità di macchine virtuali, servizio app di Azure e server locali/esterni. Per distribuire il traffico, un gateway applicazione utilizza diversi componenti descritti in questo articolo.

![Componenti utilizzati in un gateway applicazione](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Indirizzi IP front-end

Un indirizzo IP front-end è l'indirizzo IP associato a un gateway applicazione. È possibile configurare un gateway applicazione in modo che disponga di un indirizzo IP pubblico, di un indirizzo IP privato o di entrambi. Un gateway applicazione supporta un indirizzo IP pubblico o privato. La rete virtuale e l'indirizzo IP pubblico devono trovarsi nello stesso percorso del gateway applicazione. Dopo la creazione, un indirizzo IP front-end è associato a un listener.

### <a name="static-versus-dynamic-public-ip-address"></a>Indirizzo IP pubblico statico e dinamico

Lo SKU del gateway applicazione di Azure V2 può essere configurato per supportare sia l'indirizzo IP interno statico che l'indirizzo IP pubblico statico oppure solo l'indirizzo IP pubblico statico. Non può essere configurato per supportare solo l'indirizzo IP interno statico.

Lo SKU V1 può essere configurato per supportare l'indirizzo IP interno statico o dinamico e l'indirizzo IP pubblico dinamico. L'indirizzo IP dinamico del gateway applicazione non cambia in un gateway in esecuzione. Può cambiare solo quando si arresta o si avvia il gateway. Non cambia in caso di errori di sistema, aggiornamenti, aggiornamenti dell'host di Azure e così via. 

Il nome DNS associato a un gateway applicazione non cambia nel corso del ciclo di vita del gateway. Di conseguenza, è necessario utilizzare un alias CNAME e puntare all'indirizzo DNS del gateway applicazione.

## <a name="listeners"></a>Listener

Un listener è un'entità logica che controlla le richieste di connessione in ingresso. Un listener accetta una richiesta se il protocollo, la porta, il nome host e l'indirizzo IP associati alla richiesta corrispondono agli stessi elementi associati alla configurazione del listener.

Prima di utilizzare un gateway applicazione, è necessario aggiungere almeno un listener. Possono essere presenti più listener collegati a un gateway applicazione e possono essere utilizzati per lo stesso protocollo.

Dopo che un listener rileva le richieste in ingresso dai client, il gateway applicazione instrada le richieste ai membri del pool back-end configurato nella regola.

I listener supportano le porte e i protocolli seguenti.

### <a name="ports"></a>Porte

Una porta è dove un listener è in ascolto per la richiesta del client. È possibile configurare porte comprese tra 1 e 65502 per lo SKU v1 e da 1 a 65199 per lo SKU v2.

### <a name="protocols"></a>Protocolli

Il gateway applicazione supporta quattro protocolli: HTTP, HTTPS, HTTP/2 e WebSocket:
>[!NOTE]
>Il supporto del protocollo HTTP/2 è disponibile per i client che si connettono solo a listener del gateway applicazione. La comunicazione con i pool di server back-end è sempre superiore a HTTP/1.1. Per impostazione predefinita, il supporto di HTTP/2 è disabilitato. È possibile scegliere di attivarlo.

- Specificare tra i protocolli HTTP e HTTPS nella configurazione del listener.
- Il supporto per [i protocolli WebSockets e HTTP/2](features.md#websocket-and-http2-traffic) viene fornito in modo nativo e il [supporto WebSocket](application-gateway-websocket.md) è abilitato per impostazione predefinita. Non esistono impostazioni configurabili dall'utente per abilitare o disabilitare in modo selettivo il supporto di WebSocket. Utilizzare WebSockets con entrambi i listener HTTP e HTTPS.

Utilizzare un listener HTTPS per la terminazione TLS. Un listener HTTPS scarica il lavoro di crittografia e decrittografia al gateway applicazione, in modo che i server Web non siano gravati dall'overhead.

### <a name="custom-error-pages"></a>Pagine di errore personalizzate

Il gateway applicazione consente di creare pagine di errore personalizzate anziché visualizzare le pagine di errore predefinite. Se si usa una pagina di errore personalizzata, è possibile usare il proprio layout e marchio aziendali. Il gateway applicazione visualizza una pagina di errore personalizzata quando una richiesta non riesce a raggiungere il back-end.

Per ulteriori informazioni, vedere [Pagine di errore personalizzate per il gateway applicazione.](custom-error.md)

### <a name="types-of-listeners"></a>Tipi di listener

Esistono due tipi di listener:

- **Basic**. Questo tipo di listener è in ascolto di un singolo sito di dominio, in cui dispone di un singolo mapping DNS all'indirizzo IP del gateway applicazione. Questa configurazione del listener è necessaria quando si ospita un singolo sito dietro un gateway applicazione.

- **Multisito**. Questa configurazione del listener è necessaria quando si configurano più applicazioni Web nella stessa istanza del gateway applicazione. Consente di configurare una topologia più efficiente per le distribuzioni aggiungendo fino a 100 siti Web a un gateway applicazione. Ogni sito Web può essere indirizzato al proprio pool back-end. Ad esempio, tre sottodomini, abc.contoso.com, xyz.contoso.com e pqr.contoso.com, puntano all'indirizzo IP del gateway applicazione. È necessario creare tre listener multisito e configurare ogni listener per la rispettiva impostazione di porta e protocollo.

    Per ulteriori informazioni, consultate [Hosting multisito](application-gateway-web-app-overview.md).

Dopo aver creato un listener, associarlo a una regola di routing delle richieste. Questa regola determina il modo in cui la richiesta ricevuta nel listener deve essere instradata al back-end.

Il gateway applicazione elabora i listener [nell'ordine indicato.](configuration-overview.md#order-of-processing-listeners)

## <a name="request-routing-rules"></a>Richiedere regole di routing

Una regola di routing delle richieste è un componente chiave di un gateway applicazione perché determina come instradare il traffico sul listener. La regola associa il listener, il pool di server back-end e le impostazioni HTTP back-end.

Quando un listener accetta una richiesta, la regola di routing delle richieste inoltra la richiesta al back-end o la reindirizza altrove. Se la richiesta viene inoltrata al back-end, la regola di routing delle richieste definisce a quale pool di server back-end inoltrarlo. La regola di routing delle richieste determina inoltre se le intestazioni nella richiesta devono essere riscritte. Un listener può essere collegato a una regola.

Esistono due tipi di regole di routing delle richieste:

- **Basic**. Tutte le richieste sul listener associato (ad esempio, blog.contoso.com/) vengono inoltrate al pool back-end associato utilizzando l'impostazione HTTP associata.

- **Basato su percorso**. Questa regola di routing consente di instradare le richieste sul listener associato a un pool back-end specifico, in base all'URL nella richiesta. Se il percorso dell'URL in una richiesta corrisponde al modello di percorso in una regola basata sul percorso, la regola instrada tale richiesta. Applica il modello di percorso solo al percorso URL, non ai relativi parametri di query. Se il percorso URL in una richiesta del listener non corrisponde a nessuna delle regole basate sul percorso, instrada la richiesta al pool back-end predefinito e alle impostazioni HTTP.

Per ulteriori informazioni, vedere [Routing basato su URL](url-route-overview.md).

### <a name="redirection-support"></a>Supporto per il reindirizzamento

La regola di routing delle richieste consente inoltre di reindirizzare il traffico sul gateway applicazione. Si tratta di un meccanismo di reindirizzamento generico, pertanto è possibile reindirizzare da e verso qualsiasi porta definita utilizzando le regole.

È possibile scegliere la destinazione di reindirizzamento come un altro listener (che consente il reindirizzamento automatico da HTTP a HTTPS) o un sito esterno. È inoltre possibile scegliere di fare in modo che il reindirizzamento sia temporaneo o permanente oppure per aggiungere il percorso URI e la stringa di query all'URL reindirizzato.

Per ulteriori informazioni, vedere [Reindirizzare il traffico sul gateway applicazione.](redirect-overview.md)

### <a name="rewrite-http-headers"></a>Riscrivere le intestazioni HTTP

Utilizzando le regole di routing delle richieste, è possibile aggiungere, rimuovere o aggiornare le intestazioni di richiesta e risposta HTTP(S) quando i pacchetti di richiesta e risposta si spostano tra il client e i pool back-end tramite il gateway applicazione.

Le intestazioni possono essere impostate su valori statici o su altre intestazioni e variabili server. Ciò consente di eliminare importanti casi d'uso, ad esempio l'estrazione degli indirizzi IP dei client, la rimozione di informazioni riservate sul back-end, l'aggiunta di maggiore sicurezza e così via.

Per ulteriori informazioni, vedere [Riscrivere le intestazioni HTTP nel gateway applicazione.](rewrite-http-headers.md)

## <a name="http-settings"></a>Impostazioni HTTP

Un gateway applicazione indirizza il traffico ai server back-end (specificati nella regola di routing delle richieste che includono le impostazioni HTTP) utilizzando il numero di porta, il protocollo e altre impostazioni descritte in questo componente.

La porta e il protocollo utilizzati nelle impostazioni HTTP determinano se il traffico tra il gateway applicazione e i server back-end è crittografato (che fornisce TLS end-to-end) o non crittografato.

Questo componente viene utilizzato anche per:

- Determinare se una sessione utente deve essere mantenuta nello stesso server utilizzando l'affinità di [sessione basata su cookie.](features.md#session-affinity)

- Rimuovere in modo normale i membri del pool back-end utilizzando [lo svuotamento della connessione.](features.md#connection-draining)

- Associare un probe personalizzato per monitorare l'integrità del back-end, impostare l'intervallo di timeout della richiesta, sostituire il nome host e il percorso nella richiesta e fornire facilità con un clic per specificare le impostazioni per il back-end del servizio app.

## <a name="backend-pools"></a>Pool back-end

Un pool back-end indirizza la richiesta ai server back-end, che servono la richiesta. I pool back-end possono contenere:

- Schede di interfaccia di rete
- set di scalabilità di macchine virtuali
- Indirizzi IP pubblici
- Indirizzi IP interni
- Nome di dominio completo
- Back-end multitenant (ad esempio Servizio app)

I membri del pool back-end del gateway applicazione non sono associati a un set di disponibilità. Un gateway applicazione può comunicare con istanze esterne alla rete virtuale in cui si è. Di conseguenza, i membri dei pool back-end possono essere tra cluster, tra data center o all'esterno di Azure, purché sia disponibile la connettività IP.

Se si utilizzano indirizzi IP interni come membri del pool back-end, è necessario utilizzare il [peering](../virtual-network/virtual-network-peering-overview.md) della rete virtuale o un [gateway VPN.](../vpn-gateway/vpn-gateway-about-vpngateways.md) Il peering di reti virtuali è supportato e vantaggioso per il bilanciamento del carico del traffico in altre reti virtuali.

Un gateway applicazione può anche comunicare con i server locali quando sono connessi tramite tunnel Di Azure ExpressRoute o VPN se il traffico è consentito.

È possibile creare pool back-end diversi per diversi tipi di richieste. Ad esempio, creare un pool back-end per le richieste generali e quindi un altro pool back-end per le richieste ai microservizi per l'applicazione.

## <a name="health-probes"></a>Probe di integrità

Per impostazione predefinita, un gateway applicazione monitora l'integrità di tutte le risorse nel pool back-end e rimuove automaticamente quelle non integre. Monitora quindi le istanze non integre e le aggiunge nuovamente al pool back-end integro quando diventano disponibili e rispondono ai probe di integrità.

Oltre al monitoraggio del probe di integrità predefinito, è anche possibile personalizzare il probe di integrità in base ai requisiti dell'applicazione. I probe personalizzati consentono un controllo più granulare sul monitoraggio dello stato. Quando si usano probe personalizzati, è possibile configurare l'intervallo di probe, l'URL e il percorso da testare e il numero di risposte non riuscite da accettare prima che l'istanza del pool back-end sia contrassegnata come non integra. È consigliabile configurare probe personalizzati per monitorare l'integrità di ogni pool back-end.

Per ulteriori informazioni, vedere [Monitorare l'integrità del gateway applicazione.](../application-gateway/application-gateway-probe-overview.md)

## <a name="next-steps"></a>Passaggi successivi

Creare un gateway applicazione:

* [Nel portale di Azure](quick-create-portal.md)
* [Usando Azure PowerShell](quick-create-powershell.md)
* [Usando l'interfaccia della riga di comando di Azure](quick-create-cli.md)
