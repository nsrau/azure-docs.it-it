---
title: Guida alle prestazioni per il Servizio Azure SignalR
description: Panoramica delle prestazioni e del benchmark del servizio SignalR di Azure.An overview of the performance and benchmark of Azure SignalR Service. Metriche chiave da considerare quando si pianifica la capacità.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74157662"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guida alle prestazioni per il Servizio Azure SignalR

Uno dei vantaggi principali dell'uso del servizio SignalR di Azure è la facilità di scalabilità delle applicazioni SignalR.One of the key benefits of using Azure SignalR Service is the ease of scaling SignalR applications. In uno scenario su larga scala, le prestazioni sono un fattore importante. 

In questa guida verranno introdotti i fattori che influiscono sulle prestazioni dell'applicazione SignalR.In this guide, we'll introduce the factors that affect SignalR application performance. Descriveremo le prestazioni tipiche in diversi scenari di casi d'uso. Alla fine, introdurremo l'ambiente e gli strumenti che è possibile utilizzare per generare un rapporto sul rendimento.

## <a name="term-definitions"></a>Definizioni dei termini

*In ingresso:* il messaggio in arrivo nel servizio SignalR di Azure.Inbound : The incoming message to Azure SignalR Service.

*In uscita:* il messaggio in uscita dal servizio SignalR di Azure.Outbound : The outgoing message from Azure SignalR Service.

*Larghezza di banda*: La dimensione totale di tutti i messaggi in 1 secondo.

*Modalità predefinita:* modalità di lavoro predefinita quando è stata creata un'istanza del servizio SignalR di Azure.Default mode : The default working mode when an Azure SignalR Service instance was created. Il servizio SignalR di Azure prevede che il server app stabilisca una connessione con esso prima di accettare le connessioni client.

*Modalità senza server:* modalità in cui il servizio SignalR di Azure accetta solo connessioni client. Non è consentita alcuna connessione al server.

## <a name="overview"></a>Panoramica

Il servizio SignalR di Azure definisce sette livelli Standard per capacità di prestazioni diverse. Questa guida risponde alle seguenti domande:

-   Qual è il tipico servizio SignalR di Azure per ogni livello?

-   Il servizio SignalR di Azure soddisfa i requisiti per la velocità effettiva dei messaggi, ad esempio l'invio di 100.000 messaggi al secondo?

-   Per il mio scenario specifico, quale livello è adatto a me? Oppure come posso selezionare il livello corretto?

-   Che tipo di server applicazioni (dimensioni VM) è adatto a me? Quanti di essi è necessario distribuire?

Per rispondere a queste domande, questa guida fornisce innanzitutto una spiegazione di alto livello dei fattori che influiscono sulle prestazioni. Viene quindi illustrato il numero massimo di messaggi in ingresso e in uscita per ogni livello per i casi d'uso tipici: **echo**, **broadcast**, send **to group**e send **to connection** (chat peer-to-peer).

