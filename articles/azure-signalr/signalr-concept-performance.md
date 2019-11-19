---
title: Guida alle prestazioni per il servizio Azure SignalR
description: Panoramica delle prestazioni e del benchmark del servizio Azure SignalR. Metriche chiave da considerare durante la pianificazione della capacità.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157662"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guida alle prestazioni per il servizio Azure SignalR

Uno dei principali vantaggi dell'uso del servizio Azure SignalR è la facilità di ridimensionamento delle applicazioni SignalR. In uno scenario su larga scala, le prestazioni sono un fattore importante. 

In questa guida verranno introdotti i fattori che influiscono sulle prestazioni dell'applicazione SignalR. Verranno descritte le prestazioni tipiche in scenari con diversi casi d'uso. Alla fine, verranno introdotti l'ambiente e gli strumenti che è possibile usare per generare un report di prestazioni.

## <a name="term-definitions"></a>Definizioni dei termini

In *ingresso*: messaggio in ingresso per il servizio Azure SignalR.

In *uscita*: messaggio in uscita dal servizio Azure SignalR.

*Larghezza di banda*: dimensioni totali di tutti i messaggi in un secondo.

*Modalità predefinita*: modalità di funzionamento predefinita quando è stata creata un'istanza del servizio Azure SignalR. Il servizio Azure SignalR prevede che il server app stabilisca una connessione prima di accettare le connessioni client.

*Modalità senza server*: una modalità in cui il servizio Azure SignalR accetta solo le connessioni client. Non è consentita alcuna connessione al server.

## <a name="overview"></a>Overview

Il servizio Azure SignalR definisce sette livelli standard per capacità di prestazioni diverse. Questa guida risponde alle domande seguenti:

-   Quali sono le prestazioni tipiche del servizio SignalR di Azure per ogni livello?

-   Il servizio Azure SignalR soddisfa i requisiti per la velocità effettiva dei messaggi, ad esempio l'invio di 100.000 messaggi al secondo?

-   Per lo scenario specifico, qual è il livello adatto per me? O come è possibile selezionare il livello appropriato?

-   Quale tipo di server app (dimensione VM) è adatto per me? Quanti di essi è necessario distribuire?

Per rispondere a queste domande, in questa guida viene fornita una spiegazione di alto livello dei fattori che influiscono sulle prestazioni. Vengono quindi illustrati i messaggi in ingresso e in uscita massimi per ogni livello per i casi d'uso tipici: **echo**, **broadcast**, **Send to Group**e **Send to connection** (chat peer-to-peer).

