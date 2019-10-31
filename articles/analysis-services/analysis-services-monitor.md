---
title: Monitorare le metriche dei server di Azure Analysis Services | Microsoft Docs
description: Informazioni su come monitorare le metriche dei server di Analysis Services nel portale di Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 57bcffad2634ea556cc0dd53763011f921acc5e0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146808"
---
# <a name="monitor-server-metrics"></a>Monitorare le metriche dei server

Analysis Services fornisce le metriche in Azure Esplora metriche, uno strumento gratuito nel portale, che consente di monitorare le prestazioni e l'integrità dei server. È possibile, ad esempio, monitorare la memoria e l'utilizzo di CPU, il numero di connessioni client ed eseguire query sull'utilizzo di risorse. Analysis Services usa lo stesso framework di monitoraggio usato dalla maggior parte degli altri servizi Azure. Per altre informazioni, vedere [Introduzione ad Azure Esplora metriche](../azure-monitor/platform/metrics-getting-started.md).

Per eseguire una diagnostica più approfondita, tenere traccia delle prestazioni, identificare le tendenze in più risorse del servizio in un gruppo di risorse o una sottoscrizione, usare [Monitoraggio di Azure](../azure-monitor/overview.md). Monitoraggio di Azure può generare un servizio fatturabile.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Per monitorare le metriche per un server Analysis Services

1. Nel portale di Azure selezionare **Metriche**.

    ![Monitoraggio nel portale di Azure](./media/analysis-services-monitor/aas-monitor-portal.png)

