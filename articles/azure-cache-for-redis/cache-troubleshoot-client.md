---
title: Risolvere i problemi relativi al lato client di cache di Azure per Redis
description: Informazioni su come risolvere i problemi comuni lato client con cache di Azure per Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: yegu
ms.openlocfilehash: a4fdbe9c0943e77719a9ee9da7dc358696284d99
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122624"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Risolvere i problemi relativi al lato client di cache di Azure per Redis

Questa sezione illustra la risoluzione dei problemi che si verificano a causa di una condizione nel client Redis usata dall'applicazione.

- [Pressione della memoria nel client Redis](#memory-pressure-on-redis-client)
- [Traffico in sequenza](#traffic-burst)
- [Utilizzo elevato della CPU client](#high-client-cpu-usage)
- [Limitazione della larghezza di banda lato client](#client-side-bandwidth-limitation)
- [Dimensioni della richiesta o della risposta di grandi dimensioni](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Pressione della memoria nel client Redis

Il numero eccessivo di richieste di memoria nel computer client causa problemi di prestazioni che possono ritardare l'elaborazione delle risposte dalla cache. Quando si verifica un utilizzo elevato di memoria, il sistema può eseguire il paging dei dati su disco. Questo _errore di pagina_ causa un rallentamento significativo del sistema.

Per rilevare le richieste di memoria nel client:

- Monitorare l'utilizzo della memoria nel computer per assicurarsi che non superi la memoria disponibile.
- Monitorare il contatore delle prestazioni `Page Faults/Sec` del client. Durante il normale funzionamento, la maggior parte dei sistemi presenta alcuni errori di pagina. I picchi di errori di pagina corrispondenti ai timeout della richiesta possono indicare un numero eccessivo di richieste di memoria.

Un elevato numero di richieste di memoria nel client può essere mitigato in diversi modi:

- Esaminare i modelli di utilizzo della memoria per ridurre il consumo di memoria nel client.
- Aggiornare la macchina virtuale client a dimensioni maggiori con una maggiore quantità di memoria.

## <a name="traffic-burst"></a>Traffico in sequenza

I burst di traffico combinati con impostazioni `ThreadPool` insufficienti possono causare ritardi nell'elaborazione dei dati già inviati dal server Redis, ma non ancora utilizzati sul lato client.

Monitorare il modo in cui le statistiche di `ThreadPool` cambiano nel tempo usando [un esempio `ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). È possibile usare `TimeoutException` messaggi di StackExchange. Redis come riportato di seguito per approfondire l'analisi:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Nell'eccezione precedente, esistono diversi problemi interessanti:

- Si noti che nella sezione `IOCP` e nella sezione `WORKER` è presente un valore `Busy` maggiore del valore `Min`. Questa differenza significa che è necessario modificare le impostazioni di `ThreadPool`.
- È anche possibile osservare `in: 64221` Questo valore indica che sono stati ricevuti 64.211 byte a livello di socket del kernel del client ma che non sono stati letti dall'applicazione. Questa differenza indica in genere che l'applicazione (ad esempio, StackExchange. Redis) non legge i dati dalla rete con la stessa velocità con cui il server lo invia all'utente.

È possibile [configurare le impostazioni di `ThreadPool`](https://gist.github.com/JonCole/e65411214030f0d823cb) per assicurarsi che il pool di thread venga scalato rapidamente in scenari con picchi.

## <a name="high-client-cpu-usage"></a>Utilizzo elevato della CPU client

Un utilizzo elevato della CPU client indica che il sistema non è in grado di gestire il lavoro richiesto. Anche se la cache ha inviato rapidamente la risposta, il client potrebbe non riuscire a elaborare la risposta in modo tempestivo.

Monitorare l'utilizzo della CPU a livello di sistema del client usando le metriche disponibili nell'portale di Azure o tramite i contatori delle prestazioni nel computer. Prestare attenzione a non monitorare la CPU del *processo* perché un singolo processo può avere un utilizzo ridotto della CPU, ma la CPU a livello di sistema può essere elevata. Cercare nell'utilizzo della CPU i picchi corrispondenti ai timeout. La CPU elevata può anche causare valori di `in: XXX` elevati nei messaggi di errore `TimeoutException`, come descritto nella sezione relativa al [traffico in sequenza](#traffic-burst) .

> [!NOTE]
> StackExchange.Redis 1.1.603 e versioni successive includono la metrica `local-cpu` nei messaggi di errore `TimeoutException`. Assicurarsi di usare la versione più recente del [pacchetto NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). È importante avere la versione più recente perché i bug del codice vengono continuamente fissati per renderlo più solido in caso di timeout.
>

Per attenuare l'utilizzo elevato della CPU di un client:

- Individuare la causa di picchi della CPU.
- Aggiornare il client a una macchina virtuale di dimensioni maggiori con più capacità di CPU.

## <a name="client-side-bandwidth-limitation"></a>Limitazione della larghezza di banda lato client

A seconda dell'architettura dei computer client, possono esserci limitazioni relative alla quantità di larghezza di banda di rete disponibile. Se il client supera la larghezza di banda disponibile sovraccaricando la capacità di rete, i dati non vengono elaborati sul lato client con la stessa velocità con cui il server lo invia. Questa situazione può causare timeout.

Monitorare le variazioni di utilizzo della larghezza di banda nel tempo usando [un esempio `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Non è possibile eseguire correttamente questo codice in alcuni ambienti con autorizzazioni con restrizioni, come Siti Web di Azure.

Per attenuare, ridurre l'utilizzo della larghezza di banda di rete o aumentare le dimensioni della macchina virtuale client con una maggiore capacità di rete.

## <a name="large-request-or-response-size"></a>Dimensioni della richiesta o della risposta di grandi dimensioni

Una richiesta/risposta di grandi dimensioni può causare un timeout. Si supponga, ad esempio, che il valore di timeout configurato nel client sia di 1 secondo. L'applicazione richiede due chiavi, ad esempio "A" e "B", nello stesso momento (con la stessa connessione di rete fisica). La maggior parte dei client supporta la richiesta "pipelining", dove entrambe le richieste "A" e "B" vengono inviate una dopo l'altra senza attendere le risposte. Il server invia le risposte nello stesso ordine. Se la risposta "A" è di grandi dimensioni, può consumare la maggior parte del timeout per le richieste successive.

Nell'esempio seguente, la richiesta "A" e "B" vengono inviate rapidamente al server. Il server inizia A inviare rapidamente le risposte "A" e "B". A causa dei tempi di trasferimento dei dati, la risposta ' B ' deve attendere il timeout della risposta ' A ' anche se il server ha risposto rapidamente.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Non è semplice misurare questa richiesta/risposta. È possibile instrumentare il codice client per tenere traccia di richieste e risposte di grandi dimensioni.

Le risoluzioni per le dimensioni di risposta di grandi dimensioni sono diverse ma includono:

1. Ottimizzare l'applicazione per un numero elevato di valori piccoli, anziché alcuni valori di grandi dimensioni.
    - La soluzione migliore consiste nel dividere i dati in valori più bassi correlati.
    - Vedere il post [Qual è l'intervallo di dimensioni dei valori ideale per Redis? 100 KB è troppo grande?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) per informazioni dettagliate sul motivo per cui si consiglia di usare valori più piccoli.
1. Aumentare le dimensioni della macchina virtuale per ottenere una maggiore capacità di larghezza di banda
    - Una maggiore larghezza di banda nella macchina virtuale client o server può ridurre i tempi di trasferimento dei dati per risposte di dimensioni maggiori.
    - Confrontare l'utilizzo di rete corrente in entrambi i computer con i limiti delle dimensioni correnti della VM. Una maggiore larghezza di banda solo sul server o solo sul client potrebbe non essere sufficiente.
1. Aumentare il numero di oggetti connessione utilizzati dall'applicazione.
    - Usare un approccio Round Robin per eseguire richieste su oggetti di connessione diversi.

## <a name="additional-information"></a>Informazioni aggiuntive

- [Risolvere i problemi del lato server di cache di Azure per il server Redis](cache-troubleshoot-server.md)
- [In che modo è possibile valutare e testare le prestazioni della cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
