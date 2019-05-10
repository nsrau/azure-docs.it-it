---
title: Usare i probe di integrità di Azure Load Balancer per ridimensionare e garantire un'elevata disponibilità per il servizio
titlesuffix: Azure Load Balancer
description: Informazioni su come usare i probe di integrità per monitorare le istanze di Load Balancer
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: kumud
ms.openlocfilehash: e488a4a6438279270f3d86dafa16c45eda184059
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415716"
---
# <a name="load-balancer-health-probes"></a>Probe di integrità di Load Balancer

Azure Load Balancer mette a disposizione probe di integrità da usare con le regole di bilanciamento del carico.  La configurazione e le risposte dei probe di integrità determinano quale istanza del pool back-end deve ricevere nuovi flussi. È possibile utilizzare i probe di integrità per rilevare l'errore di un'applicazione in un'istanza di back-end. È anche possibile generare una risposta personalizzata per un probe di integrità e usare il probe per il controllo di flusso, al fine di gestire un carico o tempi di inattività pianificati. Se il probe di integrità non riesce, Load Balancer interrompe l'invio di nuovi flussi alla rispettiva istanza non integra.

I probe di integrità supportano più protocolli. La disponibilità di un tipo specifico di probe di integrità per supportare un protocollo specifico dipende dalla SKU di Load Balancer.  Anche il comportamento del servizio varia a seconda della SKU di Load Balancer.