Questa guida non può coprire tutti gli scenari (e diversi casi d'uso, dimensioni dei messaggi, modelli di invio dei messaggi e così via). Fornisce tuttavia alcuni metodi che consentono di:

- Valutare il requisito approssimativo per i messaggi in ingresso o in uscita.
- Individuare i livelli appropriati controllando la tabella delle prestazioni.

## <a name="performance-insight"></a>Informazioni dettagliate sulle prestazioni

In questa sezione vengono descritte le metodologie di valutazione delle prestazioni, quindi vengono elencati tutti i fattori che influiscono sulle prestazioni. Alla fine, fornisce metodi che consentono di valutare i requisiti di prestazioni.

### <a name="methodology"></a>Metodologia

La *velocità effettiva* e la *latenza* sono due aspetti tipici del controllo delle prestazioni. Per il servizio Azure SignalR, a ogni livello SKU sono configurati criteri di limitazione della velocità effettiva. Il criterio definisce *la velocità effettiva massima consentita (larghezza di banda in ingresso e in uscita)* come massima velocità effettiva ottenibile quando il 99% dei messaggi presenta una latenza inferiore a 1 secondo.

La latenza è l'intervallo di tempo dalla connessione che invia il messaggio alla ricezione del messaggio di risposta dal servizio Azure SignalR. Prendiamo **echo** come esempio. Ogni connessione client aggiunge un timestamp nel messaggio. L'hub del server app invia nuovamente il messaggio originale al client. Pertanto, il ritardo di propagazione viene calcolato facilmente da ogni connessione client. Il timestamp è allegato per ogni messaggio in **broadcast**, **invio al gruppo**e **INVIO alla connessione**.

Per simulare migliaia di connessioni client simultanee, vengono create più macchine virtuali in una rete privata virtuale in Azure. Tutte queste VM si connettono alla stessa istanza del servizio Azure SignalR.

Nella modalità predefinita del servizio Azure SignalR le VM del server app vengono distribuite nella stessa rete privata virtuale delle macchine virtuali client. Tutte le VM client e le macchine virtuali del server app vengono distribuite nella stessa rete della stessa area per evitare la latenza tra aree.

### <a name="performance-factors"></a>Fattori relativi alle prestazioni

In teoria, la capacità del servizio Azure SignalR è limitata dalle risorse di calcolo: CPU, memoria e rete. Ad esempio, un numero maggiore di connessioni al servizio Azure SignalR comporta l'utilizzo di più memoria da parte del servizio. Per il traffico di messaggi di dimensioni maggiori (ad esempio, ogni messaggio è più grande di 2.048 byte), il servizio Azure SignalR deve spendere più cicli di CPU per elaborare il traffico. Nel frattempo, la larghezza di banda di rete di Azure impone anche un limite per il traffico massimo.

Il tipo di trasporto è un altro fattore che influiscono sulle prestazioni. I tre tipi sono [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [server inviato-evento](https://en.wikipedia.org/wiki/Server-sent_events)e [polling prolungato](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket è un protocollo di comunicazione bidirezionale e full-duplex su una singola connessione TCP. Server-Sent-Event è un protocollo unidirezionale per eseguire il push dei messaggi dal server al client. Il polling prolungato richiede che i client eseguano periodicamente il polling delle informazioni dal server tramite una richiesta HTTP. Per la stessa API nelle stesse condizioni, WebSocket offre le migliori prestazioni, il server inviato-evento è più lento e il polling prolungato è il più lento. Per impostazione predefinita, il servizio Azure SignalR consiglia WebSocket.

Il costo di routing del messaggio limita anche le prestazioni. Il servizio Azure SignalR svolge un ruolo di router di messaggi, che instrada il messaggio da un set di client o server ad altri client o server. Uno scenario o un'API diversa richiede un criterio di routing diverso. 

Per **echo**, il client invia un messaggio a se stesso e anche la destinazione di routing è la stessa. Questo modello ha il costo di routing più basso. Tuttavia, per la **trasmissione**, l' **invio al gruppo**e l' **INVIO alla connessione**, il servizio Azure SignalR deve cercare le connessioni di destinazione tramite la struttura dei dati distribuiti interna. Questa elaborazione aggiuntiva usa più CPU, memoria e larghezza di banda di rete. Di conseguenza, le prestazioni sono più lente.

Nella modalità predefinita, anche il server applicazioni potrebbe diventare un collo di bottiglia per determinati scenari. Azure SignalR SDK deve richiamare l'hub, mentre mantiene una connessione dinamica a ogni client tramite segnali di heartbeat.

In modalità senza server, il client invia un messaggio tramite HTTP post, che non è efficiente come WebSocket.

Un altro fattore è il protocollo: JSON e [MessagePack](https://msgpack.org/index.html). Le dimensioni di MessagePack sono inferiori e vengono recapitate più velocemente di JSON. Tuttavia, MessagePack potrebbe non migliorare le prestazioni. Le prestazioni del servizio Azure SignalR non sono sensibili ai protocolli perché non decodificano il payload del messaggio durante l'invio dei messaggi da client a server o viceversa.

In sintesi, i fattori seguenti influiscono sulla capacità in ingresso e in uscita:

-   Livello SKU (CPU/memoria)

-   Numero di connessioni

-   Dimensioni dei messaggi

-   Velocità di invio messaggi

-   Tipo di trasporto (WebSocket, server-sent-Event o con polling prolungato)

-   Scenario dei casi d'uso (costo di routing)

-   Server App e connessioni al servizio (in modalità server)


### <a name="finding-a-proper-sku"></a>Ricerca di uno SKU corretto

Come è possibile valutare la capacità in ingresso/in uscita o individuare il livello adatto a un caso d'uso specifico?

Si supponga che il server dell'app sia sufficientemente potente e non sia il collo di bottiglia delle prestazioni. Quindi, controllare la larghezza di banda massima in ingresso e in uscita per ogni livello.

#### <a name="quick-evaluation"></a>Valutazione rapida

Si semplifica prima di tutto la valutazione presumendo alcune impostazioni predefinite: 

- Il tipo di trasporto è WebSocket.
- Le dimensioni del messaggio sono pari a 2.048 byte.
- Un messaggio viene inviato ogni secondo.
- Il servizio Azure SignalR è in modalità predefinita.

Ogni livello ha una larghezza di banda massima in ingresso e in uscita. Una semplice esperienza utente non è garantita dopo che la connessione in ingresso o in uscita supera il limite.

**Echo** fornisce la larghezza di banda massima in ingresso perché ha il costo di routing più basso. **Broadcast** definisce la larghezza di banda massima dei messaggi in uscita.

*Non* superare i valori evidenziati nelle due tabelle seguenti.

|       Echo                        | Commutazione1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni                       | 1\.000 | 2\.000 | 5\.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| **Larghezza di banda in ingresso** | **2 MBps**    | **4 MBps**    | **10 MBps**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Larghezza di banda in uscita | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Trasmissione             | Commutazione1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Connessioni               | 1\.000 | 2\.000 | 5\.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| Larghezza di banda in ingresso  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Larghezza di banda in uscita** | **4 MBps**    | **8 MBps**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

La larghezza di banda in *ingresso* e la *larghezza di banda* in uscita sono le dimensioni totali del messaggio al secondo.  Ecco le formule seguenti:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: numero di connessioni che inviano il messaggio.

- *outboundConnections*: numero di connessioni che ricevono il messaggio.

- *messageSize*: dimensione di un singolo messaggio (valore medio). Un messaggio di piccole dimensioni inferiore a 1.024 byte ha un effetto sulle prestazioni simile a quello di un messaggio di 1.024 byte.

- *sendInterval*: tempo di invio di un messaggio. Si tratta in genere di un secondo per messaggio, ovvero l'invio di un messaggio ogni secondo. Un intervallo più piccolo significa inviare più messaggi in un periodo di tempo. Ad esempio, 0,5 secondi per messaggio significa inviare due messaggi ogni secondo.

- *Connessioni*: la soglia massima di cui è stato eseguito il commit per il servizio Azure SignalR per ogni livello. Se il numero di connessione viene aumentato ulteriormente, la limitazione della connessione risentirà.

#### <a name="evaluation-for-complex-use-cases"></a>Valutazione per casi di utilizzo complessi

##### <a name="bigger-message-size-or-different-sending-rate"></a>Dimensioni del messaggio maggiori o velocità di invio diversa

Il caso d'uso reale è più complesso. È possibile che invii un messaggio di dimensioni superiori a 2.048 byte oppure che la frequenza dei messaggi di invio non sia un messaggio al secondo. Unit100's broadcast come esempio per scoprire come valutarne le prestazioni.

Nella tabella seguente viene illustrato un caso d'uso reale della **trasmissione**. Tuttavia, le dimensioni del messaggio, il numero di connessioni e la velocità di invio dei messaggi sono diverse da quelle assunte nella sezione precedente. La domanda è il modo in cui è possibile dedurre qualsiasi elemento (dimensione del messaggio, numero di connessioni o frequenza di invio dei messaggi) se ne sono note solo due.

| Trasmissione  | Dimensioni dei messaggi | Messaggi in ingresso al secondo | Connessioni | Intervalli di invio |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100.000     | 5 secondi                      |
| 2 | 256 KB               | 1                        | 8\.000       | 5 secondi                      |

La formula seguente è facile da dedurre in base alla formula precedente:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Per Unit100, la larghezza di banda massima in uscita è 400 MB rispetto alla tabella precedente. Per le dimensioni del messaggio da 20 KB, il numero massimo di connessioni in uscita deve essere 400 MB \* 5/20 KB = 100.000, che corrisponde al valore reale.

##### <a name="mixed-use-cases"></a>Casi di utilizzo misti

Il caso d'uso reale combina in genere i quattro casi d'uso di base: **echo**, **broadcast**, **Send to Group**e **Send to connection**. La metodologia utilizzata per valutare la capacità è:

1. Dividere i casi di utilizzo misti in quattro casi d'uso di base.
1. Calcolare la larghezza di banda massima dei messaggi in ingresso e in uscita usando le formule precedenti separatamente.
1. Sommare i calcoli della larghezza di banda per ottenere la larghezza di banda totale massima in ingresso/uscita. 

Quindi, selezionare il livello appropriato dalle tabelle della larghezza di banda massima in ingresso/in uscita.

> [!NOTE]
> Per inviare un messaggio a centinaia o migliaia di piccoli gruppi o per migliaia di client che inviano un messaggio tra loro, il costo di routing diventerà dominante. Prendere in considerazione questo effetto.

Per il caso d'uso dell'invio di un messaggio ai client, assicurarsi che il server dell'app *non* sia il collo di bottiglia. La sezione "Case Study" seguente fornisce linee guida sul numero di server applicazioni necessari e sul numero di connessioni server da configurare.

## <a name="case-study"></a>Case study

Nelle sezioni seguenti vengono illustrati quattro casi d'uso tipici per il trasporto WebSocket: **echo**, **broadcast**, **Send to Group**e **Send to connection**. Per ogni scenario, la sezione elenca la capacità in ingresso e in uscita corrente per il servizio Azure SignalR. Vengono inoltre illustrati i principali fattori che influiscono sulle prestazioni.

Nella modalità predefinita, il server app crea cinque connessioni server con il servizio Azure SignalR. Per impostazione predefinita, il server applicazioni usa Azure SignalR Service SDK. Nei risultati dei test delle prestazioni seguenti le connessioni server vengono aumentate a 15 (o più per la trasmissione e l'invio di un messaggio a un gruppo grande).

Diversi casi d'uso hanno requisiti diversi per i server app. **Broadcast** richiede un numero ridotto di server app. **Echo** o **Send to connection necessita di** molti server app.

In tutti i casi d'uso, le dimensioni predefinite del messaggio sono pari a 2.048 byte e l'intervallo di invio del messaggio è di 1 secondo.

### <a name="default-mode"></a>Modalità predefinita

I client, i server dell'app Web e il servizio Azure SignalR sono interessati dalla modalità predefinita. Ogni client sta per una singola connessione.

#### <a name="echo"></a>Echo

Prima di tutto, un'app Web si connette al servizio Azure SignalR. In secondo luogo, molti client si connettono all'app Web, che reindirizza i client al servizio Azure SignalR con il token di accesso e l'endpoint. I client stabiliscono quindi le connessioni WebSocket con il servizio Azure SignalR.

Dopo che tutti i client hanno stabilito le connessioni, iniziano a inviare un messaggio contenente un timestamp all'hub specifico ogni secondo. L'hub restituisce il messaggio al client originale. Ogni client calcola la latenza quando riceve il messaggio echo.

Nel diagramma seguente, da 5 a 8 (traffico evidenziato in rosso) si trovano in un ciclo. Il ciclo viene eseguito per una durata predefinita (5 minuti) e ottiene la statistica della latenza di tutti i messaggi.

![Traffico per il caso d'uso Echo](./media/signalr-concept-performance/echo.png)

Il comportamento di **echo** determina che la larghezza di banda in ingresso massima è uguale alla larghezza di banda massima in uscita. Per informazioni dettagliate, vedere la tabella seguente.

|       Echo                        | Commutazione1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni                       | 1\.000 | 2\.000 | 5\.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Messaggi in ingresso/in uscita al secondo | 1\.000 | 2\.000 | 5\.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Larghezza di banda in ingresso/in uscita | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

In questo caso di utilizzo, ogni client richiama l'hub definito nel server applicazioni. L'hub chiama semplicemente il metodo definito nel lato client originale. Questo hub è l'hub più leggero per **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Anche per questo hub semplice, la pressione del traffico sul server app è evidente quando aumenta il carico del messaggio in ingresso **echo** . Questa pressione del traffico richiede molti server app per i livelli SKU di grandi dimensioni. La tabella seguente elenca il numero di server app per ogni livello.


|    Echo          | Commutazione1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1\.000 | 2\.000 | 5\.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Numero di server applicazioni | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Il numero di connessione del client, la dimensione del messaggio, la velocità di invio dei messaggi, il livello SKU e la CPU/memoria del server applicazioni influiscono sulle prestazioni complessive di **echo**.

#### <a name="broadcast"></a>Trasmissione

Per la **trasmissione**, quando l'app Web riceve il messaggio, trasmette a tutti i client. Maggiore è il numero di client da trasmettere, maggiore sarà il traffico dei messaggi per tutti i client. Vedere il diagramma seguente.

![Traffico per il caso d'uso broadcast](./media/signalr-concept-performance/broadcast.png)

Un numero ridotto di client trasmette. La larghezza di banda dei messaggi in ingresso è ridotta, ma la larghezza di banda in uscita è enorme. La larghezza di banda dei messaggi in uscita aumenta con l'aumento della velocità di connessione o trasmissione del client.

Nella tabella seguente vengono riepilogate le connessioni client massime, il numero di messaggi in ingresso/in uscita e la larghezza di banda.

|     Trasmissione             | Commutazione1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Connessioni               | 1\.000 | 2\.000 | 5\.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| Messaggi in ingresso al secondo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Messaggi in uscita al secondo | 2\.000 | 4\.000 | 10.000 | 20.000 | 40.000 | 100.000 | 200.000 |
| Larghezza di banda in ingresso  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Larghezza di banda in uscita | 4 MBps   | 8 MBps   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

I client broadcast che inviano messaggi non sono più di quattro. Sono necessari meno server app rispetto a **echo** perché la quantità di messaggi in ingresso è ridotta. Due server app sono sufficienti per considerazioni su contratti di contratto e prestazioni. Tuttavia, è necessario aumentare le connessioni server predefinite per evitare squilibri, soprattutto per Unit50 e Unit100.

|   Trasmissione      | Commutazione1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1\.000 | 2\.000 | 5\.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Numero di server applicazioni | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumentare le connessioni server predefinite da 5 a 40 in ogni server app per evitare possibili connessioni server non bilanciate al servizio Azure SignalR.
>
> Il numero di connessione client, le dimensioni del messaggio, la velocità di invio dei messaggi e il livello SKU influiscono sulle prestazioni complessive per la **trasmissione**.

#### <a name="send-to-group"></a>Invia al gruppo

Il caso d'uso **Invia al gruppo** ha un modello di traffico simile per la **trasmissione**. La differenza è che, dopo che i client stabiliscono le connessioni WebSocket con il servizio Azure SignalR, devono partecipare ai gruppi prima di poter inviare un messaggio a un gruppo specifico. Il diagramma seguente illustra il flusso del traffico.

![Traffico per il caso d'uso di invio al gruppo](./media/signalr-concept-performance/sendtogroup.png)

I membri del gruppo e il conteggio gruppo sono due fattori che influiscono sulle prestazioni. Per semplificare l'analisi, vengono definiti due tipi di gruppi:

- **Small Group**: ogni gruppo dispone di 10 connessioni. Il numero di gruppo è uguale a (numero massimo di connessioni)/10. Ad esempio, per Commutazione1, se ci sono 1.000 conteggi di connessione, sono disponibili 1000/10 = 100 gruppi.

- **Big Group**: il numero di gruppo è sempre 10. Il numero di membri del gruppo è uguale a (numero massimo di connessioni)/10. Per Commutazione1, ad esempio, se sono presenti 1.000 conteggi delle connessioni, ogni gruppo ha 1000/10 = 100 membri.

**Invia al gruppo** comporta un costo di routing per il servizio Azure SignalR perché deve trovare le connessioni di destinazione tramite una struttura di dati distribuita. Man mano che le connessioni di invio aumentano, il costo aumenta.

##### <a name="small-group"></a>Gruppo piccolo

Il costo di routing è significativo per l'invio di messaggi a molti gruppi di piccole dimensioni. Attualmente, l'implementazione del servizio Azure SignalR raggiunge il limite di costo del routing in Unit50. L'aggiunta di più CPU e memoria non è utile, quindi Unit100 non può migliorare ulteriormente in base alla progettazione. Se è necessaria una maggiore larghezza di banda in ingresso, contattare il supporto tecnico.

|   Invia a un piccolo gruppo     | Commutazione1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Connessioni               | 1\.000 | 2\.000 | 5\.000  | 10.000 | 20.000 | 50.000 | 100.000
| Conteggio membri gruppo        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Numero di gruppi               | 100   | 200   | 500    | 1\.000  | 2\.000  | 5\.000  | 10.000 
| Messaggi in ingresso al secondo  | 200   | 400   | 1\.000  | 2\.500  | 4\.000  | 7\.000  | 7\.000   |
| Larghezza di banda in ingresso  | 400 KBps  | 800 KBps  | 2 MBps     | 5 MBps     | 8 MBps     | 14 MBps    | 14 MBps     |
| Messaggi in uscita al secondo | 2\.000 | 4\.000 | 10.000 | 25.000 | 40.000 | 70.000 | 70.000  |
| Larghezza di banda in uscita | 4 MBps    | 8 MBps    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Molte connessioni client chiamano l'hub, quindi anche il numero del server applicazioni è critico per le prestazioni. La tabella seguente elenca i conteggi dei server app suggeriti.

|  Invia a un piccolo gruppo   | Commutazione1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1\.000 | 2\.000 | 5\.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Numero di server applicazioni | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Il numero di connessione del client, la dimensione del messaggio, la velocità di invio dei messaggi, il costo di routing, il livello di SKU e la CPU/memoria del server applicazioni influiscono sulle prestazioni complessive del **gruppo Invia a small**.

##### <a name="big-group"></a>Gruppo grande

Per l' **invio al gruppo di grandi dimensioni**, la larghezza di banda in uscita diventa il collo di bottiglia prima di raggiungere il limite di costo di routing. La tabella seguente elenca la larghezza di banda massima in uscita, che è quasi identica a quella per la **trasmissione**.

|    Invia a Big Group      | Commutazione1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Connessioni               | 1\.000 | 2\.000 | 5\.000  | 10.000 | 20.000 | 50.000  | 100.000
| Conteggio membri gruppo        | 100   | 200   | 500    | 1\.000  | 2\.000  | 5\.000   | 10.000 
| Numero di gruppi               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Messaggi in ingresso al secondo  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Larghezza di banda in ingresso  | 80 KBps   | 40 KBps   | 40 KBps    | 20 KBps    | 40 KBps    | 40 KBps     | 40 KBps     |
| Messaggi in uscita al secondo | 2\.000 | 4\.000 | 10.000 | 20.000 | 40.000 | 100.000 | 200.000 |
| Larghezza di banda in uscita | 8 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

Il numero di connessioni di invio non è superiore a 40. Il carico del server app è ridotto, quindi il numero suggerito di app Web è ridotto.

|  Invia a Big Group  | Commutazione1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1\.000 | 2\.000 | 5\.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Numero di server applicazioni | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumentare le connessioni server predefinite da 5 a 40 in ogni server app per evitare possibili connessioni server non bilanciate al servizio Azure SignalR.
> 
> Il numero di connessione del client, la dimensione del messaggio, la velocità di invio dei messaggi, il costo di routing e il livello SKU influiscono sulle prestazioni complessive di **invio al gruppo Big**.

#### <a name="send-to-connection"></a>Invia a connessione

Nel caso di utilizzo **di Invia alla connessione** , quando i client stabiliscono le connessioni al servizio Azure SignalR, ogni client chiama un hub speciale per ottenere il proprio ID connessione. Il benchmark delle prestazioni raccoglie tutti gli ID connessione, li mischia e li riassegna a tutti i client come destinazione di invio. I client continuano a inviare il messaggio alla connessione di destinazione fino al termine del test delle prestazioni.

![Traffico per il caso d'uso da inviare a client](./media/signalr-concept-performance/sendtoclient.png)

Il costo di routing per l' **INVIO alla connessione** è simile al costo per l' **invio a un piccolo gruppo**.

Man mano che aumenta il numero di connessioni, il costo del routing limita le prestazioni complessive. Unit50 ha raggiunto il limite. Di conseguenza, Unit100 non può migliorare ulteriormente.

La tabella seguente è un riepilogo statistico dopo molti cicli di esecuzione del benchmark **Send to connection** .

|   Invia a connessione   | Commutazione1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Connessioni                        | 1\.000 | 2\.000 | 5\.000 | 10.000 | 20.000 | 50.000          | 100.000         |
| Messaggi in ingresso/in uscita al secondo | 1\.000 | 2\.000 | 5\.000 | 8\.000  | 9000  | 20.000 | 20.000 |
| Larghezza di banda in ingresso/in uscita | 2 MBps    | 4 MBps    | 10 MBps   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Questo caso d'uso richiede un carico elevato sul lato server dell'app. Vedere il numero consigliato di server applicazioni nella tabella seguente.

|  Invia a connessione  | Commutazione1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1\.000 | 2\.000 | 5\.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Numero di server applicazioni | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Il numero di connessione del client, la dimensione del messaggio, la velocità di invio dei messaggi, il costo di routing, il livello SKU e la CPU/memoria per il server applicazioni influiscono sulle prestazioni complessive della **trasmissione alla connessione**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>Echo, broadcast e Send di ASP.NET SignalR a Small Group

Il servizio Azure SignalR offre la stessa capacità di prestazioni per ASP.NET SignalR. 

Il test delle prestazioni USA app Web di Azure dal [piano di servizio standard S3](https://azure.microsoft.com/pricing/details/app-service/windows/) per ASP.NET SignalR.

La tabella seguente fornisce il numero di app Web suggerite per ASP.NET SignalR **echo**.

|   Echo           | Commutazione1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1\.000 | 2\.000 | 5\.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Numero di server applicazioni | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

La tabella seguente indica il numero di app Web suggerite per la **trasmissione**di ASP.NET SignalR.

|  Trasmissione       | Commutazione1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1\.000 | 2\.000 | 5\.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Numero di server applicazioni | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

La tabella seguente indica il numero di app Web suggerite per l' **invio di**ASP.NET SignalR a un piccolo gruppo.

|  Invia a un piccolo gruppo     | Commutazione1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Connessioni      | 1\.000 | 2\.000 | 5\.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Numero di server applicazioni | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Modalità senza server

I client e il servizio Azure SignalR sono interessati dalla modalità senza server. Ogni client sta per una singola connessione. Il client invia messaggi tramite l'API REST a un altro client o trasmette messaggi a tutti.

L'invio di messaggi ad alta densità tramite l'API REST non è efficiente quanto l'uso di WebSocket. È necessario creare una nuova connessione HTTP ogni volta ed è un costo aggiuntivo in modalità senza server.

#### <a name="broadcast-through-rest-api"></a>Trasmissione tramite l'API REST
Tutti i client stabiliscono connessioni WebSocket con il servizio Azure SignalR. Alcuni client iniziano quindi a trasmettere tramite l'API REST. Il messaggio inviato (in ingresso) è tutto tramite HTTP post, che non è efficiente rispetto a WebSocket.

|   Trasmissione tramite l'API REST     | Commutazione1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Connessioni               | 1\.000 | 2\.000 | 5\.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| Messaggi in ingresso al secondo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Messaggi in uscita al secondo | 2\.000 | 4\.000 | 10.000 | 20.000 | 40.000 | 100.000 | 200.000 |
| Larghezza di banda in ingresso  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Larghezza di banda in uscita | 4 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Inviare all'utente tramite l'API REST
Il benchmark assegna i nomi utente a tutti i client prima di iniziare la connessione al servizio Azure SignalR. Dopo che i client hanno stabilito le connessioni WebSocket con il servizio Azure SignalR, iniziano a inviare messaggi ad altri tramite HTTP post.

|   Inviare all'utente tramite l'API REST | Commutazione1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Connessioni               | 1\.000 | 2\.000 | 5\.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| Messaggi in ingresso al secondo  | 300   | 600   | 900    | 1\.300  | 2\.000  | 10.000  | 18.000  |
| Messaggi in uscita al secondo | 300   | 600   | 900    | 1\.300  | 2\.000  | 10.000  | 18.000 |
| Larghezza di banda in ingresso  | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |
| Larghezza di banda in uscita | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |

## <a name="performance-test-environments"></a>Ambienti di test delle prestazioni

Per tutti i casi d'uso elencati in precedenza, sono stati eseguiti i test delle prestazioni in un ambiente Azure. Sono state usate al massimo 50 VM client e 20 VM del server app. Ecco alcuni dettagli:

- Dimensioni macchina virtuale client: StandardDS2V2 (2 vCPU, 7G memoria)

- Dimensioni VM del server app: StandardF4sV2 (4 vCPU, 8G memoria)

- Connessioni del server Azure SignalR SDK: 15

## <a name="performance-tools"></a>Strumenti per le prestazioni

È possibile trovare gli strumenti per le prestazioni per il servizio Azure SignalR su [GitHub](https://github.com/Azure/azure-signalr-bench/).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata illustrata una panoramica delle prestazioni del servizio SignalR di Azure negli scenari con casi d'uso tipici.

Per ottenere informazioni dettagliate sugli elementi interni del servizio e sulla relativa scalabilità, vedere le guide seguenti:

* [Elementi interni del Servizio Azure SignalR](signalr-concept-internals.md)
* [Scalabilità del servizio Azure SignalR](signalr-howto-scale-multi-instances.md)
