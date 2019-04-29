---
title: Guida alle prestazioni per il servizio Azure SignalR
description: Panoramica delle prestazioni del servizio Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: f7cc05c8c2a299d809c4386d119fef58fa2548d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269442"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guida alle prestazioni per il servizio Azure SignalR

Uno dei principali vantaggi dell'uso di servizio Azure SignalR è la facilità di scalabilità di applicazioni SignalR. In uno scenario su larga scala, le prestazioni sono un fattore importante. 

In questa Guida, verrà presentato i fattori che influiscono sulle prestazioni di applicazioni SignalR. Che descriveremo tipico delle prestazioni in diversi scenari di casi d'uso. Al termine, verrà presentato l'ambiente e gli strumenti che è possibile usare per generare un rapporto di prestazioni.

## <a name="term-definitions"></a>Definizioni dei termini

*Connessioni in entrata*: Il messaggio in arrivo al servizio Azure SignalR.

*Outbound*: Il messaggio in uscita dal servizio Azure SignalR.

*Bandwidth*: Le dimensioni totali di tutti i messaggi di 1 secondo.

*Modalità predefinita*: La modalità di utilizzo predefinita quando è stata creata un'istanza di servizio Azure SignalR. Azure SignalR Service prevede che il server di app per stabilire una connessione con esso prima che accetta connessioni client.

*Modalità senza server*: Modalità in cui servizio Azure SignalR accetta solo connessioni client. Non è consentita alcuna connessione al server.

## <a name="overview"></a>Panoramica

Azure SignalR Service definisce sette livelli Standard per le capacità di prestazioni diverso. Questa Guida risponde alle domande seguenti:

-   Che cos'è la tipica delle prestazioni servizio Azure SignalR per ogni livello?