| | SKU Standard | SKU Basic |
| --- | --- | --- |
| [Tipi di probe](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportamento in caso di inattività dei probe](#probedown) | Tutti i probe sono inattivi, tutti i flussi TCP continuano. | Tutti i probe verso il basso, tutti i flussi TCP scadono. | 

> [!IMPORTANT]
> I probe di integrità di Load Balancer sono originati dall'indirizzo IP 168.63.129.16 e non devono essere bloccati perché possano contrassegnare l'istanza come attiva.  Per informazioni dettagliate, vedere [Indirizzo IP di origine dei probe](#probesource).

## <a name="types"></a>Tipi di probe

Il probe di integrità può essere configurato per listener tramite i tre protocolli seguenti:

- [Listener TCP](#tcpprobe)
- [Endpoint HTTP](#httpprobe)
- [Endpoint HTTPS](#httpsprobe)

I tipi di probe di integrità disponibili variano in base allo SKU di Load Balancer selezionato:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| SKU Standard |    &#9989; |   &#9989; |   &#9989; |
| SKU Basic |   &#9989; |   &#9989; | &#10060; |

A prescindere dal tipo di probe di integrità scelto, esso può osservare qualsiasi porta in un'istanza di back-end, tra cui la porta su cui viene effettivamente fornito il servizio.

### <a name="tcpprobe"></a>Probe TCP

I probe TCP avviano una connessione tramite l'esecuzione di un handshake TCP aperto a tre vie con la porta definita,  I probe TCP terminano una connessione con un handshake TCP chiuso a quattro vie.

L'intervallo minimo del probe è di 5 secondi e il numero minimo di risposte non integre è 2.  La durata totale di tutti gli intervalli non può superare i 120 secondi.

Un probe TCP ha esito negativo quando:
* Il listener TCP sull'istanza non invia alcuna risposta durante il periodo di timeout.  Un probe viene contrassegnato in base alla configurazione del numero di richieste di probe non riuscite che possono rimanere senza risposta prima che il probe sia contrassegnato come inattivo.
* Il probe riceve un TCP Reset dall'istanza.

#### <a name="resource-manager-template"></a>Modello di Resource Manager

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="httpprobe"></a> <a name="httpsprobe"></a> Probe HTTP/HTTPS

> [!NOTE]
> Il probe HTTPS è disponibile solo per [Load Balancer Standard](load-balancer-standard-overview.md).

I probe HTTP e HTTPS si basano sul probe TCP ed emettono una richiesta HTTP GET con il percorso specificato. Entrambi i probe supportano i percorsi relativi per HTTP GET. I probe HTTPS sono uguali ai probe HTTP con l'aggiunta di un wrapper Transport Layer Security (TLS, precedente noto come SSL). Il probe di integrità viene contrassegnato come inattivo quando l'istanza risponde con uno stato HTTP 200 entro il periodo di timeout.  Per impostazione predefinita, il probe di integrità tenta di controllare la porta del probe di integrità configurata ogni 15 secondi. L'intervallo minimo del probe è di 5 secondi. La durata totale di tutti gli intervalli non può superare i 120 secondi.

I probe HTTP/HTTPS sono utili anche se si desidera esprimere un probe di integrità.  implementare la logica personalizzata per rimuovere le istanze dalla rotazione di bilanciamento del carico se la porta probe è anche il listener per il servizio stesso. Ad esempio, si potrebbe scegliere di rimuovere un'istanza se indica oltre il 90% di uso della CPU e restituisce uno stato HTTP diverso da 200. 

Se si usano servizi cloud e ruoli Web che usano w3wp.exe, si ottiene anche il monitoraggio automatico del sito Web. Gli errori nel codice del sito Web restituiscono uno stato diverso da 200 al probe del servizio di bilanciamento del carico.

Un probe HTTP/HTTPS ha esito negativo quando:
* L'endpoint del probe restituisce un codice di risposta HTTP diverso da 200, ad esempio 403, 404 o 500. In questo caso, il probe di integrità verrà contrassegnato immediatamente come inattivo. 
* L'endpoint del probe non invia alcuna risposta durante il periodo di timeout di 31 secondi. Più richieste di probe potrebbero non ricevere risposta prima che il probe venga contrassegnato come non in esecuzione e fino a quando non viene raggiunta la somma di tutti gli intervalli di timeout.
* L'endpoint del probe chiude la connessione tramite l'invio di un TCP Reset.

#### <a name="resource-manager-templates"></a>Modelli di Gestione risorse

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guestagent"></a>Probe dell'agente guest (solo servizi classici)

Per impostazione predefinita, i ruoli del servizio cloud, ovvero i ruoli di lavoro e i ruoli Web, usano un agente guest per il monitoraggio probe.  Un probe dell'agente guest rappresenta un'ultima possibilità di configurazione.  Definire sempre un probe di integrità in modo esplicito con un probe TCP o HTTP. Un probe dell'agente guest non è altrettanto efficace dei probe definiti in modo esplicito per la maggior parte degli scenari di applicazione.

Un probe dell'agente guest è un controllo dell'agente guest all'interno della macchina virtuale. L'agente guest è quindi in ascolto e risponde con HTTP 200 OK solo quando l'istanza è in stato Pronto. Gli altri stati sono Occupato, Riciclo in corso o Arresto.

Per altre informazioni, vedere [Configure the service definition file (csdef) for health probes](https://msdn.microsoft.com/library/azure/ee758710.aspx) (Configurare il file csdef per probe di integrità) o [Get started by creating a public load balancer for cloud services](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services) (Introduzione alla creazione di un servizio di bilanciamento del carico pubblico per i servizi cloud).

Se l'agente guest non risponde con un messaggio HTTP 200 OK, il servizio di bilanciamento del carico contrassegna l'istanza come istanza che non risponde. Il servizio smette quindi di inviare flussi all'istanza. Il servizio di bilanciamento del carico continua a controllare l'istanza. 

Se l'agente guest risponde con un messaggio HTTP 200, il servizio di bilanciamento del carico continua a inviare i nuovi flussi a tale istanza.

Quando si usa un ruolo Web, il codice del sito Web viene in genere eseguito in w3wp.exe, che non è monitorato dall'infrastruttura di Azure o dall'agente guest. Gli errori in w3wp.exe, ad esempio le risposte HTTP 500, non vengono segnalati all'agente guest. Di conseguenza, il servizio di bilanciamento del carico non rimuove l'istanza dalla rotazione.

<a name="health"></a>
## <a name="probehealth"></a>Integrità dei probe

I probe di integrità TCP, HTTP e HTTPS sono considerati integri e contrassegnano come integra l'istanza del ruolo quando:

* Il probe di integrità ha esito positivo dopo l'avvio della macchina virtuale.
* Raggiunto il numero specificato di probe necessari per contrassegnare l'istanza del ruolo come integra.

> [!NOTE]
> Se l'integrità di un probe varia, il servizio di bilanciamento del carico attende più a lungo prima di ripristinarne lo stato di integrità. Questo tempo di attesa aggiuntivo protegge l'utente e l'infrastruttura ed è un criterio voluto.

## <a name="probe-count-and-timeout"></a>Conteggio e timeout dei probe

Il comportamento dei probe dipende dagli elementi seguenti:

* Numero di probe riusciti che consentono di contrassegnare un'istanza come attiva.
* Numero di probe non riusciti che fanno sì che un'istanza sia contrassegnata come inattiva.

I valori di timeout e di intervallo specificati determinano se un'istanza viene contrassegnata come attiva o inattiva.

## <a name="probedown"></a>Comportamento in caso di inattività dei probe

### <a name="tcp-connections"></a>Connessioni TCP

Le nuove connessioni TCP avranno esito positivo per le istanze di back-end integre rimanenti.

Se il probe di integrità dell'istanza di back-end ha esito negativo, le connessioni TCP stabilite a questa istanza di back-end continuano.

Se tutti i probe per tutte le istanze in un pool back-end hanno esito negativo, non verranno inviati nuovi flussi al pool back-end. Load Balancer Standard consente la continuazione dei flussi TCP stabiliti.  Load Balancer Basic terminerà tutti i flussi TCP esistenti verso il pool back-end.
 
Load Balancer è un servizio pass-through del servizio che, quindi, non termina le connessioni TCP. Il flusso si trova sempre tra il client e il sistema operativo guest e l'applicazione della macchina virtuale. Un pool con tutti i probe inattivi impedirà a un front-end di rispondere ai tentativi di apertura di una connessione TCP, perché non sarà presente un'istanza di back-end integra per la ricezione del flusso e la risposta con SYN-ACK.

### <a name="udp-datagrams"></a>Datagrammi UDP

I datagrammi UDP verranno recapitati alle istanze di back-end integre.

UDP è senza connessione e non esiste uno stato di flusso monitorato per l'UDP. Se un probe di integrità dell'istanza di back-end ha esito negativo, i flussi UDP esistenti possono passare a un'altra istanza integra nel pool di back-end.

Se tutti i probe per un pool di back-end hanno esito negativo, i flussi UDP esistenti verranno interrotti per Load Balancer Standard e Basic.

<a name="source"></a>
## <a name="probesource"></a>Indirizzo IP di origine dei probe

Load Balancer usa un servizio distribuito probe per il suo modello di integrità interno. Ogni host in cui si trovano le macchine virtuali include un servizio di esecuzione del probe che può essere programmato per generare i probe di integrità sulla base della configurazione del cliente. Il traffico probe dell'integrità si trova direttamente tra il servizio di esecuzione del probe che genera il probe di integrità e la macchine virtuale del cliente. Tutti i probe di integrità di Load Balancer sono originati dall'indirizzo IP 168.63.129.16.  È possibile usare lo spazio di indirizzi IP all'interno di una rete virtuale che non sia uno spazio RFC1918.  L'utilizzo di un indirizzo IP di proprietà di Microsoft e riservato a livello globale riduce il rischio di conflitti di indirizzo con lo spazio indirizzi IP usato all'interno della rete virtuale.  Questo indirizzo IP è uguale in tutte le aree e non cambia. Non rappresenta neppure un rischio per la sicurezza poiché solo il componente interno della piattaforma Azure può generare un pacchetto da questo indirizzo IP. 

Il tag del servizio AzureLoadBalancer identifica questo indirizzo IP di origine nei [gruppi di sicurezza di rete](../virtual-network/security-overview.md) e consente il traffico dei probe di integrità per impostazione predefinita.

Oltre ai probe di integrità di bilanciamento del carico, il [operazioni seguenti usano questo indirizzo IP](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Abilitazione dell'agente di macchine virtuali per la comunicazione con la piattaforma per segnalare che si trova in uno stato "Pronto"
- Abilitazione della comunicazione con il server virtuale DNS per fornire la risoluzione dei nomi filtrati per i clienti che non definiscono i server DNS personalizzati.  Questo filtro garantisce che i clienti possano risolvere solo i nomi host della loro distribuzione.
- Consente alla macchina virtuale di ottenere un indirizzo IP dinamico dal servizio DHCP in Azure.

## <a name="design"></a> Indicazioni per la progettazione

I probe di integrità consentono di aumentare la resilienza del servizio e di ridimensionarlo. Una configurazione errata o un modello di struttura non valido può compromettere la disponibilità e la scalabilità del servizio. Esaminare l'intero documento e tenere in considerazione l'impatto per il proprio scenario nel caso la risposta del probe venga contrassegnata come inattiva o attiva, nonché per la disponibilità della propria applicazione.

Quando si progetta il modello di integrità per l'applicazione, è consigliabile eseguire il probe di una porta in un'istanza di back-end che rifletta l'integrità dell'istanza __e__ del servizio dell'applicazione fornito.  La porta dell'applicazione e la porta probe non devono necessariamente corrispondere.  In alcuni scenari, potrebbe essere utile differenziare la porta probe dalla porta con cui l'applicazione fornisce il servizio.  

In alcuni casi può essere utile per l'applicazione generare una risposta del probe di integrità non solo per rilevare l'integrità dell'applicazione, ma anche per segnalare direttamente a Load Balancer se l'istanza deve ricevere o meno nuovi flussi.  È possibile modificare la risposta del probe per consentire all'applicazione di creare congestione e di limitare la distribuzione di nuovi flussi a un'istanza interrompendo il probe di integrità o prepararsi per la manutenzione dell'applicazione e avviare lo svuotamento dello scenario.  Quando si usa Load Balancer Standard, un segnale di [probe inattivo](#probedown) consentirà sempre ai flussi TCP di continuare fino al timeout o alla chiusura della connessione. 

Per il bilanciamento del carico UDP, si deve generare un segnale di probe di integrità personalizzato dall'istanza del back-end e usare un probe di integrità TCP, HTTP o HTTPS che abbia come destinazione il listener corrispondente in modo da riflettere l'integrità dell'applicazione UDP.

Quando si usano [regole di bilanciamento del carico per porte a disponibilità elevata](load-balancer-ha-ports-overview.md) con [Load Balancer Standard](load-balancer-standard-overview.md), viene eseguito il bilanciamento del carico di tutte le porte e una singola risposta del probe di integrità deve riflettere lo stato dell'intera istanza.

Bon inviare mediante NAT o proxy un probe di integrità tramite l'istanza che riceve il probe di integrità a un'altra istanza nella rete virtuale, perché questa configurazione può causare errori a catena nello scenario.  Si consideri lo scenario seguente: un set di appliance di terze parti viene distribuito nel pool di back-end di una risorsa di Load Balancer per offrire scalabilità e ridondanza per le appliance; il probe di integrità è configurato per eseguire il probe di una porta usata come proxy dall'appliance di terze parti o convertita da quest'ultima ad altre macchine virtuali dietro il dispositivo.  Se si esegue il probe della stessa porta in uso per la conversione o per le richieste di proxy alle altre macchine virtuali dietro l'appliance, ogni risposta di probe da una singola macchina virtuale dietro l'appliance contrassegnerà quest'ultima come inattiva. Questa configurazione può causare un errore a catena nello scenario dell'intera applicazione come risultato di una singola istanza di back-end dietro l'appliance.  Il trigger può essere un errore intermittente del probe che farà sì che Load Balancer contrassegni come inattiva la destinazione originale, ovvero l'istanza dell'appliance, rischiando di disabilitare di conseguenza lo scenario dell'intera applicazione. Eseguire invece il probe dell'integrità dell'appliance stessa. La selezione del probe per determinare il segnale di integrità è una considerazione importante per gli scenari con appliance virtuali di rete ed è necessario consultare il fornitore dell'applicazione per informazioni sulle opzioni di segnali di integrità disponibili per tali scenari.

Se non si consente l'[indirizzo IP di origine](#probesource) nei criteri firewall, il probe di integrità avrà esito negativo, perché non sarà in grado di raggiungere l'istanza.  A sua volta, Load Balancer contrassegnerà l'istanza come inattiva a causa dell'errore del probe di integrità.  Questo errore di configurazione può causare un esito negativo nello scenario dell'applicazione con bilanciamento di carico.

Per consentire al probe di integrità di Load Balancer di contrassegnare l'istanza come attiva, è **necessario** autorizzare questo indirizzo IP in tutti i criteri firewall locali e i [gruppi di sicurezza di rete](../virtual-network/security-overview.md) di Azure.  Per impostazione predefinita, ogni gruppo di sicurezza di rete include il [tag del servizio](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer per consentire il traffico del probe di integrità.

Se si vuole testare un errore del probe di integrità o contrassegnare una singola istanza come inattiva, è possibile usare un [gruppo di sicurezza di rete](../virtual-network/security-overview.md) per bloccare esplicitamente il probe di integrità (porta di destinazione o [indirizzo IP di origine](#probesource)) e simulare l'esito negativo di un probe.

Evitare di configurare la rete virtuale con l'intervallo di indirizzi IP di proprietà di Microsoft, che contiene 168.63.129.16.  Tali configurazioni entreranno in conflitto con l'indirizzo IP del probe di integrità e potrebbero causare un esito negativo dello scenario.

Se si dispone di più interfacce nella macchina virtuale, è necessario assicurarsi di rispondere al probe nell'interfaccia su cui è stato ricevuto.  Potrebbe essere necessario generare l'indirizzo di rete e convertirlo nella macchina virtuale in base a ogni interfaccia.

Non abilitare i [timestamp TCP](https://tools.ietf.org/html/rfc1323).  Se vengono abilitati i timestamp TCP, i probe di integrità avranno esito negativo perché i pacchetti TCP verranno eliminati dallo stack TCP del sistema operativo guest della macchina virtuale, cosa che porterà Load Balancer a contrassegnare come inattivo l'endpoint corrispondente.  I timestamp TCP sono abilitati periodicamente per impostazione predefinita sulle immagini delle macchine virtuali con sicurezza elevata e devono essere disattivati.

## <a name="monitoring"></a>Monitoraggio

Entrambi i servizi [Load Balancer Standard](load-balancer-standard-overview.md) pubblico e interno espongono lo stato dei probe di integrità come una metrica multidimensionale per ogni istanza di back-end e per ogni endpoint tramite Monitoraggio di Azure. Queste metriche possono essere utilizzate da altri servizi di Azure o applicazioni di partner. 

Base pubblica di Load Balancer espone lo stato probe di integrità riepilogato per ogni pool back-end con i log di monitoraggio di Azure.  Log di monitoraggio di Azure non sono disponibili per i bilanciamenti del carico Basic interna.  È possibile usare [monitoraggio di Azure registra](load-balancer-monitor-log.md) per controllare lo stato di integrità probe del servizio di bilanciamento carico pubblico e conteggio dei probe. La registrazione può essere usata con Power BI o Azure Operational Insights per fornire statistiche sullo stato di integrità del servizio di bilanciamento del carico.

## <a name="limitations"></a>Limitazioni

- I probe HTTPS non supportano l'autenticazione reciproca con un certificato client.
- I probe di integrità avranno esito negativo se sono abilitati i timestamp TCP.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Load Balancer Standard](load-balancer-standard-overview.md)
- [Introduzione alla creazione di un servizio di bilanciamento del carico pubblico in Resource Manager con PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [API REST per i probe di integrità](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Richiedere nuove funzionalità dei probe di integrità con [Uservoice di Load Balancer](https://aka.ms/lbuservoice)