2. In **metrica**selezionare le metriche da includere nel grafico. 

    ![Grafico di monitoraggio](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Metriche del server

Usare questa tabella per determinare le metriche più adatte allo scenario di monitoraggio. Nello stesso grafico possono essere visualizzate solo metriche della stessa unità.

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Lunghezza coda processi nel pool di comandi|Conteggio|Media|Numero dei processi nella coda del pool dei thread dei comandi.|
|CurrentConnections|Connessione: connessioni correnti|Conteggio|Media|Numero corrente delle connessioni client stabilite.|
|CurrentUserSessions|Sessioni utente correnti|Conteggio|Media|Numero corrente di sessioni utente attive.|
|mashup_engine_memory_metric|Memoria Motore M|Byte|Media|Utilizzo della memoria da parte dei processi del motore mashup|
|mashup_engine_qpu_metric|QPU Motore M|Conteggio|Media|Utilizzo QPU da parte dei processi motore mashup|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo 0-25 GB per S1, 0-50 GB per S2 e 0-100 GB per S4|
|memory_thrashing_metric|Thrashing di memoria|Percentuale|Media|Thrashing di memoria medio.|
|CleanerCurrentPrice|Memoria: prezzo corrente pulitura memoria|Conteggio|Media|Prezzo corrente della memoria, in €/byte/tempo, moltiplicato per 1000.|
|CleanerMemoryNonshrinkable|Memoria: pulitura memoria non compattabile|Byte|Media|Quantità di memoria, in byte, non soggetta a compattazione da parte della pulitura della memoria in background.|
|CleanerMemoryShrinkable|Memoria: pulitura memoria compattabile|Byte|Media|Quantità di memoria, in byte, soggetta a compattazione da parte della pulitura della memoria in background.|
|MemoryLimitHard|Memoria: limite memoria assoluto|Byte|Media|Limite assoluto della memoria, dal file di configurazione.|
|MemoryLimitHigh|Memoria: limite memoria massimo|Byte|Media|Limite superiore della memoria, dal file di configurazione.|
|MemoryLimitLow|Memoria: limite memoria minimo|Byte|Media|Limite inferiore della memoria, dal file di configurazione.|
|MemoryLimitVertiPaq|Memoria: limite memoria VertiPaq|Byte|Media|Limite in memoria dal file di configurazione.|
|MemoryUsage|Memoria: utilizzo memoria|Byte|Media|Utilizzo della memoria del processo server utilizzato per il calcolo del prezzo di una memoria più pulita. Equivale al contatore Process\PrivateBytes più le dimensioni dei dati con mapping in memoria, ignorando la memoria con mapping o allocazione eseguita dal motore di analisi in memoria (VertiPaq) in eccesso rispetto al limite di memoria del motore.|
|private_bytes_metric|Private Bytes |Byte|Media|Quantità totale di memoria allocata dal processo del motore Analysis Services e dai processi del contenitore mashup, esclusa la memoria condivisa con altri processi.|
|virtual_bytes_metric|Virtual Bytes |Byte|Media|Dimensioni correnti dello spazio degli indirizzi virtuali usato dal processo del motore Analysis Services e dai processi del contenitore mashup.|
|mashup_engine_private_bytes_metric|Byte privati motore M |Byte|Media|La quantità totale di processi del contenitore mashup di memoria è stata allocata, esclusa la memoria condivisa con altri processi.|
|mashup_engine_virtual_bytes_metric|Byte virtuali del motore M |Byte|Media|Dimensioni correnti dello spazio degli indirizzi virtuali che i processi del contenitore mashup usano.|
|Quota|Memoria: quota|Byte|Media|Quota di memoria corrente, in byte. Le quote di memoria sono note anche come concessioni di memoria o prenotazioni di memoria.|
|QuotaBlocked|Memoria: Richieste di quota bloccate|Conteggio|Media|Numero corrente di richieste di quota bloccate fino a quando non vengono liberate altre quote di memoria.|
|VertiPaqNonpaged|Memoria: VertiPaq non di paging|Byte|Media|Byte di memoria bloccata nel working set per l'uso da parte del motore in memoria.|
|VertiPaqPaged|Memoria: VertiPaq di paging|Byte|Media|Byte di memoria di paging in uso per i dati in memoria.|
|ProcessingPoolJobQueueLength|Lunghezza coda processi nel pool di elaborazione|Conteggio|Media|Numero dei processi non di I/O nella coda del pool dei thread di elaborazione.|
|RowsConvertedPerSec|Elaborazione: righe convertite al secondo|Conteggio al secondo|Media|Velocità di conversione delle righe durante l'elaborazione.|
|RowsReadPerSec|Elaborazione: righe lette al secondo|Conteggio al secondo|Media|Velocità di lettura delle righe da tutti i database relazionali.|
|RowsWrittenPerSec|Elaborazione: righe scritte al secondo|Conteggio al secondo|Media|Velocità di scrittura delle righe durante l'elaborazione.|
|qpu_metric|QPU|Conteggio|Media|QPU. Intervallo 0-100 per S1, 0-200 per S2 e 0-400 per S4|
|QueryPoolBusyThreads|Thread occupati pool di query|Conteggio|Media|Numero dei thread occupati nel pool dei thread di query.|
|SuccessfullConnectionsPerSec|Connessioni riuscite al secondo|Conteggio al secondo|Media|Numero delle connessioni completate correttamente al secondo.|
|CommandPoolBusyThreads|Thread: thread occupati nel pool di comandi|Conteggio|Media|Numero dei thread occupati nel pool dei thread dei comandi.|
|CommandPoolIdleThreads|Thread: Thread inattivi pool di comandi|Conteggio|Media|Numero dei thread inattivi nel pool dei thread dei comandi.|
|LongParsingBusyThreads|Thread: thread occupati per analisi dei thread lunghi|Conteggio|Media|Numero dei thread occupati nel pool dei thread per l'analisi dei thread lunghi.|
|LongParsingIdleThreads|Thread: Thread inattivi per analisi dei thread lunghi|Conteggio|Media|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread lunghi.|
|LongParsingJobQueueLength|Thread: lunghezza coda processi di analisi dei thread lunghi|Conteggio|Media|Numero dei processi nella coda del pool dei thread per l'analisi dei thread lunghi.|
|ProcessingPoolIOJobQueueLength|Thread: lunghezza coda processi di I/O nel pool di elaborazione|Conteggio|Media|Numero di processi di I/O nella coda del pool dei thread di elaborazione.|
|ProcessingPoolBusyIOJobThreads|Thread: thread di processi di I/O occupati nel pool di elaborazione|Conteggio|Media|Numero di thread che eseguono processi di I/O nel pool dei thread di elaborazione.|
|ProcessingPoolBusyNonIOThreads|Thread: Thread non di I/O occupati nel pool di elaborazione|Conteggio|Media|Numero dei thread che eseguono processi non di I/O nel pool dei thread di elaborazione.|
|ProcessingPoolIdleIOJobThreads|Thread: thread di processi di I/O inattivi nel pool di elaborazione|Conteggio|Media|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|
|ProcessingPoolIdleNonIOThreads|Thread: thread non di I/O inattivi nel pool di elaborazione|Conteggio|Media|Numero di thread inattivi nel pool dei thread di elaborazione dedicato a processi non di I/O.|
|QueryPoolIdleThreads|Thread: thread inattivi nel pool di query|Conteggio|Media|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|
|QueryPoolJobQueueLength|Thread: lunghezza della coda processi nel pool di query|Conteggio|Media|Numero dei processi nella coda del pool dei thread di query.|
|ShortParsingBusyThreads|Thread: thread occupati per analisi dei thread brevi|Conteggio|Media|Numero dei thread occupati nel pool dei thread per l'analisi dei thread brevi.|
|ShortParsingIdleThreads|Thread: thread inattivi per analisi dei thread brevi|Conteggio|Media|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread brevi.|
|ShortParsingJobQueueLength|Thread: lunghezza coda processi di analisi dei thread brevi|Conteggio|Media|Numero dei processi nella coda del pool dei thread per l'analisi dei thread brevi.|
|TotalConnectionFailures|Numero totale di errori di connessione|Conteggio|Media|Numero totale dei tentativi di connessione non riusciti.|
|TotalConnectionRequests|Numero totale di richieste di connessione|Conteggio|Media|Numero totale delle richieste di connessione |

## <a name="next-steps"></a>Passaggi successivi
[Panoramica di monitoraggio di Azure](../azure-monitor/overview.md)      
[Introduzione ad Azure Esplora metriche](../azure-monitor/platform/metrics-getting-started.md)      
[Metriche nell'API REST di Monitoraggio di Azure](/rest/api/monitor/metrics)
