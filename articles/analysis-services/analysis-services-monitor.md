---
title: Monitorare le metriche dei server di Azure Analysis Services | Microsoft Docs
description: Informazioni su come monitorare le metriche dei server di Analysis Services nel portale di Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/14/2017
ms.author: owend
ms.openlocfilehash: f9b32029f0a7065fff73ddb6417fc5c1c7e658a5
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="monitor-server-metrics"></a>Monitorare le metriche dei server

Analysis Services fornisce le metriche che consentono di monitorare le prestazioni e l'integrità dei server. È possibile, ad esempio, monitorare la memoria e l'utilizzo di CPU, il numero di connessioni client ed eseguire query sull'utilizzo di risorse. Analysis Services usa lo stesso framework di monitoraggio usato dalla maggior parte degli altri servizi Azure. Per altre informazioni, vedere [Metriche in Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Per eseguire una diagnostica più approfondita, tenere traccia delle prestazioni, identificare le tendenze in più risorse del servizio in un gruppo di risorse o una sottoscrizione, usare [Monitoraggio di Azure](https://azure.microsoft.com/services/monitor/). Monitoraggio di Azure può generare un servizio fatturabile.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Per monitorare le metriche per un server Analysis Services

1. Nel portale di Azure selezionare **Metriche**.

    ![Monitoraggio nel portale di Azure](./media/analysis-services-monitor/aas-monitor-portal.png)

2. In **Metriche disponibili** selezionare le metriche da includere nel grafico. 

    ![Grafico di monitoraggio](./media/analysis-services-monitor/aas-monitor-chart.png)

