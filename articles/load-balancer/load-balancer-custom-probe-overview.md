---
title: Usare i probe di integrità di Azure Load Balancer per ridimensionare e garantire un'elevata disponibilità per il servizio
titlesuffix: Azure Load Balancer
description: Informazioni su come usare i probe di integrità per monitorare le istanze di Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 62af688c6090b61f2596ab376cb479c270b87759
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274125"
---
# <a name="load-balancer-health-probes"></a>Probe di integrità di Load Balancer

Quando si usano le regole di bilanciamento del carico con Azure Load Balancer, è necessario specificare un probe di integrità per consentire Load Balancer di rilevare lo stato dell'endpoint back-end.  La configurazione del probe di integrità e delle risposte di probe determina quali istanze del pool back-end riceveranno nuovi flussi. È possibile usare i probe di integrità per rilevare l'errore di un'applicazione in un endpoint back-end. È anche possibile generare una risposta personalizzata per un probe di integrità e usare il probe per il controllo di flusso, al fine di gestire un carico o tempi di inattività pianificati. Quando un probe di integrità ha esito negativo, Load Balancer smetterà di inviare nuovi flussi alla rispettiva istanza non integra.

I probe di integrità supportano più protocolli. La disponibilità di uno specifico protocollo di probe di integrità varia in base Load Balancer SKU.  Inoltre, il comportamento del servizio varia in base Load Balancer SKU, come illustrato nella tabella seguente:

