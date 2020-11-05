---
title: Streaming dei dati in Azure SQL Edge
description: Informazioni sul flusso di dati in Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f63ab040e750c0c642c9656a5482529b926e9295
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392113"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Streaming dei dati in Azure SQL Edge

Azure SQL Edge fornisce un'implementazione nativa delle funzionalità di flusso di dati denominate streaming T-SQL. Fornisce il flusso di dati in tempo reale, l'analisi e l'elaborazione di eventi per analizzare ed elaborare volumi elevati di dati di streaming veloce da più origini, simultaneamente. Il flusso T-SQL viene creato usando lo stesso motore di streaming a prestazioni elevate che consente l' [analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md) in Microsoft Azure. La funzionalità supporta un set di funzionalità simile offerto da analisi di flusso di Azure in esecuzione sul perimetro.

Come con l'analisi di flusso, lo streaming T-SQL riconosce i modelli e le relazioni nelle informazioni estratte da un numero di origini di input internet, tra cui dispositivi, sensori e applicazioni. È possibile usare questi modelli per attivare azioni e avviare i flussi di lavoro. Ad esempio, è possibile creare avvisi, inserire informazioni in una soluzione di creazione di report o visualizzazione o archiviare i dati per un uso successivo. 

Streaming T-SQL consente di:

* Analisi di flussi di dati di telemetria in tempo reale da dispositivi IoT.
* Usa l'analisi in tempo reale dei dati generati da veicoli autonomi e autisti.
* Usa il monitoraggio remoto e la manutenzione predittiva di asset industriali o di produzione di valore elevato.
* Usare il rilevamento delle anomalie e il riconoscimento dei modelli delle letture dei sensori Internet in un'agricoltura o in una farm energetica.

## <a name="how-does-t-sql-streaming-work"></a>Come funziona lo streaming T-SQL?

Il flusso T-SQL funziona esattamente allo stesso modo di [analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md#how-does-stream-analytics-work). Ad esempio, usa il concetto di *processi* di streaming per l'elaborazione del flusso di dati in tempo reale. 

Un processo di analisi di flusso è costituito da:

- **Input di flusso** : definisce le connessioni a un'origine dati da cui leggere il flusso di dati. SQL Edge di Azure supporta attualmente i tipi di input di flusso seguenti:
    - Hub di Edge
    - Kafka (il supporto per gli input Kafka è attualmente disponibile solo nelle versioni Intel/AMD64 di Azure SQL Edge).

- **Output flusso** : definisce le connessioni a un'origine dati in cui scrivere il flusso di dati. SQL Edge di Azure supporta attualmente i tipi di output di flusso seguenti
    - Hub di Edge
    - SQL (l'output SQL può essere un database locale all'interno dell'istanza di Azure SQL Edge o un SQL Server remoto o un database SQL di Azure). 

- **Query di flusso** : definisce la trasformazione, le aggregazioni, il filtro, l'ordinamento e i join da applicare al flusso di input, prima che venga scritto nell'output del flusso. La query di flusso è basata sullo stesso linguaggio di query usato da analisi di flusso. Per altre informazioni, vedere [linguaggio di query di analisi di flusso](/stream-analytics-query/stream-analytics-query-language-reference).

> [!IMPORTANT]
> Lo streaming T-SQL, a differenza di analisi di flusso, attualmente non supporta l' [uso di dati di riferimento per le ricerche](../stream-analytics/stream-analytics-use-reference-data.md) o l'uso di funzioni definite dall' [utente e Uda in un processo di flusso](../stream-analytics/streaming-technologies.md#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> Il flusso T-SQL supporta solo un subset della superficie di attacco del linguaggio supportata da analisi di flusso. Per altre informazioni, vedere [linguaggio di query di analisi di flusso](/stream-analytics-query/stream-analytics-query-language-reference).

## <a name="limitations-and-restrictions"></a>Limitazioni e restrizioni

Per lo streaming T-SQL si applicano le limitazioni e le restrizioni seguenti. 

- Solo un processo di streaming può essere attivo in un momento specifico. Prima di avviare un altro processo, è necessario arrestare i processi già in esecuzione.
- Ogni esecuzione del processo di streaming è a thread singolo. Se il processo di streaming contiene più query, ogni query viene valutata in ordine seriale.
- Quando si arresta un processo di streaming in Azure SQL Edge, potrebbe verificarsi un certo ritardo prima che sia possibile avviare il processo di streaming successivo. Questo ritardo è stato introdotto perché il processo di streaming sottostante deve essere arrestato in risposta alla richiesta di arresto del processo e quindi riavviato in risposta alla richiesta di avvio del processo. 
- Streaming T-SQL fino a 32 partizioni per un flusso Kafka. Se si tenta di configurare un numero di partizioni superiore, verrà generato un errore. 

## <a name="next-steps"></a>Passaggi successivi

- [Creare un processo di analisi di flusso in Azure SQL Edge ](create-stream-analytics-job.md)
- [Visualizzazione dei metadati associati ai processi di flusso in Azure SQL Edge ](streaming-catalog-views.md)
- [Crea flusso esterno](create-external-stream-transact-sql.md)