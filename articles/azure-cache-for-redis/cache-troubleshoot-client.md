---
title: Risolvere i problemi relativi al lato client di cache di Azure per Redis
description: Informazioni su come risolvere i problemi comuni sul lato client con la cache di Azure per Redis, ad esempio la pressione della memoria del client Redis, il picchiditraffico, la CPU elevata, la larghezza di banda limitata, le richieste di grandi dimensioni o le dimensioni di risposta di grandi dimensioni.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: ace953fcb278604cb64eef463753f0f2622d3d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277947"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Risolvere i problemi relativi al lato client di cache di Azure per Redis

In questa sezione vengono illustrati i problemi che si verificano a causa di una condizione nel client Redis utilizzato dall'applicazione.

- [Pressione della memoria sul client Redis](#memory-pressure-on-redis-client)
- [Scoppia il traffico](#traffic-burst)
- [Utilizzo elevato della CPU client](#high-client-cpu-usage)
- [Limitazione della larghezza di banda lato client](#client-side-bandwidth-limitation)
- [Dimensioni di richiesta o risposta di grandi dimensioni](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Pressione della memoria sul client Redis

La pressione della memoria sul computer client comporta tutti i tipi di problemi di prestazioni che possono ritardare l'elaborazione delle risposte dalla cache. Quando si verifica una pressione della memoria, il sistema può paginare i dati su disco. Questo _errore di pagina_ causa un rallentamento significativo del sistema.

Per rilevare la pressione della memoria sul client:

- Monitorare l'utilizzo della memoria nel computer per assicurarsi che non superi la memoria disponibile.
- Monitorare il `Page Faults/Sec` contatore delle prestazioni del client. Durante il normale funzionamento, la maggior parte dei sistemi presentano alcuni errori di pagina. I picchi negli errori di pagina corrispondenti ai timeout delle richieste possono indicare una pressione della memoria.

La pressione elevata della memoria sul client può essere attenuata in diversi modi:

- Esaminare i modelli di utilizzo della memoria per ridurre il consumo di memoria nel client.
- Aggiornare la macchina virtuale client a una dimensione maggiore con più memoria.

## <a name="traffic-burst"></a>Scoppia il traffico

I burst di traffico combinati con impostazioni `ThreadPool` insufficienti possono causare ritardi nell'elaborazione dei dati già inviati dal server Redis, ma non ancora utilizzati sul lato client.

Monitorare `ThreadPool` come le statistiche cambiano nel tempo utilizzando [un esempio `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). È possibile `TimeoutException` utilizzare i messaggi da StackExchange.Redis come di seguito per analizzare ulteriormente:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Nell'eccezione precedente, ci sono diversi problemi che sono interessanti:

- Si noti che nella sezione `IOCP` e nella sezione `WORKER` è presente un valore `Busy` maggiore del valore `Min`. Questa differenza `ThreadPool` significa che le impostazioni devono essere regolate.
- È anche possibile osservare `in: 64221` Questo valore indica che sono stati ricevuti 64.211 byte a livello di socket del kernel del client ma non sono stati letti dall'applicazione. Questa differenza in genere significa che l'applicazione (ad esempio, StackExchange.Redis) non legge i dati dalla rete con la velocità con cui il server li invia all'utente.

È possibile [configurare le `ThreadPool` impostazioni](cache-faq.md#important-details-about-threadpool-growth) per assicurarsi che il pool di thread venga ridimensionato rapidamente in scenari di burst.

## <a name="high-client-cpu-usage"></a>Utilizzo elevato della CPU client

L'utilizzo elevato della CPU client indica che il sistema non riesce a tenere il passo con il lavoro che è stato chiesto di fare. Anche se la cache ha inviato la risposta rapidamente, il client potrebbe non riuscire a elaborare la risposta in modo tempestivo.

Monitorare l'utilizzo della CPU a livello di sistema del client usando le metriche disponibili nel portale di Azure o tramite i contatori delle prestazioni nel computer. Prestare attenzione a non monitorare la CPU del *processo* perché un singolo processo può avere un utilizzo ridotto della CPU, ma la CPU a livello di sistema può essere elevata. Cercare nell'utilizzo della CPU i picchi corrispondenti ai timeout. La CPU elevata `in: XXX` può `TimeoutException` anche causare valori elevati nei messaggi di errore, come descritto nella sezione Burst di [traffico.](#traffic-burst)

> [!NOTE]
> StackExchange.Redis 1.1.603 e versioni successive includono la metrica `local-cpu` nei messaggi di errore `TimeoutException`. Assicurarsi di usare la versione più recente del [pacchetto NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). È importante avere la versione più recente perché i bug del codice vengono continuamente fissati per renderlo più solido in caso di timeout.
>

Per ridurre l'utilizzo elevato della CPU di un client:

- Esaminare la causa dei picchi di CPU.
- Aggiornare il client a una dimensione della macchina virtuale maggiore con una maggiore capacità della CPU.

## <a name="client-side-bandwidth-limitation"></a>Limitazione della larghezza di banda lato client

A seconda dell'architettura dei computer client, possono esserci limitazioni relative alla quantità di larghezza di banda di rete disponibile. Se il client supera la larghezza di banda disponibile sovraccaricando la capacità di rete, i dati non vengono elaborati sul lato client con la stessa rapidità con cui vengono inviati dal server. Questa situazione può causare timeout.

Monitorare il modo in cui l'utilizzo della larghezza di banda cambia nel tempo utilizzando [un esempio `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Non è possibile eseguire correttamente questo codice in alcuni ambienti con autorizzazioni con restrizioni, come Siti Web di Azure.

Per ridurre, ridurre il consumo di larghezza di banda di rete o aumentare le dimensioni della macchina virtuale client a uno con più capacità di rete.

## <a name="large-request-or-response-size"></a>Dimensioni di richiesta o risposta di grandi dimensioni

Una richiesta/risposta di grandi dimensioni può causare un timeout. Si supponga ad esempio che il valore di timeout configurato nel client sia di 1 secondo. L'applicazione richiede due chiavi, ad esempio "A" e "B", nello stesso momento (con la stessa connessione di rete fisica). La maggior parte dei client supporta la richiesta "pipelining", in cui entrambe le richieste 'A' e 'B' vengono inviate una dopo l'altra senza attendere le relative risposte. Il server invia le risposte nello stesso ordine. Se la risposta 'A' è di grandi dimensioni, può eritare la maggior parte del timeout per le richieste successive.

Nell'esempio seguente, le richieste "A" e "B" vengono inviate rapidamente al server. Il server inizia a inviare rapidamente le risposte 'A' e 'B'. A causa dei tempi di trasferimento dei dati, la risposta 'B' deve attendere i tempi di risposta 'A' anche se il server ha risposto rapidamente.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Non è semplice misurare questa richiesta/risposta. È possibile instrumentare il codice client per tenere traccia di richieste e risposte di grandi dimensioni.

Le risoluzioni per le risposte di grandi dimensioni sono varie, ma includono:

1. Ottimizzare l'applicazione per un numero elevato di valori piccoli, anziché alcuni valori di grandi dimensioni.
    - La soluzione migliore consiste nel dividere i dati in valori più bassi correlati.
    - Vedi il post [Qual è l'intervallo di dimensioni del valore ideale per redis? 100 KB è troppo grande?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) per informazioni dettagliate sul motivo per cui sono consigliati valori più piccoli.
1. Aumentare le dimensioni della macchina virtuale per ottenere capacità di larghezza di banda più elevate
    - Una maggiore larghezza di banda nella macchina virtuale client o server può ridurre i tempi di trasferimento dei dati per risposte più grandi.
    - Confrontare l'utilizzo corrente della rete in entrambi i computer con i limiti delle dimensioni correnti della macchina virtuale. Una maggiore larghezza di banda solo sul server o solo sul client potrebbe non essere sufficiente.
1. Aumentare il numero di oggetti connessione utilizzati dall'applicazione.
    - Utilizzare un approccio round robin per effettuare richieste su oggetti connessione diversi.

## <a name="additional-information"></a>Informazioni aggiuntive

- [Risolvere i problemi del lato server di cache di Azure per Redis](cache-troubleshoot-server.md)
- [In che modo è possibile valutare e testare le prestazioni della cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