Questa guida non può coprire tutti gli scenari (e diversi casi d'uso, dimensioni dei messaggi, modelli di invio di messaggi e così via). Ma fornisce alcuni metodi per aiutarti:

- Valutare il requisito approssimativo per i messaggi in ingresso o in uscita.
- Trovare i livelli appropriati controllando la tabella delle prestazioni.

## <a name="performance-insight"></a>Informazioni dettagliate sulle prestazioni

In questa sezione vengono descritte le metodologie di valutazione delle prestazioni e quindi vengono elencati tutti i fattori che influiscono sulle prestazioni. Alla fine, fornisce metodi che consentono di valutare i requisiti di prestazioni.

### <a name="methodology"></a>Metodologia

*La velocità effettiva* e la *latenza* sono due aspetti tipici del controllo delle prestazioni. Per il servizio SignalR di Azure, ogni livello SKU ha i propri criteri di limitazione della velocità effettiva. Il criterio definisce *la velocità effettiva massima consentita (larghezza* di banda in ingresso e in uscita) come velocità effettiva massima raggiunta quando il 99% dei messaggi ha una latenza inferiore a 1 secondo.

Latenza è l'intervallo di tempo dalla connessione che invia il messaggio alla ricezione del messaggio di risposta dal servizio SignalR di Azure.Latency is the time span from the connection sending the message to receiving the response message from Azure SignalR Service. Prendiamo **l'eco** come esempio. Ogni connessione client aggiunge un timestamp nel messaggio. L'hub del server applicazioni invia il messaggio originale al client. Pertanto, il ritardo di propagazione viene facilmente calcolato da ogni connessione client. Il timestamp viene allegato per ogni messaggio in **broadcast**, **inviato al gruppo**e invio a **connessione.**

Per simulare migliaia di connessioni client simultanee, vengono create più macchine virtuali in una rete privata virtuale in Azure.To simulate thousands of concurrent client connections, multiple VMs are created in a virtual private network in Azure. Tutte queste macchine virtuali si connettono alla stessa istanza del servizio SignalR di Azure.All of these VMs connect to the same Azure SignalR Service instance.

Nella modalità predefinita del servizio SignalR di Azure, le macchine virtuali del server applicazioni vengono distribuite nella stessa rete privata virtuale delle macchine virtuali client. Tutte le macchine virtuali client e le macchine virtuali del server applicazioni vengono distribuite nella stessa rete della stessa area per evitare latenza tra aree.

### <a name="performance-factors"></a>Fattori relativi alle prestazioni

Teoricamente, la capacità del servizio SignalR di Azure è limitata dalle risorse di calcolo: CPU, memoria e rete. Ad esempio, più connessioni al servizio SignalR di Azure fanno sì che il servizio utilizzi più memoria. Per un traffico di messaggi maggiore (ad esempio, ogni messaggio è maggiore di 2.048 byte), il servizio SignalR di Azure deve dedicare più cicli di CPU per elaborare il traffico. Nel frattempo, la larghezza di banda di rete di Azure impone anche un limite per il traffico massimo.

Il tipo di trasporto è un altro fattore che influisce sulle prestazioni. I tre tipi sono [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [Server-Sent-Event](https://en.wikipedia.org/wiki/Server-sent_events)e [Long-Polling](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket è un protocollo di comunicazione bidirezionale e full-duplex su una singola connessione TCP. Server-Sent-Event è un protocollo unidirezionale per il push dei messaggi da server a client. Il polling prolungato richiede che i client eserino periodicamente le informazioni dal server tramite una richiesta HTTP. Per la stessa API nelle stesse condizioni, WebSocket ha le migliori prestazioni, Server-Sent-Event è più lento e Long-Polling è il più lento. Il servizio SignalR di Azure consiglia WebSocket per impostazione predefinita.

Il costo di routing dei messaggi limita anche le prestazioni. Il servizio SignalR di Azure svolge un ruolo come router di messaggi, che instrada il messaggio da un set di client o server ad altri client o server. Uno scenario o un'API diversa richiedono criteri di routing diversi. 

Per **echo**, il client invia un messaggio a se stesso e anche la destinazione di routing è se stessa. Questo modello ha il costo di routing più basso. Ma per **broadcast**, **inviare al gruppo**e **inviare a connessione**, Il servizio SignalR di Azure deve cercare le connessioni di destinazione tramite la struttura di dati distribuiti interni. Questa elaborazione aggiuntiva utilizza più CPU, memoria e larghezza di banda di rete. Di conseguenza, le prestazioni sono più lente.

Nella modalità predefinita, il server applicazioni potrebbe anche diventare un collo di bottiglia per determinati scenari. L'SDK SignalR di Azure deve richiamare l'hub, mentre mantiene una connessione attiva con ogni client tramite segnali di heartbeat.

In modalità senza server, il client invia un messaggio tramite post HTTP, che non è efficiente come WebSocket.

Un altro fattore è il protocollo: JSON e [MessagePack](https://msgpack.org/index.html). MessagePack ha dimensioni inferiori e viene recapitato più velocemente di JSON. MessagePack potrebbe non migliorare le prestazioni, tuttavia. Le prestazioni del servizio SignalR di Azure non sono sensibili ai protocolli perché non decodificano il payload del messaggio durante l'inoltro dei messaggi dai client ai server o viceversa.

In sintesi, i seguenti fattori influiscono sulla capacità in entrata e in uscita:

-   Livello SKU (CPU/memoria)

-   Numero di connessioni

-   Dimensioni dei messaggi

-   Frequenza di invio dei messaggi

-   Tipo di trasporto (WebSocket, Server-Sent-Event o Long-Polling)

-   Scenario di caso d'uso (costo di routing)

-   Connessioni server applicazioni e servizi (in modalità server)


### <a name="finding-a-proper-sku"></a>Trovare uno SKU corretto

Come è possibile valutare la capacità in ingresso/uscita o individuare il livello adatto a un caso di utilizzo specifico?

Si supponga che il server applicazioni è abbastanza potente e non è il collo di bottiglia delle prestazioni. Quindi, controllare la larghezza di banda massima in ingresso e in uscita per ogni livello.

#### <a name="quick-evaluation"></a>Valutazione rapida

Semplifichiamo la valutazione in modo che assuma alcune impostazioni predefinite: 

- Il tipo di trasporto è WebSocket.The transport type is WebSocket.
- La dimensione del messaggio è 2.048 byte.
- Ogni 1 secondo viene inviato un messaggio.
- Il servizio SignalR di Azure è in modalità predefinita.

Ogni livello ha la propria larghezza di banda massima in ingresso e la larghezza di banda in uscita. Un'esperienza utente fluida non è garantita dopo che la connessione in ingresso o in uscita supera il limite.

**Echo** fornisce la larghezza di banda massima in ingresso perché ha il costo di routing più basso. **Trasmissione** definisce la larghezza di banda massima dei messaggi in uscita.

*Non* superare i valori evidenziati nelle due tabelle seguenti.

|       Echo                        | Unità1 | Unità2 | Unità5 | Unità10 | Unità20 | Unità50 | Unità100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni                       | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| **Larghezza di banda in ingresso** | **2 MBps**    | **4 MBps**    | **10 MBps**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Larghezza di banda in uscita | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Broadcast             | Unità1 | Unità2 | Unità5  | Unità10 | Unità20 | Unità50  | Unità100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000 |
| Larghezza di banda in ingresso  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Larghezza di banda in uscita** | **4 MBps**    | **8 MBps**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

*La larghezza di banda in ingresso* e la larghezza di banda in *uscita* sono la dimensione totale dei messaggi al secondo.  Ecco le formule per loro:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: Il numero di connessioni che inviano il messaggio.

- *outboundConnections*: Il numero di connessioni che ricevono il messaggio.

- *messageSize*: La dimensione di un singolo messaggio (valore medio). Un messaggio di piccole dimensioni inferiore a 1.024 byte ha un impatto sulle prestazioni simile a un messaggio di 1.024 byte.

- *sendInterval*: L'ora di invio di un messaggio. In genere è 1 secondo per messaggio, il che significa l'invio di un messaggio ogni secondo. Un intervallo più piccolo significa inviare più messaggi in un periodo di tempo. Ad esempio, 0,5 secondi per messaggio significa inviare due messaggi al secondo.

- *Connessioni:* la soglia massima di cui è stato eseguito il commit per il servizio SignalR di Azure per ogni livello. Se il numero di connessione viene ulteriormente aumentato, subirà la limitazione delle connessioni.

#### <a name="evaluation-for-complex-use-cases"></a>Valutazione per casi d'uso complessi

##### <a name="bigger-message-size-or-different-sending-rate"></a>Dimensioni del messaggio più grandi o velocità di invio diverse

Il vero caso d'uso è più complicato. Potrebbe inviare un messaggio di dimensioni superiori a 2.048 byte oppure la frequenza dei messaggi di invio non è un messaggio al secondo. Prendiamo la trasmissione di Unit100 come esempio per trovare come valutare le sue prestazioni.

La tabella seguente mostra un caso d'uso reale di **trasmissione**. Ma la dimensione del messaggio, il numero di connessioni e la frequenza di invio dei messaggi sono diversi da quelli ipotizzati nella sezione precedente. La domanda è come possiamo dedurre uno qualsiasi di questi elementi (dimensione del messaggio, numero di connessioni o frequenza di invio dei messaggi) se ne conosciamo solo due.

| Broadcast  | Dimensioni dei messaggi | Messaggi in ingresso al secondo | Connessioni | Intervalli di invio |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB (da 20).                | 1                        | 100,000     | 5 secondi                      |
| 2 | 256 KB               | 1                        | 8.000       | 5 secondi                      |

La seguente formula è facile da dedurre in base alla formula precedente:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Per Unit100, la larghezza di banda massima in uscita è 400 MB dalla tabella precedente. Per una dimensione di messaggio di 20 KB, le \* connessioni in uscita massime devono essere 400 MB 5 / 20 KB - 100.000, che corrisponde al valore reale.

##### <a name="mixed-use-cases"></a>Casi d'uso misti

Il caso d'uso reale in genere combina i quattro casi d'uso di base insieme: **echo**, **broadcast**, send **to group**e send **to connection**. La metodologia utilizzata per valutare la capacità consiste nel:

1. Dividere i casi d'uso misto in quattro casi d'uso di base.
1. Calcolare la larghezza di banda massima dei messaggi in ingresso e in uscita utilizzando separatamente le formule precedenti.
1. Sommare i calcoli della larghezza di banda per ottenere la larghezza di banda massima totale in ingresso/in uscita. 

Riprendere quindi il livello corretto dalle tabelle di larghezza di banda massima in ingresso/in uscita.

> [!NOTE]
> Per l'invio di un messaggio a centinaia o migliaia di piccoli gruppi o per migliaia di client che inviano un messaggio l'uno all'altro, il costo di routing diventerà dominante. Prendere in considerazione questo impatto.

Per usare un messaggio ai client, assicurarsi che il server applicazioni *non* sia il collo di bottiglia. La sezione "Case study" seguente fornisce le linee guida sul numero di app server necessari e sul numero di connessioni server da configurare.

## <a name="case-study"></a>Case study

Nelle sezioni seguenti vengono sottoposti a quattro casi d'uso tipici per il trasporto WebSocket: **echo**, **broadcast**, send **to group**e send **to connection**. For each scenario, the section lists the current inbound and outbound capacity for Azure SignalR Service. Vengono inoltre illustrati i principali fattori che influiscono sulle prestazioni.

Nella modalità predefinita, il server applicazioni crea cinque connessioni server con il servizio SignalR di Azure.In the default mode, the app server creates five server connections with Azure SignalR Service. Il server applicazioni usa l'SDK del servizio SignalR di Azure per impostazione predefinita. Nei risultati dei test delle prestazioni seguenti, le connessioni al server vengono aumentate a 15 (o più per la trasmissione e l'invio di un messaggio a un gruppo di grandi dimensioni).

Casi d'uso diversi hanno requisiti diversi per i server applicazioni. **Broadcast** richiede un numero limitato di server applicazioni. **Echo** o **invia alla connessione** richiede molti server app.

In tutti i casi d'uso, la dimensione predefinita del messaggio è 2.048 byte e l'intervallo di invio dei messaggi è 1 secondo.

### <a name="default-mode"></a>Modalità predefinita

I client, i server app Web e il servizio SignalR di Azure sono coinvolti nella modalità predefinita. Ogni client è l'acronimo di una singola connessione.

#### <a name="echo"></a>Echo

Innanzitutto, un'app Web si connette al servizio SignalR di Azure.First, a web app connects to Azure SignalR Service. In secondo luogo, molti client si connettono all'app Web, che reindirizza i client al servizio SignalR di Azure con il token di accesso e l'endpoint. Quindi, i client stabiliscono connessioni WebSocket con il servizio SignalR di Azure.Then, the clients establish WebSocket connections with Azure SignalR Service.

Dopo che tutti i client stabiliscono le connessioni, iniziano a inviare un messaggio che contiene un timestamp all'hub specifico ogni secondo. L'hub fa eco al messaggio al client originale. Ogni client calcola la latenza quando riceve il messaggio echo.

Nel diagramma seguente, da 5 a 8 (traffico evidenziato in rosso) sono in un ciclo. Il ciclo viene eseguito per una durata predefinita (5 minuti) e ottiene la statistica di tutta la latenza dei messaggi.

![Traffico per il caso d'uso dell'eco](./media/signalr-concept-performance/echo.png)

Il comportamento di **echo** determina che la larghezza di banda massima in ingresso è uguale alla larghezza di banda massima in uscita. Per informazioni dettagliate, vedere la tabella seguente.

|       Echo                        | Unità1 | Unità2 | Unità5 | Unità10 | Unità20 | Unità50 | Unità100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni                       | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Messaggi in ingresso/in uscita al secondo | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Larghezza di banda in ingresso/in uscita | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

In questo caso d'uso, ogni client richiama l'hub definito nel server applicazioni. L'hub chiama solo il metodo definito nel lato client originale. Questo hub è l'hub più leggero per **echo.**

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Anche per questo semplice hub, la pressione sul traffico sul server applicazioni è prominente con l'aumento del carico dei messaggi in ingresso **echo.** Questa pressione del traffico richiede molti server applicazioni per livelli SKU di grandi dimensioni. Nella tabella seguente sono elencati i conteggi dei server applicazioni per ogni livello.


|    Echo          | Unità1 | Unità2 | Unità5 | Unità10 | Unità20 | Unità50 | Unità100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Conteggio server app | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Il numero di connessione client, la dimensione del messaggio, la velocità di invio dei messaggi, il livello SKU e la CPU/memoria del server applicazioni influiscono sulle prestazioni complessive di **echo**.

#### <a name="broadcast"></a>Broadcast

Per **la trasmissione**, quando l'app Web riceve il messaggio, viene trasmesso a tutti i client. Più client ci sono da trasmettere, più traffico di messaggi c'è a tutti i client. Vedere il diagramma seguente.

![Traffico per il caso d'uso della trasmissione](./media/signalr-concept-performance/broadcast.png)

Un piccolo numero di clienti sta trasmettendo. La larghezza di banda dei messaggi in ingresso è piccola, ma la larghezza di banda in uscita è enorme. La larghezza di banda dei messaggi in uscita aumenta con l'aumentare della connessione client o della velocità di trasmissione.

Nella tabella seguente sono riepilogate le connessioni client massime, il numero di messaggi in ingresso/in uscita e la larghezza di banda.

|     Broadcast             | Unità1 | Unità2 | Unità5  | Unità10 | Unità20 | Unità50  | Unità100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000 |
| Messaggi in ingresso al secondo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Messaggi in uscita al secondoOutbound messages per second | 2.000 | 4.000 | 10,000 | 20.000 | 40.000 | 100,000 | 200.000 |
| Larghezza di banda in ingresso  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Larghezza di banda in uscita | 4 MBps   | 8 MBps   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

I client di trasmissione che pubblicano messaggi non sono più di quattro. Hanno bisogno di un numero inferiore di server applicazioni rispetto **all'echo** perché la quantità di messaggi in ingresso è ridotta. Due server applicazioni sono sufficienti sia per il sLA che per le considerazioni sulle prestazioni. Ma è necessario aumentare le connessioni server predefinite per evitare squilibri, in particolare per Unit50 e Unit100.

|   Broadcast      | Unità1 | Unità2 | Unità5 | Unità10 | Unità20 | Unità50 | Unità100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Conteggio server app | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumentare le connessioni server predefinite da 5 a 40 in ogni server applicazioni per evitare possibili connessioni di server non bilanciate al servizio SignalR di Azure.Increase the default server connections from 5 to 40 on every app server to avoid possible unbalanced server connections to Azure SignalR Service.
>
> Il numero di connessione client, la dimensione del messaggio, la frequenza di invio dei messaggi e il livello SKU influiscono sulle prestazioni complessive per la **trasmissione**.

#### <a name="send-to-group"></a>Invia al gruppo

Il caso d'uso **di invio al gruppo** ha un modello di traffico simile alla **trasmissione**. La differenza è che dopo che i client stabiliscono connessioni WebSocket con il servizio SignalR di Azure, devono unirsi ai gruppi prima di poter inviare un messaggio a un gruppo specifico. Il diagramma seguente illustra il flusso del traffico.

![Traffico per il caso d'uso send-to-group](./media/signalr-concept-performance/sendtogroup.png)

Il numero di membri e gruppi del gruppo sono due fattori che influiscono sulle prestazioni. Per semplificare l'analisi, definiamo due tipi di gruppi:

- **Piccolo gruppo**: Ogni gruppo ha 10 connessioni. Il numero di gruppo è uguale a (numero massimo di connessioni) / 10.The group number is equal to (max connection count) / 10. Ad esempio, per Unit1, se sono presenti 1.000 conteggi di connessioni, abbiamo 1000 / 10 x 100 gruppi.

- **Grande gruppo**: Il numero di gruppo è sempre 10. Il numero di membri del gruppo è uguale a (numero massimo di connessioni) / 10.The group member count is equal to (max connection count) / 10. Ad esempio, se per Unit1, se sono presenti 1.000 conteggi di connessioni, ogni gruppo ha 1000 / 10 x 100 membri.

**Il gruppo Invia a** porta un costo di routing al servizio SignalR di Azure perché deve trovare le connessioni di destinazione tramite una struttura di dati distribuita. Con l'aumentare delle connessioni di invio, il costo aumenta.

##### <a name="small-group"></a>Piccolo gruppo

Il costo di routing è significativo per l'invio di messaggi a molti piccoli gruppi. Attualmente, l'implementazione del servizio SignalR di Azure raggiunge il limite di costo di routing in Unit50.Currently, the Azure SignalR Service implementation hits the routing cost limit at Unit50. L'aggiunta di più CPU e memoria non aiuta, quindi Unit100 non può migliorare ulteriormente in base alla progettazione. Se hai bisogno di più larghezza di banda in entrata, contatta l'assistenza clienti.

|   Invia a un piccolo gruppo     | Unità1 | Unità2 | Unità5  | Unità10 | Unità20 | Unità50 | Unità100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000 | 100,000
| Conteggio membri del gruppo        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Conteggio gruppo               | 100   | 200   | 500    | 1.000  | 2.000  | 5.000  | 10,000 
| Messaggi in ingresso al secondo  | 200   | 400   | 1.000  | 2.500  | 4.000  | 7.000  | 7.000   |
| Larghezza di banda in ingresso  | 400 KBps  | 800 KBps  | 2 MBps     | 5 MBps     | 8 MBps     | 14 MBps    | 14 MBps     |
| Messaggi in uscita al secondoOutbound messages per second | 2.000 | 4.000 | 10,000 | 25.000 | 40.000 | 70,000 | 70,000  |
| Larghezza di banda in uscita | 4 MBps    | 8 MBps    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Molte connessioni client chiamano l'hub, pertanto anche il numero del server applicazioni è fondamentale per le prestazioni. Nella tabella seguente sono elencati i conteggi dei server applicazioni suggeriti.

|  Invia a un piccolo gruppo   | Unità1 | Unità2 | Unità5 | Unità10 | Unità20 | Unità50 | Unità100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Conteggio server app | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Il numero di connessione client, la dimensione dei messaggi, la velocità di invio dei messaggi, il costo di routing, il livello SKU e la CPU/memoria del server applicazioni influiscono sulle prestazioni complessive di **invio a piccoli gruppi.**

##### <a name="big-group"></a>Grande gruppo

Per **l'invio a un gruppo di grandi dimensioni,** la larghezza di banda in uscita diventa il collo di bottiglia prima di raggiungere il limite dei costi di routing. Nella tabella seguente è elencata la larghezza di banda massima in uscita, che è quasi la stessa di quella per la **trasmissione**.

|    Invia a grande gruppo      | Unità1 | Unità2 | Unità5  | Unità10 | Unità20 | Unità50  | Unità100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000
| Conteggio membri del gruppo        | 100   | 200   | 500    | 1.000  | 2.000  | 5.000   | 10,000 
| Conteggio gruppo               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Messaggi in ingresso al secondo  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Larghezza di banda in ingresso  | 80 KBps   | 40 KBps   | 40 KBps    | 20 KBps    | 40 KBps    | 40 KBps     | 40 KBps     |
| Messaggi in uscita al secondoOutbound messages per second | 2.000 | 4.000 | 10,000 | 20.000 | 40.000 | 100,000 | 200.000 |
| Larghezza di banda in uscita | 8 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

Il numero di connessioni di invio non è superiore a 40. L'onere sul server applicazioni è ridotto, quindi il numero consigliato di applicazioni web è piccolo.

|  Invia a grande gruppo  | Unità1 | Unità2 | Unità5 | Unità10 | Unità20 | Unità50 | Unità100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Conteggio server app | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumentare le connessioni server predefinite da 5 a 40 in ogni server applicazioni per evitare possibili connessioni di server non bilanciate al servizio SignalR di Azure.Increase the default server connections from 5 to 40 on every app server to avoid possible unbalanced server connections to Azure SignalR Service.
> 
> Il numero di connessione client, la dimensione dei messaggi, la velocità di invio dei messaggi, il costo di routing e il livello SKU influiscono sulle prestazioni complessive di **invio al gruppo big.**

#### <a name="send-to-connection"></a>Invia a connessione

Nel caso di utilizzo della **connessione di invio,** quando i client stabiliscono le connessioni al servizio SignalR di Azure, ogni client chiama un hub speciale per ottenere il proprio ID di connessione. Il benchmark delle prestazioni raccoglie tutti gli ID di connessione, li rimescola e li riassegna a tutti i client come destinazione di invio. I client continuano a inviare il messaggio alla connessione di destinazione fino al completamento del test delle prestazioni.

![Traffico per il caso d'uso di invio a client](./media/signalr-concept-performance/sendtoclient.png)

Il costo di routing per **l'invio alla connessione** è simile al costo per l'invio **a un piccolo gruppo.**

Con l'aumentare del numero di connessioni, il costo di routing limita le prestazioni complessive. Unit50 ha raggiunto il limite. Di conseguenza, Unit100 non può migliorare ulteriormente.

La tabella seguente è un riepilogo statistico dopo molti cicli di esecuzione del benchmark di **invio alla connessione.**

|   Invia a connessione   | Unità1 | Unità2 | Unità5 | Unità10 | Unità20 | Unità50          | Unità100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Connessioni                        | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000          | 100,000         |
| Messaggi in ingresso/in uscita al secondo | 1.000 | 2.000 | 5.000 | 8.000  | 9000  | 20.000 | 20.000 |
| Larghezza di banda in ingresso/in uscita | 2 MBps    | 4 MBps    | 10 MBps   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Questo caso d'uso richiede un carico elevato sul lato server dell'app. Vedere il numero di server applicazioni suggerito nella tabella seguente.

|  Invia a connessione  | Unità1 | Unità2 | Unità5 | Unità10 | Unità20 | Unità50 | Unità100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Conteggio server app | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Il numero di connessione client, la dimensione dei messaggi, la velocità di invio dei messaggi, il costo di routing, il livello SKU e la CPU/memoria per il server applicazioni influiscono sulle prestazioni complessive di **invio alla connessione.**

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR eco, trasmissione, e l'invio a piccoli gruppi

Azure SignalR Service provides the same performance capacity for ASP.NET SignalR. 

Il test delle prestazioni usa App Web di Azure dal piano di [servizio standard S3](https://azure.microsoft.com/pricing/details/app-service/windows/) per ASP.NET SignalR.

Nella tabella seguente viene visualizzato il numero di app Web suggerito per ASP.NET **SignalR echo**.

|   Echo           | Unità1 | Unità2 | Unità5 | Unità10 | Unità20 | Unità50 | Unità100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Conteggio server app | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

Nella tabella seguente viene visualizzato il numero di app Web suggerito per ASP.NET **trasmissione**SignalR .

|  Broadcast       | Unità1 | Unità2 | Unità5 | Unità10 | Unità20 | Unità50 | Unità100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Conteggio server app | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

Nella tabella seguente viene visualizzato il numero di app Web consigliate per ASP.NET invio SignalR **a un piccolo gruppo**.

|  Invia a un piccolo gruppo     | Unità1 | Unità2 | Unità5 | Unità10 | Unità20 | Unità50 | Unità100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Conteggio server app | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Modalità senza server

I client e il servizio SignalR di Azure sono coinvolti in modalità senza server. Ogni client è l'acronimo di una singola connessione. Il client invia messaggi tramite l'API REST a un altro client o trasmette messaggi a tutti.

L'invio di messaggi ad alta densità tramite l'API REST non è efficiente come l'utilizzo di WebSocket.Sending high-density messages through the REST API is not efficient as using WebSocket. Richiede di creare una nuova connessione HTTP ogni volta, e questo è un costo aggiuntivo in modalità serverless.

#### <a name="broadcast-through-rest-api"></a>Trasmissione tramite API RESTBroadcast through REST API
Tutti i client stabiliscono connessioni WebSocket con il servizio SignalR di Azure.All clients establish WebSocket connections with Azure SignalR Service. Poi alcuni client iniziano a trasmettere tramite l'API REST. L'invio di messaggi (in ingresso) è tutto tramite HTTP Post, che non è efficiente rispetto a WebSocket.

|   Trasmissione tramite API RESTBroadcast through REST API     | Unità1 | Unità2 | Unità5  | Unità10 | Unità20 | Unità50  | Unità100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000 |
| Messaggi in ingresso al secondo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Messaggi in uscita al secondoOutbound messages per second | 2.000 | 4.000 | 10,000 | 20.000 | 40.000 | 100,000 | 200.000 |
| Larghezza di banda in ingresso  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Larghezza di banda in uscita | 4 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Invia all'utente tramite l'API RESTSend to user through REST API
The benchmark assigns usernames to all of the clients before they start connecting to Azure SignalR Service. Dopo che i client stabiliscono connessioni WebSocket con il servizio SignalR di Azure, iniziano a inviare messaggi ad altri utenti tramite HTTP Post.After the clients establish WebSocket connections with Azure SignalR Service, they start sending messages to others through HTTP Post.

|   Invia all'utente tramite l'API RESTSend to user through REST API | Unità1 | Unità2 | Unità5  | Unità10 | Unità20 | Unità50  | Unità100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000 |
| Messaggi in ingresso al secondo  | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18.000  |
| Messaggi in uscita al secondoOutbound messages per second | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18.000 |
| Larghezza di banda in ingresso  | 600 KBps  | 1.2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |
| Larghezza di banda in uscita | 600 KBps  | 1.2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |

## <a name="performance-test-environments"></a>Ambienti di test delle prestazioniPerformance test environments

Per tutti i casi d'uso elencati in precedenza, sono stati eseguiti i test delle prestazioni in un ambiente Azure.For all use cases listed earlier, we ded the performance tests in an Azure environment. Al massimo, abbiamo usato 50 macchine virtuali client e 20 macchine virtuali del server applicazioni. Ecco alcune informazioni:

- Dimensioni della macchina virtuale client: StandardDS2V2 (2 vCPU, 7G di memoria)

- Dimensioni della macchina virtuale del server app: StandardF4sV2 (4 vCPU, 8G di memoria)

- Connessioni al server Azure SignalR SDK: 15Azure SignalR SDK server connections: 15

## <a name="performance-tools"></a>Strumenti per le prestazioni

Gli strumenti di prestazioni per il servizio SignalR di Azure sono disponibili in [GitHub.You](https://github.com/Azure/azure-signalr-bench/)can find performance tools for Azure SignalR Service on GitHub .

## <a name="next-steps"></a>Passaggi successivi

In questo articolo viene fornita una panoramica delle prestazioni del servizio SignalR di Azure in scenari tipici dei casi d'uso.

Per ottenere informazioni dettagliate sulle funzionalità interne del servizio e sul ridimensionamento, leggere le guide seguenti:

* [Elementi interni del Servizio Azure SignalR](signalr-concept-internals.md)
* [Scalabilità del servizio SignalR di AzureAzure SignalR Service scaling](signalr-howto-scale-multi-instances.md)