| | SKU Standard | SKU Basic |
| --- | --- | --- |
| [Tipi di probe](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportamento in caso di inattività dei probe](#probedown) | Tutti i probe sono inattivi, tutti i flussi TCP continuano. | Tutte le sonde vengono arrestate, tutti i flussi TCP scadono. | 


>[!IMPORTANT]
>Esaminare questo documento nel suo complesso, incluse le [indicazioni di progettazione](#design) importanti di seguito per creare un servizio Reliable Services.

>[!IMPORTANT]
>I probe di integrità di Load Balancer sono originati dall'indirizzo IP 168.63.129.16 e non devono essere bloccati perché possano contrassegnare l'istanza come attiva.  Per informazioni dettagliate, vedere [Indirizzo IP di origine dei probe](#probesource).

## <a name="probes"></a>Configurazione Probe

La configurazione del probe di integrità è costituita dagli elementi seguenti:

- Durata dell'intervallo tra i singoli Probe
- Numero di risposte del probe che devono essere osservate prima della transizione del probe a uno stato diverso
- Protocollo del probe
- Porta del probe
- Percorso HTTP da usare per HTTP GET quando si usano Probe HTTP (S)

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Informazioni sul segnale dell'applicazione, sul rilevamento del segnale e sulla reazione della piattaforma

Il numero di risposte del probe si applica a entrambi

- il numero di probe riusciti che consentono a un'istanza di essere contrassegnata come attiva e
- numero di probe non riusciti che fanno sì che un'istanza venga contrassegnata come inattiva.

I valori di timeout e intervallo specificati determinano se un'istanza verrà contrassegnata come in alto o in basso.  La durata dell'intervallo moltiplicata per il numero di risposte del probe determina il periodo di tempo durante il quale devono essere rilevate le risposte del probe.  E il servizio reagirà dopo aver completato i probe richiesti.

È possibile illustrare ulteriormente il comportamento con un esempio. Se è stato impostato il numero di risposte del Probe su 2 e l'intervallo su 5 secondi, questo significa che è necessario osservare 2 errori di probe entro un intervallo di 10 secondi.  Poiché l'ora in cui viene inviato un probe non è sincronizzata quando l'applicazione può modificare lo stato, è possibile delegare il tempo necessario per rilevare due scenari:

1. Se l'applicazione inizia a produrre una risposta probe non riuscita immediatamente prima dell'arrivo del primo Probe, il rilevamento di questi eventi imposterà 10 secondi (2 x 5 secondi di intervallo) più la durata dell'avvio dell'applicazione per segnalare un errore a quando il primo il probe arriva.  È possibile presupporre che questo rilevamento imprenda leggermente più di 10 secondi.
2. Se l'applicazione inizia a produrre una risposta probe non riuscita subito dopo l'arrivo del primo Probe, il rilevamento di questi eventi non verrà avviato fino a quando non arriva il probe successivo (e non riesce) più altri 10 secondi (intervalli di 2 x 5 secondi).  È possibile presupporre che questo rilevamento imposti solo entro 15 secondi.

Per questo esempio, una volta che si è verificato il rilevamento, la piattaforma rileverà una quantità di tempo limitata per rispondere a questa modifica.  Ciò significa che a dipende da 

1. Quando l'applicazione inizia a cambiare stato e
2. Quando questa modifica viene rilevata e soddisfa i criteri richiesti (numero di probe inviati all'intervallo specificato) e
3. Quando il rilevamento è stato comunicato attraverso la piattaforma 

è possibile presupporre che la reazione a un probe in errore riprenda tra un minimo di 10 secondi e un massimo di 15 secondi per rispondere a una modifica del segnale dall'applicazione.  Questo esempio viene fornito per illustrare gli elementi che avvengono, tuttavia, non è possibile prevedere una durata esatta oltre alle linee guida ruvide illustrate in questo esempio.
 
## <a name="types"></a>Tipi di probe

Il protocollo usato dal probe di integrità può essere configurato in uno dei seguenti elementi:

- [Listener TCP](#tcpprobe)
- [Endpoint HTTP](#httpprobe)
- [Endpoint HTTPS](#httpsprobe)

I protocolli disponibili dipendono dallo SKU Load Balancer usato:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| SKU Standard |    &#9989; |   &#9989; |   &#9989; |
| SKU Basic |   &#9989; |   &#9989; | &#10060; |

### <a name="tcpprobe"></a>Probe TCP

I probe TCP avviano una connessione tramite l'esecuzione di un handshake TCP aperto a tre vie con la porta definita,  I probe TCP terminano una connessione con un handshake TCP chiuso a quattro vie.

L'intervallo minimo del probe è di 5 secondi e il numero minimo di risposte non integre è 2.  La durata totale di tutti gli intervalli non può superare i 120 secondi.

Un probe TCP ha esito negativo quando:
* Il listener TCP sull'istanza non invia alcuna risposta durante il periodo di timeout.  Un probe viene contrassegnato in base alla configurazione del numero di richieste di probe non riuscite che possono rimanere senza risposta prima che il probe sia contrassegnato come inattivo.
* Il probe riceve un TCP Reset dall'istanza.

Di seguito viene illustrato come è possibile esprimere questo tipo di configurazione di probe in un modello di Gestione risorse:

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

>[!NOTE]
>Il probe HTTPS è disponibile solo per [Load Balancer Standard](load-balancer-standard-overview.md).

I probe HTTP e HTTPS si basano sul probe TCP ed emettono una richiesta HTTP GET con il percorso specificato. Entrambi i probe supportano i percorsi relativi per HTTP GET. I probe HTTPS sono uguali ai probe HTTP con l'aggiunta di un wrapper Transport Layer Security (TLS, precedente noto come SSL). Il probe di integrità viene contrassegnato come inattivo quando l'istanza risponde con uno stato HTTP 200 entro il periodo di timeout.  Per impostazione predefinita, il probe di integrità tenta di controllare la porta del probe di integrità configurata ogni 15 secondi. L'intervallo minimo del probe è di 5 secondi. La durata totale di tutti gli intervalli non può superare i 120 secondi.

I probe HTTP/HTTPS possono essere utili anche per implementare una logica personalizzata per rimuovere le istanze dalla rotazione del servizio di bilanciamento del carico se la porta Probe è anche il listener per il servizio stesso. Ad esempio, si potrebbe scegliere di rimuovere un'istanza se indica oltre il 90% di uso della CPU e restituisce uno stato HTTP diverso da 200. 

Se si usano servizi cloud e ruoli Web che usano w3wp.exe, si ottiene anche il monitoraggio automatico del sito Web. Gli errori nel codice del sito Web restituiscono uno stato diverso da 200 al probe del servizio di bilanciamento del carico.

Un probe HTTP/HTTPS ha esito negativo quando:
* L'endpoint del probe restituisce un codice di risposta HTTP diverso da 200, ad esempio 403, 404 o 500. In questo caso, il probe di integrità verrà contrassegnato immediatamente come inattivo. 
* L'endpoint del probe non invia alcuna risposta durante il periodo di timeout di 31 secondi. Più richieste di probe potrebbero non ricevere risposta prima che il probe venga contrassegnato come non in esecuzione e fino a quando non viene raggiunta la somma di tutti gli intervalli di timeout.
* L'endpoint del probe chiude la connessione tramite l'invio di un TCP Reset.

Di seguito viene illustrato come è possibile esprimere questo tipo di configurazione di probe in un modello di Gestione risorse:

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
## <a name="probehealth"></a>Comportamento di probe

I probe di integrità TCP, HTTP e HTTPS sono considerati integri e contrassegnano l'endpoint back-end come integro nei casi seguenti:

* Il probe di integrità ha esito positivo dopo l'avvio della macchina virtuale.
* Il numero di probe specificato richiesto per contrassegnare l'endpoint back-end come integro è stato raggiunto.

Qualsiasi endpoint back-end che ha ottenuto uno stato integro è idoneo per la ricezione di nuovi flussi.  

> [!NOTE]
> Se il probe di integrità fluttua, il servizio di bilanciamento del carico attende più a lungo prima di riportare l'endpoint back-end nello stato integro. Questo tempo di attesa aggiuntivo protegge l'utente e l'infrastruttura ed è un criterio voluto.

## <a name="probedown"></a>Comportamento in caso di inattività dei probe

### <a name="tcp-connections"></a>Connessioni TCP

Le nuove connessioni TCP riusciranno ad endpoint back-end integro rimanente.

Se il probe di integrità di un endpoint back-end non riesce, le connessioni TCP stabilite a questo endpoint back-end continuano

Se tutti i probe per tutte le istanze in un pool back-end hanno esito negativo, non verranno inviati nuovi flussi al pool back-end. Load Balancer Standard consente la continuazione dei flussi TCP stabiliti.  Load Balancer Basic terminerà tutti i flussi TCP esistenti verso il pool back-end.
 
Load Balancer è un servizio pass-through del servizio che, quindi, non termina le connessioni TCP. Il flusso si trova sempre tra il client e il sistema operativo guest e l'applicazione della macchina virtuale. Un pool con tutti i probe è inattivo perché un front-end non risponde ai tentativi di apertura della connessione TCP (SYN) perché non è presente un endpoint back-end integro per ricevere il flusso e rispondere con un SYN-ACK.

### <a name="udp-datagrams"></a>Datagrammi UDP

I datagrammi UDP verranno recapitati a endpoint back-end integri.

UDP è senza connessione e non esiste uno stato di flusso monitorato per l'UDP. Se il probe di integrità di un endpoint back-end ha esito negativo, i flussi UDP esistenti possono passare a un'altra istanza integra nel pool back-end

Se tutti i probe per un pool di back-end hanno esito negativo, i flussi UDP esistenti verranno interrotti per Load Balancer Standard e Basic.

<a name="source"></a>
## <a name="probesource"></a>Indirizzo IP di origine dei probe

Load Balancer usa un servizio distribuito probe per il suo modello di integrità interno. Ogni host in cui si trovano le macchine virtuali include un servizio di esecuzione del probe che può essere programmato per generare i probe di integrità sulla base della configurazione del cliente. Il traffico probe dell'integrità si trova direttamente tra il servizio di esecuzione del probe che genera il probe di integrità e la macchine virtuale del cliente. Tutti i probe di integrità di Load Balancer sono originati dall'indirizzo IP 168.63.129.16.  È possibile usare lo spazio di indirizzi IP all'interno di una rete virtuale che non sia uno spazio RFC1918.  L'utilizzo di un indirizzo IP di proprietà di Microsoft e riservato a livello globale riduce il rischio di conflitti di indirizzo con lo spazio indirizzi IP usato all'interno della rete virtuale.  Questo indirizzo IP è uguale in tutte le aree e non cambia. Non rappresenta neppure un rischio per la sicurezza poiché solo il componente interno della piattaforma Azure può generare un pacchetto da questo indirizzo IP. 

Il tag del servizio AzureLoadBalancer identifica questo indirizzo IP di origine nei [gruppi di sicurezza di rete](../virtual-network/security-overview.md) e consente il traffico dei probe di integrità per impostazione predefinita.

Oltre ai Probe di integrità di Load Balancer, le [operazioni seguenti usano questo indirizzo IP](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Abilitazione dell'agente di macchine virtuali per la comunicazione con la piattaforma per segnalare che si trova in uno stato "Pronto"
- Abilitazione della comunicazione con il server virtuale DNS per fornire la risoluzione dei nomi filtrati per i clienti che non definiscono i server DNS personalizzati.  Questo filtro garantisce che i clienti possano risolvere solo i nomi host della loro distribuzione.
- Consente alla macchina virtuale di ottenere un indirizzo IP dinamico dal servizio DHCP in Azure.

## <a name="design"></a> Indicazioni per la progettazione

I probe di integrità consentono di aumentare la resilienza del servizio e di ridimensionarlo. Una configurazione errata o un modello di struttura non valido può compromettere la disponibilità e la scalabilità del servizio. Esaminare l'intero documento e tenere in considerazione l'impatto per il proprio scenario nel caso la risposta del probe venga contrassegnata come inattiva o attiva, nonché per la disponibilità della propria applicazione.

Quando si progetta il modello di integrità per l'applicazione, è consigliabile verificare una porta in un endpoint back-end che rifletta l'integrità dell'istanza __e__ il servizio dell'applicazione fornito.  La porta dell'applicazione e la porta probe non devono necessariamente corrispondere.  In alcuni scenari, potrebbe essere utile differenziare la porta probe dalla porta con cui l'applicazione fornisce il servizio.  

In alcuni casi può essere utile per l'applicazione generare una risposta del probe di integrità non solo per rilevare l'integrità dell'applicazione, ma anche per segnalare direttamente a Load Balancer se l'istanza deve ricevere o meno nuovi flussi.  È possibile modificare la risposta del probe per consentire all'applicazione di creare congestione e di limitare la distribuzione di nuovi flussi a un'istanza interrompendo il probe di integrità o prepararsi per la manutenzione dell'applicazione e avviare lo svuotamento dello scenario.  Quando si usa Load Balancer Standard, un segnale di [probe inattivo](#probedown) consentirà sempre ai flussi TCP di continuare fino al timeout o alla chiusura della connessione. 

Per il bilanciamento del carico UDP, è necessario generare un segnale di probe di integrità personalizzato dall'endpoint back-end e usare un probe di integrità TCP, HTTP o HTTPS destinato al listener corrispondente per riflettere l'integrità dell'applicazione UDP.

Quando si usano [regole di bilanciamento del carico per porte a disponibilità elevata](load-balancer-ha-ports-overview.md) con [Load Balancer Standard](load-balancer-standard-overview.md), viene eseguito il bilanciamento del carico di tutte le porte e una singola risposta del probe di integrità deve riflettere lo stato dell'intera istanza.

Bon inviare mediante NAT o proxy un probe di integrità tramite l'istanza che riceve il probe di integrità a un'altra istanza nella rete virtuale, perché questa configurazione può causare errori a catena nello scenario.  Si consideri lo scenario seguente: un set di appliance di terze parti viene distribuito nel pool di back-end di una risorsa di Load Balancer per offrire scalabilità e ridondanza per le appliance; il probe di integrità è configurato per eseguire il probe di una porta usata come proxy dall'appliance di terze parti o convertita da quest'ultima ad altre macchine virtuali dietro il dispositivo.  Se si esegue il probe della stessa porta in uso per la conversione o per le richieste di proxy alle altre macchine virtuali dietro l'appliance, ogni risposta di probe da una singola macchina virtuale dietro l'appliance contrassegnerà quest'ultima come inattiva. Questa configurazione può causare un errore a catena dell'intero scenario dell'applicazione in seguito a un singolo endpoint back-end dietro l'appliance.  Il trigger può essere un errore intermittente del probe che farà sì che Load Balancer contrassegni come inattiva la destinazione originale, ovvero l'istanza dell'appliance, rischiando di disabilitare di conseguenza lo scenario dell'intera applicazione. Eseguire invece il probe dell'integrità dell'appliance stessa. La selezione del probe per determinare il segnale di integrità è una considerazione importante per gli scenari con appliance virtuali di rete ed è necessario consultare il fornitore dell'applicazione per informazioni sulle opzioni di segnali di integrità disponibili per tali scenari.

Se non si consente l'[indirizzo IP di origine](#probesource) nei criteri firewall, il probe di integrità avrà esito negativo, perché non sarà in grado di raggiungere l'istanza.  A sua volta, Load Balancer contrassegnerà l'istanza come inattiva a causa dell'errore del probe di integrità.  Questo errore di configurazione può causare un esito negativo nello scenario dell'applicazione con bilanciamento di carico.

Per consentire al probe di integrità di Load Balancer di contrassegnare l'istanza come attiva, è **necessario** autorizzare questo indirizzo IP in tutti i criteri firewall locali e i [gruppi di sicurezza di rete](../virtual-network/security-overview.md) di Azure.  Per impostazione predefinita, ogni gruppo di sicurezza di rete include il [tag del servizio](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer per consentire il traffico del probe di integrità.

Se si vuole testare un errore del probe di integrità o contrassegnare una singola istanza come inattiva, è possibile usare un [gruppo di sicurezza di rete](../virtual-network/security-overview.md) per bloccare esplicitamente il probe di integrità (porta di destinazione o [indirizzo IP di origine](#probesource)) e simulare l'esito negativo di un probe.

Evitare di configurare la rete virtuale con l'intervallo di indirizzi IP di proprietà di Microsoft, che contiene 168.63.129.16.  Tali configurazioni entreranno in conflitto con l'indirizzo IP del probe di integrità e potrebbero causare un esito negativo dello scenario.

Se si dispone di più interfacce nella macchina virtuale, è necessario assicurarsi di rispondere al probe nell'interfaccia su cui è stato ricevuto.  Potrebbe essere necessario generare l'indirizzo di rete e convertirlo nella macchina virtuale in base a ogni interfaccia.

Non abilitare i [timestamp TCP](https://tools.ietf.org/html/rfc1323).  L'abilitazione dei timestamp TCP può causare l'esito negativo dei probe di integrità a causa dell'eliminazione dei pacchetti TCP dallo stack TCP del sistema operativo guest della macchina virtuale, il che determina Load Balancer contrassegnare il rispettivo endpoint.  I timestamp TCP sono abilitati periodicamente per impostazione predefinita sulle immagini delle macchine virtuali con sicurezza elevata e devono essere disattivati.

## <a name="monitoring"></a>Monitoraggio

Sia public che Internal [Load Balancer standard](load-balancer-standard-overview.md) espongono lo stato del probe di integrità per endpoint e endpoint back-end come metriche multidimensionali tramite monitoraggio di Azure. Queste metriche possono essere utilizzate da altri servizi di Azure o da applicazioni partner. 

Il Load Balancer pubblico di base espone lo stato del probe di integrità per ogni pool back-end tramite log di monitoraggio di Azure.  I log di monitoraggio di Azure non sono disponibili per i bilanciamenti del carico di base interni.  È possibile usare i [log di monitoraggio di Azure](load-balancer-monitor-log.md) per verificare lo stato di integrità e il numero di probe del servizio di bilanciamento del carico pubblico. La registrazione può essere usata con Power BI o Azure Operational Insights per fornire statistiche sullo stato di integrità del servizio di bilanciamento del carico.

## <a name="limitations"></a>Limitazioni

- I probe HTTPS non supportano l'autenticazione reciproca con un certificato client.
- Se i timestamp TCP sono abilitati, i probe assumehHealth non riusciranno.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Load Balancer Standard](load-balancer-standard-overview.md)
- [Introduzione alla creazione di un servizio di bilanciamento del carico pubblico in Resource Manager con PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [API REST per i probe di integrità](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Richiedere nuove funzionalità dei probe di integrità con [Uservoice di Load Balancer](https://aka.ms/lbuservoice)