-   Servizio Azure SignalR soddisfa i requisiti per la velocità effettiva dei messaggi (ad esempio, l'invio 100.000 di messaggi al secondo)?

-   Per il mio scenario specifico, quale livello è adatto per me? O come è possibile selezionare il livello corretto?

-   Il tipo di server app (dimensioni della macchina virtuale) è adatto per me? Quanti di essi è necessario distribuire?

Per rispondere a queste domande, questa guida offre prima di tutto una spiegazione di alto livello dei fattori che influiscono sulle prestazioni. Viene quindi illustrato come numero massimo di messaggi in ingresso e in uscita per ogni livello di casi d'uso tipici: **echo**, **broadcast**, **trasmissione al gruppo**, e **Invia a connessione** (peer-to-peer in una chat).

Questa Guida non è possibile coprire tutti gli scenari e casi d'uso diversi, le dimensioni dei messaggi, i modelli di invio messaggi e così via. Ma offre alcuni metodi che consentono di:

- Valutare i requisiti approssimativi per i messaggi in ingresso o in uscita.
- Trovare i livelli corretti controllando la tabella di prestazioni.

## <a name="performance-insight"></a>Informazioni dettagliate sulle prestazioni

In questa sezione vengono descritte le metodologie di valutazione delle prestazioni e quindi Elenca tutti i fattori che influiscono sulle prestazioni. Infine, fornisce metodi che consentono di valutare i requisiti di prestazioni.

### <a name="methodology"></a>Metodologia

*Velocità effettiva* e *latenza* sono due aspetti tipici della verifica delle prestazioni. Per il servizio Azure SignalR, ogni livello di SKU ha una proprio velocità effettiva dei criteri di limitazione. I criteri definiscono *velocità effettiva (in ingresso e in uscita larghezza di banda) massima* come la massima velocità effettiva ottenuta quando il 99% dei messaggi hanno una latenza che è minore di 1 secondo.

La latenza è l'intervallo di tempo di connessione che invia il messaggio di ricezione del messaggio di risposta dal servizio Azure SignalR. Diamo **echo** come esempio. Tutte le connessioni client aggiunge un timestamp nel messaggio. L'hub del server app invia il messaggio originale al client. Pertanto, il ritardo nella propagazione viene calcolato facilmente tutte le connessioni client. Il timestamp è collegato per tutti i messaggi **broadcast**, **trasmissione al gruppo**, e **inviare alla connessione**.

Per simulare migliaia di connessioni client simultanee, vengono create più macchine virtuali in una rete privata virtuale in Azure. Tutte queste macchine virtuali di connettersi alla stessa istanza del servizio Azure SignalR.

Nella modalità predefinita del servizio Azure SignalR, le macchine virtuali del server app vengono distribuiti nella stessa rete privata virtuale come client di macchine virtuali. Tutte le VM di client e server dell'app le macchine virtuali vengono distribuite nella stessa rete della stessa area per evitare la latenza tra più aree.

### <a name="performance-factors"></a>Fattori relativi alle prestazioni

In teoria, la capacità di servizio Azure SignalR è limitata dalle risorse di calcolo: CPU, memoria e rete. Ad esempio, le altre connessioni al servizio Azure SignalR che il servizio di utilizzare più memoria. Per il traffico dei messaggi di dimensioni maggiori (ad esempio, ogni messaggio è maggiore di 2048 byte), servizio Azure SignalR deve dedicare più cicli della CPU per elaborare il traffico. Nel frattempo, la larghezza di banda di rete di Azure impone anche un limite per il traffico massimo.

Il tipo di trasporto è un altro fattore che influisce sulle prestazioni. I tre tipi sono [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [evento inviato Server](https://en.wikipedia.org/wiki/Server-sent_events), e [Polling prolungato](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket è un protocollo di comunicazione full duplex e bidirezionale su una singola connessione TCP. Evento inviato server è un protocollo unidirezionale per i messaggi push dal server al client. Polling prolungato richiede ai client di eseguire periodicamente il polling delle informazioni dal server tramite una richiesta HTTP. Per la stessa API nelle stesse condizioni, WebSocket ha le migliori prestazioni, eventi inviati Server sono più lento e Polling prolungato è il più lento. Per impostazione predefinita, Azure SignalR Service consiglia WebSocket.

Il costo di routing del messaggio limita anche le prestazioni. Azure SignalR Service svolge un ruolo come router dei messaggi, che indirizza i messaggi da un set di client o server ad altri client o server. Uno scenario diverso o l'API richiede un criterio di routing diversi. 

Per la **echo**, il client invia un messaggio a se stesso e la destinazione di routing è anche se stesso. Questo modello è il costo più basso di routing. Ma per **broadcast**, **trasmissione al gruppo**, e **inviare alla connessione**, servizio Azure SignalR deve cercare le connessioni di destinazione tramite i dati interni distribuiti struttura. Questa elaborazione aggiuntiva Usa più CPU, memoria e larghezza di banda di rete. Di conseguenza, le prestazioni sono più lente.

Nella modalità predefinita, del server app potrebbe anche diventare un collo di bottiglia per determinati scenari. il SDK di SignalR Azure ha richiamare l'hub, mentre mantiene una connessione in tempo reale con tutti i client tramite segnali di heartbeat.

In modalità senza server, il client invia un messaggio dalla richiesta HTTP post, che non è efficiente quanto WebSocket.

Un altro fattore è protocollo: JSON e [MessagePack](https://msgpack.org/index.html). MessagePack è di dimensioni inferiori e viene recapitato più velocemente rispetto a JSON. MessagePack potrebbero non migliorare le prestazioni, tuttavia. Le prestazioni del servizio Azure SignalR sono sensibile ai protocolli non perché non non decodificare il payload del messaggio durante l'inoltro dei messaggi dai client ai server o viceversa.

In sintesi, i seguenti fattori influenzano la capacità in ingresso e in uscita:

-   Livello SKU (CPU/memoria)

-   Numero di connessioni

-   Dimensioni dei messaggi

-   velocità di invio messaggio

-   Tipo di trasporto (WebSocket, Server-invio-Event o Polling prolungato)

-   Scenario di caso d'uso (costo routing)

-   connessioni server e il servizio App (in modalità server)


### <a name="finding-a-proper-sku"></a>Ricerca di uno SKU appropriato

Come può valutare la capacità in ingresso/uscita o trovare quale livello è adatti per un caso d'uso specifico?

Si supponga che il server di app è abbastanza potente e non è un collo di bottiglia. Controllare quindi in ingresso e in uscita larghezza di banda massima per ogni livello.

#### <a name="quick-evaluation"></a>Valutazione rapida

È possibile semplificare la valutazione prima di tutto, presupponendo alcune impostazioni predefinite: 

- Il tipo di trasporto è WebSocket.
- La dimensione del messaggio è 2.048 byte.
- Un messaggio viene inviato un secondo.
- Azure SignalR Service è nella modalità predefinita.

Ogni livello ha un proprio massimo della larghezza di banda in ingresso e della larghezza di banda in uscita. Dopo la connessione in ingresso o in uscita supera il limite, un'esperienza utente uniforme non è garantita.

**Echo** offre la massima larghezza di banda in ingresso perché contiene il costo più basso di routing. **Broadcast** definisce la larghezza di banda massima dei messaggi in uscita.

Effettuare *non* superino i valori evidenziati in due tabelle seguenti.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni                       | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| **Larghezza di banda in ingresso** | **2 MBps**    | **4 MBps**    | **10 MBps**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Larghezza di banda in uscita | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Trasmissione             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000 |
| Larghezza di banda in ingresso  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Larghezza di banda in uscita** | **4 MBps**    | **8 MBps**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

*Connessioni in entrata della larghezza di banda* e *larghezza di banda in uscita* la dimensione totale dei messaggi al secondo.  Di seguito sono le formule per essi:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: Il numero di connessioni l'invio del messaggio.

- *outboundConnections*: Il numero di connessioni di ricezione del messaggio.

- *messageSize*: Le dimensioni di un singolo messaggio (valore medio). Un piccolo messaggio di minore di 1024 byte ha un impatto sulle prestazioni che è simile a un messaggio di 1.024 byte.

- *sendInterval*: Ora di invio di un messaggio. È in genere 1 secondo per ogni messaggio, ovvero l'invio di un messaggio ogni secondo. Un intervallo inferiore indica l'invio di più messaggi in un periodo di tempo. Ad esempio 0,5 secondi per ogni messaggio significa che l'invio di due messaggi ogni secondo.

- *Connessioni*: Soglia massima eseguito il commit per il servizio Azure SignalR per ogni livello. Se il numero di connessione viene aumentato ulteriormente, subirà dalla limitazione delle richieste di connessione.

#### <a name="evaluation-for-complex-use-cases"></a>Versione di valutazione per casi d'uso complessi

##### <a name="bigger-message-size-or-different-sending-rate"></a>Dimensione del messaggio più grande o diverse velocità di invio

Il caso d'uso reale è più complicato. Potrebbe inviare un messaggio di dimensioni superiori a 2048 byte o la velocità di invio messaggio è non un messaggio al secondo. Diamo trasmissione dell'Unit100 come esempio per scoprire come valutarne le prestazioni.

La tabella seguente illustra un caso d'uso reale di **broadcast**. Ma la dimensione dei messaggi, numero di connessioni e velocità di invio messaggi sono diversi da ciò che si presuppone che nella sezione precedente. La domanda è come è possibile dedurre uno di questi elementi (dimensione dei messaggi, numero di connessioni o velocità di invio di messaggi) se si conosce solo due di essi.

| Trasmissione  | Dimensioni dei messaggi | Messaggi in ingresso al secondo | connessioni | Inviare gli intervalli |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100,000     | 5 secondi                      |
| 2 | 256 KB               | 1                        | 8.000       | 5 secondi                      |

La formula seguente è semplice dedurre in base alla formula precedente:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Per Unit100, la massima larghezza di banda in uscita è 400 MB dalla tabella precedente. Per una dimensione di 20 KB del messaggio, il numero massimo di connessioni in uscita deve essere di 400 MB \* 5 / 20 KB = 100.000, che corrisponde al valore reale.

##### <a name="mixed-use-cases"></a>Casi d'uso misto

Il caso d'uso reale in genere una combinazione di quattro casi di uso di base tra loro: **echo**, **broadcast**, **trasmissione al gruppo**, e **inviare alla connessione**. La metodologia che consente di valutare la capacità è:

1. Dividere i casi di utilizzo misto in quattro casi di uso di base.
1. Calcolare la larghezza di banda massima dei messaggi in ingresso e in uscita tramite le formule precedenti separatamente.
1. Sommare i calcoli della larghezza di banda per ottenere il totale in ingresso/uscita larghezza di banda massima. 

Selezionare il livello corretto dalle tabelle di larghezza di banda in ingresso/uscita massima.

> [!NOTE]
> Per inviare un messaggio a centinaia o migliaia di piccoli gruppi, o per migliaia di client che inviano un messaggio tra loro, il costo di routing diventerà dominante. Prendere in considerazione tale impatto.

Per il caso d'uso di inviare un messaggio al client, assicurarsi che il server di app sia *non* il collo di bottiglia. La sezione "Case study" seguente vengono fornite indicazioni sul numero di server app è necessario e il numero di connessioni server è necessario configurare.

## <a name="case-study"></a>Case study

Le sezioni seguenti attraversano quattro casi d'uso tipici per il trasporto WebSocket: **echo**, **broadcast**, **trasmissione al gruppo**, e **inviare alla connessione**. Per ogni scenario, la sezione elenca la capacità in ingresso e in uscita corrente per il servizio Azure SignalR. Vengono inoltre illustrati i principali fattori che influiscono sulle prestazioni.

Nella modalità predefinita, il server di applicazione crea cinque connessioni al server con il servizio Azure SignalR. Il server di app Usa il SDK del servizio Azure SignalR per impostazione predefinita. Nei risultati del test delle prestazioni seguenti, le connessioni al server vengano aumentate a 15 (o più opzioni per la trasmissione e l'invio di un messaggio a un gruppo di grandi dimensione).

Diversi casi d'uso presentano requisiti diversi per i server di app. **Broadcast** deve numero ridotto di server app. **Echo** oppure **inviare alla connessione** richiede molti server di app.

In tutti i casi d'uso, la dimensione dei messaggi predefinita è 2048 byte e l'intervallo di invio del messaggio è di 1 secondo.

### <a name="default-mode"></a>Modalità predefinita

I client, server di app web e servizio Azure SignalR sono coinvolte nella modalità predefinita. Tutti i client è l'acronimo di una singola connessione.

#### <a name="echo"></a>Echo

Prima di tutto un'app web si connette al servizio Azure SignalR. In secondo luogo, molti client di connettersi all'app web, che reindirizza i client al servizio Azure SignalR con il token di accesso e l'endpoint. Quindi, i client di stabiliscono connessioni WebSocket con il servizio Azure SignalR.

Dopo che tutti i client di stabiliscono connessioni, all'avvio l'invio di un messaggio che contiene un timestamp per l'hub specifico ogni secondo. L'hub restituisce il messaggio al relativo client originale. Tutti i client consente di calcolare la latenza quando riceve nuovamente il messaggio echo.

Nel diagramma seguente, da 5 a 8 (traffico evidenziato rosso) sono in un ciclo. Il ciclo viene eseguito per una durata predefinita (5 minuti) e ottiene le statistiche di latenza di tutti i messaggi.

![Traffico per il caso d'uso echo](./media/signalr-concept-performance/echo.png)

Il comportamento delle **echo** determina che la massima larghezza di banda in ingresso è uguale alla larghezza di banda massima in uscita. Per informazioni dettagliate, vedere la tabella seguente.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni                       | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Messaggi in ingresso/in uscita al secondo | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Larghezza di banda in ingresso/uscita | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

In questo caso d'uso, tutti i client richiama l'hub definito nel server dell'app. L'hub chiama semplicemente il metodo definito sul lato client originale. Questo hub è l'hub più semplice per **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Anche per questo hub semplice, la pressione di traffico nel server dell'app è evidente come la **echo** connessioni in entrata messaggio aumento del carico. Questo utilizzo elevato di traffico richiede molti server di app per i livelli di grandi dimensioni dello SKU. La tabella seguente elenca il numero di server di app per ogni livello.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Il client connessione numerica, dimensione del messaggio messaggio l'invio di velocità, il livello SKU e CPU/memoria del server app compromettere le prestazioni complessive del **echo**.

#### <a name="broadcast"></a>Trasmissione

Per la **broadcast**, quando l'app web riceve il messaggio, lo trasmette a tutti i client. I client più vi sono per la trasmissione, il traffico di messaggi più c'è da tutti i client. Vedere il diagramma seguente.

![Traffico per il caso d'uso broadcast](./media/signalr-concept-performance/broadcast.png)

Un numero limitato di client trasmettono. La larghezza di banda di messaggio in ingresso è piccolo, ma la larghezza di banda in uscita è enorme. Consente di aumentare la larghezza di banda di messaggio in uscita della connessione client o aumenta la frequenza di broadcast.

Nella tabella seguente sono riepilogate le connessioni client massimo, conteggio dei messaggi in ingresso/uscita e la larghezza di banda.

|     Trasmissione             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000 |
| Messaggi in ingresso al secondo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Messaggi in uscita al secondo | 2.000 | 4.000 | 10,000 | 20.000 | 40.000 | 100,000 | 200.000 |
| Larghezza di banda in ingresso  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Larghezza di banda in uscita | 4 MBps   | 8 MBps   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

I client di broadcast che registra i messaggi disponibili non più di quattro. Necessitano di server app inferiore rispetto a **echo** poiché la quantità di messaggi in ingresso è limitata. Due server di app sono sufficienti per i contratti di servizio e considerazioni sulle prestazioni. Ma è necessario aumentare le connessioni del server predefinito per evitare lo sbilanciamento delle classi, in particolare per Unit50 e Unit100.

|   Trasmissione      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumentare le connessioni del server predefinite da 5 a 40 in ogni server di app per evitare possibili sbilanciata server connessioni al servizio Azure SignalR.
>
> Il numero della connessione client, dimensione del messaggio, velocità di invio di messaggi e il livello SKU compromettere le prestazioni complessive per **broadcast**.

#### <a name="send-to-group"></a>Trasmissione al gruppo

Il **trasmissione al gruppo** caso d'uso è un modello di traffico simile ai **broadcast**. La differenza è che dopo che i client stabiliscono connessioni WebSocket con il servizio Azure SignalR, devono aggiungere gruppi prima possono inviare un messaggio a un gruppo specifico. Il diagramma seguente illustra il flusso del traffico.

![Traffico per il caso d'uso per gruppo di trasmissione](./media/signalr-concept-performance/sendtogroup.png)

Membro del gruppo e conteggio dei gruppi sono due fattori che influiscono sulle prestazioni. Per semplificare l'analisi, si definiscono due tipi di gruppi:

- **Piccoli gruppi**: Ogni gruppo ha 10 connessioni. Il numero di gruppo è uguale a (numero massimo di connessione) / 10. Ad esempio, per Unit1, se sono presenti 1.000 conteggi di connessione, quindi abbiamo 1000 / 10 = 100 gruppi.

- **Gruppo grande**: Il numero di gruppo è sempre 10. Il numero di membri di gruppo è uguale a (numero massimo di connessione) / 10. Ad esempio, per Unit1, se sono presenti 1.000 conteggi di connessione, quindi ogni gruppo ha 1000 / 10 = 100 membri.

**Trasmissione al gruppo** offre un routing costo per il servizio Azure SignalR perché contiene trovare le connessioni di destinazione tramite una struttura di dati distribuiti. Come aumentare le connessioni mittente, il costo aumenta.

##### <a name="small-group"></a>Gruppo di piccole dimensioni

Il costo di routing è significativo per l'invio di messaggi a molti piccoli gruppi. Attualmente, l'implementazione del servizio Azure SignalR raggiunge il limite di costo di routing in Unit50. Aggiunta di più CPU e memoria problema persiste, in modo che non è possibile migliorare Unit100 ulteriormente per impostazione predefinita. Se è necessaria larghezza di banda in ingresso, contattare il supporto tecnico.

|   Inviare a piccoli gruppi     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000 | 100,000
| Numero di membri gruppo        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Conteggio gruppo               | 100   | 200   | 500    | 1.000  | 2.000  | 5.000  | 10,000 
| Messaggi in ingresso al secondo  | 200   | 400   | 1.000  | 2.500  | 4.000  | 7.000  | 7.000   |
| Larghezza di banda in ingresso  | 400 KBps  | 800 KBps  | 2 MBps     | 5 MBps     | 8 MBps     | 14 MBps    | 14 MBps     |
| Messaggi in uscita al secondo | 2.000 | 4.000 | 10,000 | 25.000 | 40.000 | 70,000 | 70,000  |
| Larghezza di banda in uscita | 4 MBps    | 8 MBps    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Numero di connessioni client chiama l'hub, in modo che anche il numero di server app è critico per le prestazioni. La tabella seguente elenca il numero di server app suggerite.

|  Inviare a piccoli gruppi   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Il client connessione numerica, dimensione del messaggio messaggio inviato al tasso, routing costo, il livello SKU e CPU/memoria del server app compromettere le prestazioni complessive del **inviare piccoli gruppi**.

##### <a name="big-group"></a>Gruppo di big Data

Per la **invia al gruppo di big data**, la larghezza di banda in uscita diventa il collo di bottiglia prima di raggiungere il routing limite di costo. La tabella seguente elenca il numero massimo in uscita larghezza di banda che è quasi uguale a quello utilizzato per **broadcast**.

|    Inviare al gruppo di big Data      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000
| Numero di membri gruppo        | 100   | 200   | 500    | 1.000  | 2.000  | 5.000   | 10,000 
| Conteggio gruppo               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Messaggi in ingresso al secondo  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Larghezza di banda in ingresso  | 80 KBps   | 40 KBps   | 40 KBps    | 20 KBps    | 40 KBps    | 40 KBps     | 40 KBps     |
| Messaggi in uscita al secondo | 2.000 | 4.000 | 10,000 | 20.000 | 40.000 | 100,000 | 200.000 |
| Larghezza di banda in uscita | 8 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

Il numero di connessioni mittente è non più di 40. Il carico di lavoro nel server dell'app è piccolo, in modo che il numero consigliato di App web è ridotto.

|  Inviare al gruppo di big Data  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumentare le connessioni del server predefinite da 5 a 40 in ogni server di app per evitare possibili sbilanciata server connessioni al servizio Azure SignalR.
> 
> Il numero della connessione client, dimensione del messaggio, velocità di invio di messaggi, costo di routing e livello SKU influiscono sulle prestazioni complessive del **invia al gruppo di big data**.

#### <a name="send-to-connection"></a>Invia alla connessione

Nel **inviare alla connessione** caso d'uso, quando i client stabiliscono le connessioni al servizio Azure SignalR, tutti i client chiama un hub speciale per ottenere i propri ID connessione. Il benchmark delle prestazioni raccoglie tutti gli ID di connessione, li sposta e viene riassegnato a tutti i client come destinazione di invio. I client di continuare a inviare il messaggio per la connessione di destinazione fino al termine del test delle prestazioni.

![Traffico per il caso d'uso di trasmissione-client](./media/signalr-concept-performance/sendtoclient.png)

Per il routing dei costi **inviare alla connessione** è simile al costo per **inviare piccoli gruppi**.

Man mano che aumenta il numero di connessioni, il costo di routing consente di limitare le prestazioni complessive. Unit50 ha raggiunto il limite. Di conseguenza, Unit100 non è possibile migliorare ulteriormente.

Nella tabella seguente è riportato un riepilogo statistico dopo molti cicli di esecuzione la **inviare alla connessione** benchmark.

|   Invia alla connessione   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| connessioni                        | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000          | 100,000         |
| Messaggi in ingresso/in uscita al secondo | 1.000 | 2.000 | 5.000 | 8.000  | 9000  | 20.000 | 20.000 |
| Larghezza di banda in ingresso/uscita | 2 MBps    | 4 MBps    | 10 MBps   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

In questo caso d'uso richiede un carico elevato sul lato server app. Visualizzare il server app suggerite count nella tabella seguente.

|  Invia alla connessione  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Il client connessione numerica, dimensione del messaggio messaggio l'invio di velocità, routing costo, il livello SKU e CPU/memoria per il server app compromettere le prestazioni complessive del **inviare alla connessione**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR echo, trasmettere e inviarlo al gruppo di piccole dimensioni

Azure SignalR Service fornisce la stessa capacità di prestazioni per ASP.NET SignalR. 

Il test delle prestazioni utilizza le app Web di Azure dalle [S3 piano di servizio Standard](https://azure.microsoft.com/pricing/details/app-service/windows/) ASP.NET SignalR.

Nella tabella seguente fornisce il numero di app web suggerita per ASP.NET SignalR **echo**.

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

Nella tabella seguente fornisce il numero di app web suggerita per ASP.NET SignalR **broadcast**.

|  Trasmissione       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

Nella tabella seguente fornisce il numero di app web suggerita per ASP.NET SignalR **inviare piccoli gruppi**.

|  Inviare a piccoli gruppi     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Modalità senza server

I client e servizio Azure SignalR sono coinvolti in modalità senza server. Tutti i client è l'acronimo di una singola connessione. Il client invia messaggi tramite l'API REST a un altro client o trasmettere messaggi a tutti.

L'invio di messaggi ad alta densità tramite l'API REST non è più efficiente usando WebSocket. È possibile creare una nuova connessione HTTP ogni volta che richiede e che è in modalità senza server costi aggiuntivi.

#### <a name="broadcast-through-rest-api"></a>Trasmettere tramite l'API REST
Tutti i client di stabiliscono connessioni WebSocket con il servizio Azure SignalR. Quindi alcuni client avvio la trasmissione tramite l'API REST. Messaggio inviato (in ingresso) è tutto tramite HTTP Post, che non è efficiente rispetto a WebSocket.

|   Trasmettere tramite l'API REST     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000 |
| Messaggi in ingresso al secondo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Messaggi in uscita al secondo | 2.000 | 4.000 | 10,000 | 20.000 | 40.000 | 100,000 | 200.000 |
| Larghezza di banda in ingresso  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Larghezza di banda in uscita | 4 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Invia all'utente tramite l'API REST
Il benchmark assegna i nomi utente per tutti i client prima di avviare la connessione al servizio Azure SignalR. Dopo che i client di stabiliscono una connessione WebSocket con il servizio Azure SignalR, all'avvio l'invio di messaggi ad altri utenti tramite HTTP Post.

|   Invia all'utente tramite l'API REST | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000 |
| Messaggi in ingresso al secondo  | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18,000  |
| Messaggi in uscita al secondo | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18,000 |
| Larghezza di banda in ingresso  | 600 KBps  | 1.2 MBps  | 1.8 MBps   | 2.6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |
| Larghezza di banda in uscita | 600 KBps  | 1.2 MBps  | 1.8 MBps   | 2.6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |

## <a name="performance-test-environments"></a>Ambienti di test delle prestazioni

Per tutti i casi elencati in precedenza di utilizzo, ha condotto dei test delle prestazioni in un ambiente Azure. Abbiamo utilizzato al massimo 50 macchine virtuali client e server dell'app 20 macchine virtuali. Ecco alcuni dettagli:

- Dimensioni di macchina virtuale client: StandardDS2V2 (2 vCPU, memoria 7G)

- Dimensioni della macchina virtuale del server App: StandardF4sV2 (4 vCPU, memoria di 8G)

- Connessioni al server SignalR SDK Azure: 15

## <a name="performance-tools"></a>Strumenti per le prestazioni

È possibile trovare strumenti per le prestazioni per il servizio Azure SignalR sul [GitHub](https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo, sarà possibile ottenere una panoramica delle prestazioni servizio Azure SignalR in scenari di casi d'uso tipici.

Per informazioni sugli elementi interni del servizio e la scalabilità, leggere le guide seguenti:

* [Elementi interni di Azure SignalR Service](signalr-concept-internals.md)
* [Azure SignalR Service ridimensionamento](signalr-howto-scale-multi-instances.md)
