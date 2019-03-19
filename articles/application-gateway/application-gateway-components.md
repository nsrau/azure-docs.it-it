---
title: Componenti di Gateway applicazione di Azure
description: Questo articolo fornisce informazioni sui vari componenti nel Gateway applicazione
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 44c8b331ebb258c39a003c91e0711e6dfb87cb12
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076295"
---
# <a name="application-gateway-components"></a>Componenti di gateway applicazione

 Un gateway applicazione funge da singolo punto di contatto per i client. Distribuisce il traffico in ingresso dell'applicazione in più pool di back-end, ad esempio macchine virtuali di Azure, set di scalabilità di macchine virtuali, servizi App o i server in locale ed esterni. A tale scopo, Usa diversi componenti descritti in questo articolo.

![application-gateway-components](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-address"></a>Indirizzo IP front-end

Un indirizzo IP front-end è l'indirizzo IP associato al gateway applicazione. È possibile configurare il gateway applicazione per disporre di un indirizzo IP pubblico o un indirizzo IP privato oppure entrambi. È supportato un solo indirizzo IP pubblico o un indirizzo IP privato. La rete virtuale e indirizzo IP pubblico deve essere nella stessa località del gateway applicazione.

Un indirizzo Frontend IP è associato a un *listener* dopo averlo creato. 

### <a name="static-vs-dynamic-public-ip-address"></a>Indirizzo IP pubblico dinamico di porte statiche e

Lo SKU di versione 1 supporta indirizzi IP interni statici ma non supporta gli indirizzi IP pubblici statici. L'indirizzo VIP può cambiare se il gateway applicazione viene arrestato e avviato. Il nome DNS associato al gateway applicazione non cambia durante il ciclo di vita del gateway. Per questo motivo è consigliabile usare un alias CNAME che punti all'indirizzo DNS del gateway applicazione.

Lo SKU di Gateway applicazione v2 supporta indirizzi IP pubblici statici, nonché gli indirizzi IP interni statici. 

## <a name="listeners"></a>Listener

Prima di iniziare a usare il gateway applicazione, è necessario aggiungere uno o più listener. Un listener è un'entità logica che controlla le richieste di connessione in ingresso e accetta le richieste se il protocollo, porta, host e indirizzo IP associato con la richiesta di corrispondenza con il protocollo, porta, host e indirizzo IP associato con la configurazione del listener. Possono esserci più listener collegato a un gateway applicazione e possono essere utilizzati per lo stesso protocollo. Dopo che il listener rileva le richieste in ingresso dai client, il Gateway applicazione instrada le richieste per i membri del pool back-end, usando le regole di routing di richiesta definite per il listener che ha ricevuto la richiesta in ingresso.

I listener supportano le porte e protocolli seguenti:

### <a name="ports"></a>Porte

Questa è la porta su cui il listener è in ascolto per la richiesta del client. È possibile configurare le porte compreso tra 1 e 65502 per lo SKU di versione 1 e 1 per 65199 per SKU V2.

### <a name="protocols"></a>Protocolli

Il gateway applicazione supporta i quattro protocolli seguenti: HTTP, HTTPS, HTTP/2 e WebSocket

Specificare in modo esplicito la scelta tra i protocolli HTTP e HTTPS nella configurazione del listener. Il [supporto per i protocolli WebSockets e HTTP/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) viene fornito in modo nativo. [Supporto di WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) è abilitato per impostazione predefinita. Non esistono impostazioni configurabili dall'utente per abilitare o disabilitare in modo selettivo il supporto di WebSocket. È possibile usare i WebSocket con listener HTTP e HTTPS. Il supporto del protocollo HTTP/2 è disponibile per i client che si connettono solo a listener del gateway applicazione. La comunicazione con i pool di server back-end avviene tramite HTTP/1.1. Per impostazione predefinita, il supporto di HTTP/2 è disabilitato. È possibile scegliere di abilitarlo.

È possibile usare un listener HTTPS per la terminazione SSL. Un listener HTTPS trasferisce il carico di crittografia e decrittografia di lavoro al gateway applicazione in modo che i server web non sono sovraccaricati da tale sovraccarico. Le applicazioni sono quindi liberi di concentrarsi sulla loro logica di business.

### <a name="custom-error-pages"></a>Pagine di errore personalizzate

Il gateway applicazione consente di creare pagine di errore personalizzate invece di visualizzare pagine di errore predefinito. Se si usa una pagina di errore personalizzata, è possibile usare il proprio layout e marchio aziendali. Il gateway applicazione può visualizzare una pagina di errore personalizzata quando una richiesta non riesce a raggiungere il back-end. Per altre informazioni, vedere [pagine di errore personalizzato per il Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Tipi di listener

Esistono due tipi di listener:

- **Basic**: Questo tipo di listener è in ascolto per un sito di singolo dominio in cui ha un solo mapping DNS all'indirizzo IP del gateway applicazione. Questa configurazione del listener è necessaria quando si ospita un singolo sito dietro un gateway applicazione.
- **Multisito**: Questa configurazione del listener è necessaria quando si configura più di un'applicazione web nella stessa istanza del gateway applicazione. In questo modo è possibile configurare una topologia più efficiente per le distribuzioni aggiungendo fino a 100 siti Web a un unico gateway applicazione. Ogni sito Web può essere indirizzato al proprio pool back-end. Ad esempio:   Per i tre sottodomini, ovvero abc.contoso.com, xyz.contoso.com e pqr.contoso.com puntando all'indirizzo IP del gateway applicazione. Creare tre listener di tipo *multisito* e configurare ogni listener per la rispettiva porta e impostazione del protocollo. Per altre informazioni, vedere [hosting di più siti](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Dopo aver creato un listener, è associarlo a una regola di routing di richiesta che determina come deve essere indirizzato al back-end la richiesta ricevuta nel listener.

I listener vengono elaborati nell'ordine in cui sono visualizzati. Per questo motivo, se un listener di base corrisponde a una richiesta in ingresso viene elaborata, prima di tutto. I listener multisito devono essere configurati prima di un listener di base per garantire che il traffico sia indirizzato al back-end corretto.

## <a name="request-routing-rule"></a>Regola richiesta routing

Questo è il componente più importante del gateway applicazione e si determina come viene indirizzato il traffico nel listener associati a questa regola. La regola associa il listener, il pool di server back-end e le impostazioni HTTP di back-end. Una volta che una richiesta viene accettata dal listener, la regola di routing richiesta determina se la richiesta deve essere inoltrate al back-end o reindirizzato in un' posizione. Se la richiesta è determinata da inoltrare al back-end, la regola di routing richiesta definisce quale pool di server back-end deve essere inoltrato a. Inoltre, la regola di routing richiesta determina inoltre se devono essere riscritte le intestazioni nella richiesta. Un listener può essere collegato a una sola regola.

Possono essere presenti due tipi di regole di routing di richiesta:

- **Basic:** Tutte le richieste sul listener associati (ad esempio: blog.contoso.com/*) vengono inoltrati al pool back-end associati usando l'impostazione HTTP associato.
- **Basata sul percorso:** Questo tipo di regola consente di indirizzare le richieste sul listener associati a un pool di back-end specifico basato sull'URL nella richiesta. Se il percorso dell'URL in una richiesta corrisponde al modello di percorso in una regola basata sul percorso, la richiesta è instradata usando tale regola. Il modello di percorso viene applicato solo al percorso dell'URL, non ai relativi parametri di query. Se il percorso dell'URL di una richiesta su un listener non corrisponde ad alcuna delle regole basate sul percorso, quindi la richiesta viene indirizzata al *predefinite* pool back-end e il *predefinito* impostazioni HTTP. Per altre informazioni, vedere [routing basato su URL](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Supporto per il reindirizzamento

La regola di routing richiesta consente inoltre di reindirizzare il traffico sul gateway applicazione. Si tratta di un meccanismo di reindirizzamento generico che consente di eseguire il reindirizzamento da e verso qualsiasi porta definita mediante regole. È possibile scegliere la destinazione di reindirizzamento deve essere un altro listener (che consente di abilitare automatico da HTTP per il reindirizzamento HTTPS) o un sito esterno, scegliere il reindirizzamento temporaneo o permanente o scegliere aggiunta la stringa di query e percorso URI per l'URL reindirizzato. Per altre informazioni, vedere [reindirizzare il traffico sul Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="rewrite-http-headers"></a>Riscrivere le intestazioni HTTP

Usando le regole di routing della richiesta è possibile aggiungere, rimuovere o aggiornare HTTP (S) richiesta e intestazioni di risposta durante la richiesta e spostare pacchetti di risposta tra il pool back-end e client, tramite il gateway applicazione. Le intestazioni possono non essere impostate solo per valori statici, ma anche ad altre intestazioni e le variabili del server importanti. Ciò consentirà di eseguire diversi casi d'uso importanti, ad esempio l'estrazione di indirizzo IP del computer client, la rimozione di informazioni riservate sul back-end, l'aggiunta di ulteriori misure di sicurezza e così via. Per altre informazioni, vedere [intestazioni di HTTP riscrivere nel gateway applicazione](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>Impostazioni HTTP

Le route di gateway applicazione il traffico al back-end usando il numero di porta, protocollo Server (specificati nella regola di routing di richiesta che sono collegate le impostazioni HTTP) e altre impostazioni specificate in questo componente. La porta e protocollo usato per le impostazioni HTTP di determinare se il traffico tra i server gateway e back-end dell'applicazione è crittografato, pertanto l'esecuzione di SSL end-to-end, o non crittografati. Questo componente viene inoltre utilizzato per: determinare se una sessione utente deve rimanere nello stesso server usando il [affinità di sessione basata su cookie](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity), eseguire normale per la rimozione dei membri del pool back-end tramite [connessione lo svuotamento](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining), associare un probe personalizzato per monitorare l'integrità back-end, impostare l'intervallo di timeout della richiesta, sostituire il nome host e il percorso nella richiesta e garantire la semplicità di un solo clic per specificare l'impostazione di back-end per back-end del servizio App. 

## <a name="backend-pool"></a>Pool back-end

Il pool back-end viene utilizzato per indirizzare le richieste ai server back-end, quali rispondere alla richiesta. Pool back-end possono essere costituiti da schede di rete, set, IP indirizzi, interna IP indirizzi pubblici, FQDN e multi-tenant di scalabilità di macchine virtuali back-end, ad esempio servizio App di Azure. I membri del pool back-end gateway applicazione non sono associati a un set di disponibilità. Il Gateway applicazione può comunicare con istanze all'esterno della rete virtuale che si trova in, di conseguenza, i membri dei pool di back-end possono essere tra i data center, cluster o all'esterno di Azure, purché vi sia connettività IP. Se si prevede di usare indirizzi IP interni come membri del pool back-end, è necessario il [peering reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) o il [gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Peering reti virtuali è supportato ed è utile per il traffico di bilanciamento del carico nelle altre reti virtuali. Il gateway applicazione può anche comunicare con ai server on-premises quando sono connessi da tunnel VPN o ExpressRoute, purché il traffico è consentito.

È possibile creare pool di back-end diversi per diversi tipi di richieste. Ad esempio, creare un pool di back-end per le richieste generale e altri pool di back-end per le richieste per i microservizi per l'applicazione.

## <a name="health-probes"></a>Probe di integrità

Per impostazione predefinita il gateway applicazione monitora l'integrità di tutte le risorse nel pool back-end e rimuove automaticamente le risorse considerate non dal pool. Continua a monitorare le istanze non integre e li aggiunge al pool back-end integro dopo che sono diventate disponibili e rispondono ai probe di integrità. Oltre al monitoraggio del probe di integrità predefinito, è anche possibile personalizzare il probe di integrità in base ai requisiti dell'applicazione. I probe personalizzati consentono un controllo più granulare sul monitoraggio dell'integrità. Quando si usano i probe personalizzati, è possibile configurare l'intervallo di probe, l'URL e percorso da testare e il numero di risposte non riuscito da accettare prima che l'istanza del pool back-end sia contrassegnata come non integro. Si consiglia di configurare i probe personalizzati per monitorare l'integrità di ogni pool back-end. Per altre informazioni, vedere [monitorare l'integrità del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito familiarità con i componenti di Gateway applicazione, è possibile:
* [Creare un Gateway applicazione nel portale di Azure](quick-create-portal.md)
* [Creare un Gateway applicazione usando Azure PowerShell](quick-create-powershell.md)
* [Creare un Gateway applicazione tramite la CLI di Azure](quick-create-cli.md)