## <a name="server-metrics"></a>Metriche del server
Usare questa tabella per determinare le metriche più adatte allo scenario di monitoraggio. Nello stesso grafico possono essere visualizzate solo metriche della stessa unità.

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Lunghezza coda processi nel pool di comandi|Numero|Media|Numero dei processi nella coda del pool dei thread dei comandi.|
|CurrentConnections|Connessione: connessioni correnti|Numero|Media|Numero corrente delle connessioni client stabilite.|
|CurrentUserSessions|Sessioni utente correnti|Numero|Media|Numero corrente di sessioni utente attive.|
|mashup_engine_memory_metric|Memoria Motore M|Byte|Media|Utilizzo della memoria da parte dei processi del motore mashup|
|mashup_engine_qpu_metric|QPU Motore M|Numero|Media|Utilizzo QPU da parte dei processi motore mashup|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo 0-25 GB per S1, 0-50 GB per S2 e 0-100 GB per S4|
|memory_thrashing_metric|Thrashing di memoria|Percentuale|Media|Thrashing di memoria medio.|
|CleanerCurrentPrice|Memoria: prezzo corrente pulitura memoria|Numero|Media|Prezzo corrente della memoria, in €/byte/tempo, moltiplicato per 1000.|
|CleanerMemoryNonshrinkable|Memoria: pulitura memoria non compattabile|Byte|Media|Quantità di memoria, in byte, non soggetta a compattazione da parte della pulitura della memoria in background.|
|CleanerMemoryShrinkable|Memoria: pulitura memoria compattabile|Byte|Media|Quantità di memoria, in byte, soggetta a compattazione da parte della pulitura della memoria in background.|
|MemoryLimitHard|Memoria: limite memoria assoluto|Byte|Media|Limite assoluto della memoria, dal file di configurazione.|
|MemoryLimitHigh|Memoria: limite memoria massimo|Byte|Media|Limite superiore della memoria, dal file di configurazione.|
|MemoryLimitLow|Memoria: limite memoria minimo|Byte|Media|Limite inferiore della memoria, dal file di configurazione.|
|MemoryLimitVertiPaq|Memoria: limite memoria VertiPaq|Byte|Media|Limite in memoria dal file di configurazione.|
|MemoryUsage|Memoria: utilizzo memoria|Byte|Media|Utilizzo della memoria del processo server utilizzato per il calcolo del prezzo di una memoria più pulita. Equivale al contatore Process\PrivateBytes più le dimensioni dei dati con mapping in memoria, ignorando la memoria con mapping o allocazione eseguita dal motore di analisi in memoria (VertiPaq) in eccesso rispetto al limite di memoria del motore.|
|Quota|Memoria: quota|Byte|Media|Quota di memoria corrente, in byte. Le quote di memoria sono note anche come concessioni di memoria o prenotazioni di memoria.|
|QuotaBlocked|Memoria: Richieste di quota bloccate|Numero|Media|Numero corrente di richieste di quota bloccate fino a quando non vengono liberate altre quote di memoria.|
|VertiPaqNonpaged|Memoria: VertiPaq non di paging|Byte|Media|Byte di memoria bloccata nel working set per l'uso da parte del motore in memoria.|
|VertiPaqPaged|Memoria: VertiPaq di paging|Byte|Media|Byte di memoria di paging in uso per i dati in memoria.|
|ProcessingPoolJobQueueLength|Lunghezza coda processi nel pool di elaborazione|Numero|Media|Numero dei processi non di I/O nella coda del pool dei thread di elaborazione.|
|RowsConvertedPerSec|Elaborazione: righe convertite al secondo|Conteggio al secondo|Media|Velocità di conversione delle righe durante l'elaborazione.|
|RowsReadPerSec|Elaborazione: righe lette al secondo|Conteggio al secondo|Media|Velocità di lettura delle righe da tutti i database relazionali.|
|RowsWrittenPerSec|Elaborazione: righe scritte al secondo|Conteggio al secondo|Media|Velocità di scrittura delle righe durante l'elaborazione.|
|qpu_metric|QPU|Numero|Media|QPU. Intervallo 0-100 per S1, 0-200 per S2 e 0-400 per S4|
|QueryPoolBusyThreads|Thread occupati pool di query|Numero|Media|Numero dei thread occupati nel pool dei thread di query.|
|SuccessfullConnectionsPerSec|Connessioni riuscite al secondo|Conteggio al secondo|Media|Numero delle connessioni completate correttamente al secondo.|
|CommandPoolBusyThreads|Thread: thread occupati nel pool di comandi|Numero|Media|Numero dei thread occupati nel pool dei thread dei comandi.|
|CommandPoolIdleThreads|Thread: Thread inattivi pool di comandi|Numero|Media|Numero dei thread inattivi nel pool dei thread dei comandi.|
|LongParsingBusyThreads|Thread: thread occupati per analisi dei thread lunghi|Numero|Media|Numero dei thread occupati nel pool dei thread per l'analisi dei thread lunghi.|
|LongParsingIdleThreads|Thread: Thread inattivi per analisi dei thread lunghi|Numero|Media|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread lunghi.|
|LongParsingJobQueueLength|Thread: lunghezza coda processi di analisi dei thread lunghi|Numero|Media|Numero dei processi nella coda del pool dei thread per l'analisi dei thread lunghi.|
|ProcessingPoolIOJobQueueLength|Thread: lunghezza coda processi di I/O nel pool di elaborazione|Numero|Media|Numero di processi di I/O nella coda del pool dei thread di elaborazione.|
|ProcessingPoolBusyIOJobThreads|Thread: thread di processi di I/O occupati nel pool di elaborazione|Numero|Media|Numero di thread che eseguono processi di I/O nel pool dei thread di elaborazione.|
|ProcessingPoolBusyNonIOThreads|Thread: Thread non di I/O occupati nel pool di elaborazione|Numero|Media|Numero dei thread che eseguono processi non di I/O nel pool dei thread di elaborazione.|
|ProcessingPoolIdleIOJobThreads|Thread: thread di processi di I/O inattivi nel pool di elaborazione|Numero|Media|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|
|ProcessingPoolIdleNonIOThreads|Thread: thread non di I/O inattivi nel pool di elaborazione|Numero|Media|Numero di thread inattivi nel pool dei thread di elaborazione dedicato a processi non di I/O.|
|QueryPoolIdleThreads|Thread: thread inattivi nel pool di query|Numero|Media|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|
|QueryPoolJobQueueLength|Thread: lunghezza della coda processi nel pool di query|Numero|Media|Numero dei processi nella coda del pool dei thread di query.|
|ShortParsingBusyThreads|Thread: thread occupati per analisi dei thread brevi|Numero|Media|Numero dei thread occupati nel pool dei thread per l'analisi dei thread brevi.|
|ShortParsingIdleThreads|Thread: thread inattivi per analisi dei thread brevi|Numero|Media|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread brevi.|
|ShortParsingJobQueueLength|Thread: lunghezza coda processi di analisi dei thread brevi|Numero|Media|Numero dei processi nella coda del pool dei thread per l'analisi dei thread brevi.|
|TotalConnectionFailures|Numero totale di errori di connessione|Numero|Media|Numero totale dei tentativi di connessione non riusciti.|
|TotalConnectionRequests|Numero totale di richieste di connessione|Numero|Media|Numero totale delle richieste di connessione |

## <a name="next-steps"></a>Passaggi successivi
[Monitoraggio in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Metriche in Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Metriche nell'API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931930.aspx)