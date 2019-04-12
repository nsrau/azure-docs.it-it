---
title: Guida alle prestazioni per il servizio Azure SignalR
description: Panoramica delle prestazioni del servizio Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: 53139dd253c491ea6578fd0b9cbada4e7b331c7d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502039"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guida alle prestazioni per il servizio Azure SignalR

Uno dei vantaggi principali per l'uso di servizio Azure SignalR è la facilità di scalabilità di applicazioni SignalR. In uno scenario su larga scala, prestazioni diventano un fattore importante. In questa guida verranno presentati i fattori che hanno un impatto sulle prestazioni dell'applicazione SignalR e in diversi casi d'uso, che cos'è la tipica delle prestazioni? Al termine, si introdurrà anche l'ambiente e gli strumenti usati per generare report di prestazioni.

## <a name="terms-definition"></a>Definizione di condizioni

*ASRS*: Servizio Azure SignalR

*Connessioni in entrata*: il messaggio in arrivo al servizio Azure SignalR

*In uscita*: il messaggio in uscita dal servizio Azure SignalR

*Larghezza di banda*: dimensione totale di tutti i messaggi di 1 secondo

*Modalità predefinita*: ASRS prevede che il server di app per stabilire una connessione con esso prima di accettare connessioni client. È la modalità di utilizzo predefinita quando è stato creato un ASRS.

*Modalità senza server*: ASRS accetta solo connessioni client. Non è consentita alcuna connessione al server.

## <a name="overview"></a>Panoramica

ASRS definisce sette livelli Standard per le capacità di prestazioni diverso e in questa guida si propone di rispondere alle domande seguenti:

-   Che cos'è la prestazioni ASRS tipiche per ogni livello?

-   ASRS soddisfa il requisito di velocità effettiva dei messaggi, ad esempio, l'invio di 100.000 messaggi al secondo?

-   Per il mio scenario specifico, quale livello è adatto per me? O come è possibile selezionare il livello corretto?

-   Il tipo di server app (dimensioni della macchina virtuale) è ideale per me e quante di esse devono distribuire?

Per rispondere a queste domande, questa Guida alle prestazioni prima fornisce una spiegazione di alto livello sui fattori che hanno un impatto sulle prestazioni, quindi viene illustrato i numero massimo messaggi in ingresso e in uscita per ogni livello di casi d'uso tipici: **echo**, **broadcast**, **al gruppo di trasmissione**, e **trasmissione / connessioni** (to peer chat).

Non è possibile per questo documento coprire tutti gli scenari (e caso d'uso diversi, dimensione del messaggio diverso o modello l'invio di messaggi e così via.). Tuttavia, fornisce alcuni metodi di valutazione per consentire agli utenti di circa valutare i requisiti dei messaggi in ingresso o in uscita, quindi trovare i livelli corretti controllando la tabella di prestazioni.

## <a name="performance-insight"></a>Informazioni dettagliate sulle prestazioni

In questa sezione vengono descritte le metodologie di valutazione delle prestazioni e quindi Elenca tutti i fattori che hanno un impatto sulle prestazioni. Al termine, fornisce metodi per consentire di valutare i requisiti di prestazioni.

### <a name="methodology"></a>Metodologia

**Velocità effettiva** e **latenza** sono due aspetti tipici della verifica delle prestazioni. Per ASRS, livello di SKU diversi ha una velocità effettiva diversa la limitazione delle richieste dei criteri. Questo documento viene definito **velocità effettiva (in ingresso e in uscita larghezza di banda) massima** come il numero massimo di velocità effettiva ottenuta quando 99% dei messaggi hanno latenza minore di 1 secondo.

La latenza è l'intervallo di tempo di connessione che invia messaggi alla ricezione del messaggio di risposta da ASRS. Diamo **echo** ad esempio, tutte le connessioni client aggiunge un timestamp nel messaggio. L'hub del server App invia il messaggio originale al client. Pertanto, il ritardo nella propagazione viene calcolato facilmente tutte le connessioni client. Il timestamp è collegato per tutti i messaggi **broadcast**, **al gruppo di trasmissione**, e **trasmissione / connessioni**.

Per simulare migliaia di connessioni client simultanee, vengono create più macchine virtuali in una rete privata virtuale in Azure. Tutte queste macchine virtuali di connettersi all'istanza ASRS stesso.

Nella modalità predefinita ASRS, le macchine virtuali del server app vengono distribuiti anche nella stessa rete privata virtuale come client di macchine virtuali.

Tutte le macchine virtuali client e server dell'app che le VM vengono distribuite nella stessa rete della stessa area per evitare tra area latenza.

### <a name="performance-factors"></a>Fattori relativi alle prestazioni

In teoria, capacità ASRS è limitata dalle risorse di calcolo: CPU, memoria e rete. Ad esempio, le altre connessioni a ASRS, più memoria ASRS utilizzato. Per il traffico di messaggi più grande, ad esempio, ogni messaggio è superiore a 2048 byte, richiede ASRS dedicare più cicli della CPU per elaborare anche. Nel frattempo, la larghezza di banda di rete di Azure impone anche un limite per il traffico massimo.

Il tipo di trasporto [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [evento inviato Sever](https://en.wikipedia.org/wiki/Server-sent_events), o [Polling prolungato](https://en.wikipedia.org/wiki/Push_technology), è un altro fattore influisce sulle prestazioni. WebSocket è un protocollo di comunicazione bidirezionale e full duplex in un'unica connessione TCP. Evento inviato server invece protocollo unidirezionale al messaggio push dal server al client. Polling prolungato richiede ai client di eseguire periodicamente il polling delle informazioni dal server tramite una richiesta HTTP. Per l'API stessa con la stessa condizione, WebSocket ha le migliori prestazioni, eventi inviati server sono più lento e Polling prolungato è il più lento. ASRS consiglia WebSocket per impostazione predefinita.

Inoltre, il costo di routing del messaggio limita anche le prestazioni. ASRS svolge un ruolo come router dei messaggi, che indirizza i messaggi da un set di client o server ad altri client o server. Uno scenario diverso o l'API richiede diversi criteri di routing. Per la **echo**, il client invia un messaggio a se stesso e la destinazione di routing è anche se stesso. Questo modello è il costo più basso di routing. Ma per **broadcast**, **al gruppo di trasmissione**, **trasmissione / connessioni**, ASRS deve cercare le connessioni di destinazione tramite la struttura interna dei dati distribuito, che utilizza più CPU, memoria e persino larghezza di banda di rete. Di conseguenza, le prestazioni sono più lenta **echo**.

Nella modalità predefinita, il server di app può anche diventare un collo di bottiglia per determinati scenari, perché Azure SignalR SDK deve richiamare l'Hub, nel frattempo mantiene la connessione in tempo reale con tutti i client tramite segnali heartbeat.

In modalità senza server, il client invia messaggi mediante HTTP post, che non è efficiente quanto WebSocket.

Un altro fattore è protocollo: JSON e [MessagePack](https://msgpack.org/index.html). MessagePack è di dimensioni inferiori e viene recapitato più velocemente rispetto a JSON. Intuitivamente, MessagePack può trarre vantaggio delle prestazioni, ma prestazioni ASRS non sensibili con protocolli poiché non decodifica il payload del messaggio durante l'inoltro dei messaggi dai client ai server o viceversa.

In sintesi, i fattori seguenti hanno un impatto sulla capacità in ingresso e in uscita:

-   Livello SKU (CPU/memoria)

-   numero di connessioni

-   Dimensione del messaggio

-   velocità di invio messaggio

-   tipo di trasporto (WebSocket/server-invio-evento/Polling prolungato)

-   usare scenari dei casi d' (costo routing)

-   connessioni server e il servizio App (in modalità server)


### <a name="find-a-proper-sku"></a>Trovare uno SKU appropriato

Come valutare la capacità in ingresso/uscita o come trovare quale livello è adatto per un caso d'uso specifico?

Si presuppone che il server di app è abbastanza potente e non è un collo di bottiglia. È quindi possibile controllare in ingresso e in uscita larghezza di banda massima per ogni livello.

#### <a name="quick-evaluation"></a>Valutazione rapida

È possibile semplificare la valutazione prima di tutto, presupponendo alcune impostazioni predefinite: WebSocket viene utilizzato, dimensione del messaggio è 2048 byte, l'invio di un secondo messaggio ed è predefinita in modalità.

Ogni livello ha un proprio massimo della larghezza di banda in ingresso e della larghezza di banda in uscita. Esperienza utente uniforme non è garantito quando l'ingresso o in uscita supera il limite.

**Echo** offre la massima larghezza di banda in ingresso perché contiene il costo più basso di routing. **Broadcast** definisce la larghezza di banda massima dei messaggi in uscita.

Effettuare **non** superino i valori evidenziati in due tabelle seguenti.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni                       | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| **Larghezza di banda in ingresso (byte/sec)** | **2 MINUTI**    | **4 MINUTI**    | **10M**   | **20 MILIONI**    | **40M**    | **100 MILIONI**   | **200 MB**    |
| Larghezza di banda in uscita (byte/sec) | 2 MINUTI    | 4 MINUTI    | 10M   | 20 MILIONI    | 40M    | 100 MILIONI   | 200 MB    |


|     Trasmissione             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000 |
| Larghezza di banda in ingresso (byte/sec)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K     |
| **Larghezza di banda in uscita (byte/sec)** | **4 MINUTI**    | **8 MB**    | **20 MILIONI**    | **40M**    | **80 MB**    | **200 MB**    | **400 MILIONI**   |

Larghezza di banda in ingresso e le formule di larghezza di banda in uscita:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

*inboundConnections*: il numero di connessioni l'invio di messaggi

*outboundConnections*: il numero di connessioni di ricezione messaggio

*messageSize*: le dimensioni di un singolo messaggio (valore medio). Per messaggio di piccole dimensioni le cui dimensioni sono inferiori a 1024 byte, ha l'impatto sulle prestazioni simili come messaggio di 1024 byte.

*sendInterval*: l'ora di invio di un messaggio, si tratta in genere 1 secondo per ogni messaggio, ovvero l'invio di un messaggio ogni secondo. Più piccoli sendInterval significa l'invio di più messaggi determinato periodo di tempo. Ad esempio 0,5 secondi per ogni messaggio significa che l'invio di due messaggi ogni secondo.

*Le connessioni* è la soglia massima commit ASRS per ogni livello. Se il numero di connessione viene aumentato ulteriormente, subirà dalla limitazione delle richieste di connessione.

*Connessioni in entrata della larghezza di banda* e *larghezza di banda in uscita* la dimensione totale dei messaggi al secondo. Qui sto ' significa megabyte per motivi di semplicità.

#### <a name="evaluation-for-complex-use-cases"></a>Versione di valutazione per casi d'uso complessi

##### <a name="bigger-message-size-or-different-sending-rate"></a>Dimensione del messaggio più grande o diverse velocità di invio

Il caso d'uso reale è più complicato. Può inviare messaggio di dimensioni superiori a 2048 byte o velocità di invio dei messaggi è non un messaggio al secondo. Diamo trasmissione dell'unit100 come esempio per scoprire come valutarne le prestazioni.

Nella tabella seguente viene illustrato un caso reale dei **broadcast**, ma la dimensione dei messaggi, numero di connessioni e velocità di invio messaggi sono diversi da ciò che si presuppone che nella sezione precedente. La domanda è come è possibile dedurre uno di questi elementi (dimensione dei messaggi, numero di connessioni o velocità di invio di messaggi) se si conosce solo 2 di essi.

| Trasmissione  | Dimensione del messaggio (byte) | Connessioni in entrata (messaggi/sec) | connessioni | Inviare gli intervalli (secondi) |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 K                 | 1                        | 100,000     | 5                       |
| 2 | 256 K                | 1                        | 8.000       | 5                       |

La formula seguente è facilmente per essere dedotte in base alla formula precedente esistente:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Per unit100, sappiamo che la larghezza di banda in uscita massimo è 400 milioni dalla tabella precedente, quindi per le dimensioni del messaggio di 20 KB, il numero massimo di connessioni in uscita deve essere 400 milioni \* 5 / 20 KB = 100.000, che corrisponde al valore reale.

##### <a name="mixed-use-cases"></a>Casi d'uso misto

Il caso d'uso reale in genere una combinazione di quattro casi di uso di base tra loro: **echo**, **broadcast**, **trasmissione al gruppo**, oppure **inviare alla connessione**. La metodologia utilizzata per valutare la capacità consiste nel dividere quattro casi di uso di base, i casi di utilizzo misto **calcolare la larghezza di banda massima dei messaggi in ingresso e in uscita** utilizzando le formule precedenti separatamente e sommare i valori in modo da ottenere il totale larghezza di banda in ingresso/uscita massima. Selezionare il livello corretto dalle tabelle di larghezza di banda in ingresso/uscita massima.

Nel frattempo, per l'invio di messaggi fino a centinaia o migliaia di piccoli gruppi o migliaia di client che inviano messaggi tra loro, il costo di routing diventerà dominante. Tale impatto debba tener conto. Ulteriori dettagli sono disponibili nelle sezioni seguenti "Case study".

Per il caso d'uso dell'invio messaggio ai client, assicurarsi che sia il server di app **non** il collo di bottiglia. Sezione "Case study" fornisce le linee guida su quanti server app è necessario e il numero di connessioni server deve essere configurato.

## <a name="case-study"></a>Case study

Le sezioni seguenti attraversano quattro casi d'uso tipici per il trasporto WebSocket: **echo**, **broadcast**, **al gruppo di trasmissione**, e **trasmissione / connessioni** . Per ogni scenario, vengono elencati i ASRS corrente in ingresso e capacità in uscita, nel frattempo spiega che cos'è i principali fattori sulle prestazioni.

Parola chiave default, la modalità server di App, tramite il SDK del servizio Azure SignalR per impostazione predefinita, crea cinque connessioni al server con ASRS. Sulle prestazioni risultato seguente, le connessioni sono aumentate a 15 (o più opzioni per la trasmissione al gruppo di big Data l'invio del messaggio) server del test.

Diversi casi d'uso sono previsti requisiti differenti sui server app. **Broadcast** deve numero ridotto di server app. **Echo** oppure **trasmissione / connessioni** richiede molti server di app.

In tutti i casi d'uso, la dimensione dei messaggi predefinita è 2048 byte e intervallo di invio dei messaggi è 1 secondo.

## <a name="default-mode"></a>Modalità predefinita

I client, server di app web e ASRS sono coinvolti in questa modalità. Tutti i client è l'acronimo di una singola connessione.

### <a name="echo"></a>Echo

In primo luogo, App web di connettersi a ASRS. In secondo luogo, molti i client si connettono all'app web, che si viene reindirizzati i client ASRS con il token di accesso e l'endpoint. Quindi, i client stabiliscono connessioni WebSocket con ASRS.

Dopo che tutti i client di stabiliscono connessioni, all'avvio l'invio di messaggi, che contiene un timestamp per l'Hub specifico ogni secondo. L'Hub restituisce il messaggio al relativo client originale. Tutti i client consente di calcolare la latenza quando riceve nuovamente il messaggio echo.

I passaggi 5\~8 (traffico evidenziato rossa) si trovano in un ciclo, che verrà eseguito per una durata predefinita (5 minuti) e ottenere le statistiche di latenza di tutti i messaggi.
La Guida alle prestazioni Mostra il numero massimo numero di connessione client.

![Echo](./media/signalr-concept-performance/echo.png)

**Echo**del comportamento determina che la massima larghezza di banda in ingresso è uguale alla larghezza di banda massima in uscita. Vedere la tabella seguente.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni                       | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Connessioni in entrata/in uscita (messaggi/sec) | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Larghezza di banda in ingresso/in uscita (byte/sec) | 2 MINUTI    | 4 MINUTI    | 10M   | 20 MILIONI    | 40M    | 100 MILIONI   | 200 MB    |

In questo caso d'uso, tutti i client richiama l'hub definito nel server dell'app. L'hub chiama semplicemente il metodo definito sul lato client originale. Questo hub è l'hub pesato più chiaro per **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Anche per questo hub semplice, la pressione di traffico sul server app è anche evidente quanto il **echo** in ingresso aumenta di messaggio. È pertanto necessario molti server di app per i livelli di grandi dimensioni dello SKU. La tabella seguente elenca il numero di server di app per ogni livello.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Il client connessione numerica, dimensione del messaggio messaggio l'invio di velocità, il livello SKU e CPU/memoria del server di app attiva impatto sulle prestazioni complessive del **echo**.

### <a name="broadcast"></a>Trasmissione

Per la **broadcast**, quando l'app web riceve il messaggio, lo trasmette a tutti i client. Il numero di client per la trasmissione, il traffico di messaggi più a tutti i client. Vedere il diagramma seguente.

![Trasmissione](./media/signalr-concept-performance/broadcast.png)

La caratteristica di trasmissione è che sono presenti un numero limitato di client broadcast, che significa che la larghezza di banda di messaggio in ingresso è piccolo, ma la larghezza di banda in uscita è enorme. Consente di aumentare la larghezza di banda di messaggio in uscita della connessione client o aumenta la frequenza di broadcast.

Il numero massimo connessioni client, numero di messaggi in ingresso/uscita e della larghezza di banda sono riepilogati nella tabella seguente.

|     Trasmissione             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000 |
| Connessioni in entrata (messaggi/sec)  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| In uscita (messaggi/sec) | 2.000 | 4.000 | 10,000 | 20.000 | 40.000 | 100,000 | 200.000 |
| Larghezza di banda in ingresso (byte/sec)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Larghezza di banda in uscita (byte/sec) | 4 MINUTI    | 8 MB    | 20 MILIONI    | 40M    | 80 MB    | 200 MB    | 400 MILIONI    |

I client di broadcast che registra i messaggi sono non più di 4, quindi richiede un minor numero di server app rispetto a **echo** poiché la quantità di messaggi in ingresso è limitata. Due server di app sono sufficienti da tenere in considerazione i contratti di servizio e le prestazioni. Ma le connessioni del server predefinita devono essere aumentate per evitare un utilizzo non equilibrato problema specialmente per Unit50 e Unit100.

|   Trasmissione      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Aumentare le connessioni del server predefinite da 5 a 40 in ogni server di app per evitare possibili sbilanciata le connessioni ai ASRS.
>
> Il numero della connessione client, dimensione del messaggio, velocità di invio di messaggi e il livello SKU hanno impatto sulle prestazioni complessive per **broadcast**

### <a name="send-to-group"></a>Trasmissione al gruppo

**Trasmissione a gruppo** ha simile modello di traffico, ad eccezione del fatto che dopo la creazione di connessioni WebSocket con ASRS client, i gruppi devono aggiungere prima possono inviare messaggi a un gruppo specifico. Nel diagramma seguente è illustrato il flusso del traffico.

![Inviare al gruppo](./media/signalr-concept-performance/sendtogroup.png)

Membro del gruppo e conteggio dei gruppi sono due fattori con impatto sulle prestazioni. Per semplificare l'analisi, si definiscono due tipi di gruppi: piccola e categoria di big Data.

- `small group`: 10 connessioni in ogni gruppo. Il numero di gruppo è uguale a (numero massimo di connessione) / 10. Ad esempio, per 1 unità, se sono presenti 1000 i conteggi di connessione, quindi abbiamo 1000 / 10 = 100 gruppi.

- `Big group`: Numero di gruppo è sempre 10. Il numero di membri di gruppo è uguale a (numero massimo di connessione) / 10. Ad esempio, per unità di 1, se sono presenti 1000 i conteggi di connessione, quindi ogni gruppo ha 1000 / 10 = 100 membri.

**Trasmissione a gruppo** offre costi routing ASRS perché contiene trovare le connessioni di destinazione tramite una struttura di dati distribuiti. Come aumentare le connessioni mittente, il costo aumenta anche.

#### <a name="small-group"></a>Gruppo di piccole dimensioni

Il costo di routing è significativo per l'invio di messaggi a molti piccoli gruppi. Attualmente, l'implementazione ASRS raggiunge il limite di costo routing in unit50. Aggiungendo altre CPU e memoria non Guida in linea, in modo che non è possibile migliorare unit100 ulteriormente per impostazione predefinita. Se hai bisogno di larghezza di banda in ingresso, contattare il supporto tecnico per la personalizzazione.

|   Inviare a piccoli gruppi     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000 | 100,000
| Numero di membri gruppo        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Conteggio gruppo               | 100   | 200   | 500    | 1.000  | 2.000  | 5.000  | 10,000 
| Connessioni in entrata (messaggi/sec)  | 200   | 400   | 1.000  | 2.500  | 4.000  | 7.000  | 7.000   |
| Larghezza di banda in ingresso (byte/sec)  | 400 K  | 800 K  | 2 MINUTI     | 5M     | 8 MB     | 14M    | 14M     |
| In uscita (messaggi/sec) | 2.000 | 4.000 | 10,000 | 25.000 | 40.000 | 70,000 | 70,000  |
| Larghezza di banda in uscita (byte/sec) | 4 MINUTI    | 8 MB    | 20 MILIONI    | 50 MILIONI     | 80 MB    | M 140   | M 140    |

Esistono molte connessioni client, la chiamata dell'hub, pertanto, anche il numero di server app è critico per le prestazioni. Nella tabella seguente viene elencato il numero di server app suggerite.

|  Inviare a piccoli gruppi   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Il numero di connessione client, la dimensione, velocità di invio di messaggi, costo di routing, il livello SKU e CPU/memoria del server app avere impatto sulle prestazioni complessive del **trasmissione a piccoli gruppi**.

#### <a name="big-group"></a>Gruppo di big Data

Per la **send-a-big-group**, la larghezza di banda in uscita diventa il collo di bottiglia prima di raggiungere il routing limite di costo. La tabella seguente elenca il numero massimo in uscita larghezza di banda che è pressoché lo stesso come **broadcast**.

|    Inviare al gruppo di big Data      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000
| Numero di membri gruppo        | 100   | 200   | 500    | 1.000  | 2.000  | 5.000   | 10,000 
| Conteggio gruppo               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Connessioni in entrata (messaggi/sec)  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Larghezza di banda in ingresso (byte/sec)  | 80 K   | 40 K   | 40 K    | 20 K    | 40 K    | 40 K     | 40 K     |
| In uscita (messaggi/sec) | 2.000 | 4.000 | 10,000 | 20.000 | 40.000 | 100,000 | 200.000 |
| Larghezza di banda in uscita (byte/sec) | 8 MB    | 8 MB    | 20 MILIONI    | 40M    | 80 MB    | 200 MB    | 400 MILIONI    |

Il numero di connessioni mittente è non più di 40, la responsabilità di server app è di piccole dimensioni, pertanto il numero di app web suggerito ha dimensioni ridotte.

|  Inviare al gruppo di big Data  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Aumentare le connessioni del server predefinite da 5 a 40 in ogni server di app per evitare possibili sbilanciata le connessioni ai ASRS.
> 
> Il numero della connessione client, dimensione del messaggio, velocità di invio di messaggi, costo di routing e livello SKU avere impatto sulle prestazioni complessive del **send-a-big-group**.

### <a name="send-to-connection"></a>Invia alla connessione

In questo caso d'uso, quando i client di stabiliscono le connessioni a ASRS, ogni client chiama un hub speciale per ottenere i propri ID connessione. Il benchmark delle prestazioni ha la responsabilità di raccogliere tutti gli ID di connessione, shuffle li e riassegnarli a tutti i client come destinazione di invio. I client di continuare a inviare messaggi per la connessione di destinazione fino al termine del test delle prestazioni.

![Inviare al client](./media/signalr-concept-performance/sendtoclient.png)

Per il routing dei costi **trasmissione-a-connection** è simile a quello **trasmissione a piccoli gruppi**.

Man mano che aumenta il numero di connessioni, le prestazioni complessive sono limitata dal costo di routing. 50 unità ha raggiunto il limite. Di conseguenza, 100 unità non è possibile migliorare ulteriormente.

La tabella seguente riporta una riepilogo delle statistiche dopo molti cicli di esecuzione **trasmissione / connessioni** benchmark

|   Invia alla connessione   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| connessioni                        | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000          | 100,000         |
| In ingresso / in uscita (messaggi/sec) | 1.000 | 2.000 | 5.000 | 8.000  | 9000  | 20.000 | 20.000 |
| Larghezza di banda in ingresso / in uscita (byte/sec) | 2 MINUTI    | 4 MINUTI    | 10M   | 16M    | 18M    | 40M       | 40M       |

In questo caso d'uso richiede un carico elevato sul lato server dell'app. Visualizzare il server app suggerite count nella tabella seguente.

|  Invia alla connessione  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Il numero di connessione client, la dimensione, velocità di invio di messaggi, costo di routing, il livello SKU e CPU/memoria del server app avere impatto sulle prestazioni complessive del **trasmissione / connessioni**.

### <a name="aspnet-signalr-echobroadcastsend-to-connection"></a>ASP.NET SignalR echo/trasmissione/trasmissione / connessioni

ASRS fornisce la stessa capacità di prestazioni per ASP.NET SignalR. In questa sezione fornisce il numero di app web suggerita per ASP.NET SignalR **echo**, **broadcast**, e **trasmissione a piccoli gruppi**.

Il test delle prestazioni Usa App Web di Azure di [S3 piano di servizio Standard](https://azure.microsoft.com/pricing/details/app-service/windows/) ASP.NET SignalR.

- `echo`

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

- `broadcast`

|  Trasmissione       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

- `Send-to-small-group`

|  Inviare a piccoli gruppi     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connessioni      | 1.000 | 2.000 | 5.000 | 10,000 | 20.000 | 50.000 | 100,000 |
| Numero di server app | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

## <a name="serverless-mode"></a>Modalità senza server

I client e ASRS sono coinvolti in questa modalità. Tutti i client è l'acronimo di una singola connessione. Il client invia messaggi tramite l'API REST a un altro client o trasmettere messaggi a tutti.

L'invio di messaggi ad alta densità tramite l'API REST non efficiente come WebSocket, perché è necessaria per creare una nuova connessione HTTP ogni volta - costi in modalità senza server aggiuntivi.

### <a name="broadcast-through-rest-api"></a>Trasmettere tramite l'API REST
Tutti i client di stabiliscono connessioni WebSocket con ASRS. Quindi alcuni client avvio la trasmissione tramite l'API REST. Messaggio inviato (in ingresso) sono tutto tramite HTTP Post, che non è efficiente rispetto a WebSocket.

|   Trasmettere tramite l'API REST     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000 |
| Connessioni in entrata (messaggi/sec)  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| In uscita (messaggi/sec) | 2.000 | 4.000 | 10,000 | 20.000 | 40.000 | 100,000 | 200.000 |
| Larghezza di banda in ingresso (byte/sec)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Larghezza di banda in uscita (byte/sec) | 4 MINUTI    | 8 MB    | 20 MILIONI    | 40M    | 80 MB    | 200 MB    | 400 MILIONI    |

### <a name="send-to-user-through-rest-api"></a>Invia all'utente tramite l'API REST
Il benchmark assegna i nomi utente per tutti i client prima di avviare la connessione a ASRS. Dopo che i client di stabilire connessioni WebSocket con ASRS, all'avvio l'invio di messaggi ad altri utenti tramite HTTP Post.

|   Invia all'utente tramite l'API REST | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connessioni               | 1.000 | 2.000 | 5.000  | 10,000 | 20.000 | 50.000  | 100,000 |
| Connessioni in entrata (messaggi/sec)  | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18,000  |
| In uscita (messaggi/sec) | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18,000 |
| Larghezza di banda in ingresso (byte/sec)  | 600 K  | 1.2 M  | 1,8 METRI   | 2.6 M   | 4 MINUTI     | 10M     | 36M    |
| Larghezza di banda in uscita (byte/sec) | 600 K  | 1.2 M  | 1,8 METRI   | 2.6 M   | 4 MINUTI     | 10M     | 36M    |

## <a name="performance-test-environments"></a>Ambienti di test delle prestazioni

Il test delle prestazioni per tutti i casi d'uso elencati in precedenza sono stati eseguiti nell'ambiente Azure. La maggior parte delle macchine virtuali di 50 client e server dell'app 20 macchine virtuali vengono usate.

Dimensioni di macchina virtuale client: StandardDS2V2 (2 vCPU, memoria 7G)

Dimensioni della macchina virtuale del server App: StandardF4sV2 (4 vCPU, memoria di 8G)

Connessioni al server SignalR SDK Azure: 15

## <a name="performance-tools"></a>Strumenti per le prestazioni

https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin

## <a name="next-steps"></a>Passaggi successivi

In questo articolo, si ottiene una panoramica delle prestazioni di SignalR Service in scenari dei casi d'uso tipici.

Per altre informazioni sugli elementi interni di SignalR Service e la scalabilità per SignalR Service, leggere la Guida seguente.

* [Elementi interni di servizio Azure SignalR](signalr-concept-internals.md)
* [Servizio Azure SignalR ridimensionamento](signalr-howto-scale-multi-instances.md)