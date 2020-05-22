---
title: Streaming dei dati in SQL Edge di Azure (anteprima)
description: Informazioni sullo streaming dei dati in SQL Edge di Azure (anteprima)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 15b6b80f9924b050b388d74adc1d67db6a386134
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594510"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Streaming dei dati in SQL Edge di Azure (anteprima)

SQL Edge di Azure (anteprima) offre due opzioni diverse per implementare lo streaming dei dati. 

1. Distribuzione dei processi Edge di Analisi di flusso di Azure creati in Azure. Per altre informazioni su come distribuire i processi Edge di Analisi di flusso di Azure in SQL Edge di Azure, vedere [Distribuire i processi di Analisi di flusso di Azure](deploy-dacpac.md).
2. Uso della nuova funzionalità **Streaming T-SQL** per creare processi di streaming in SQL Edge, senza la necessità di configurare processi di streaming in Azure. 

Sebbene sia possibile usare entrambe le opzioni per implementare lo streaming dei dati in SQL Edge, è consigliabile usarne una sola. Quando si usano entrambe, è possibile che si verifichino situazioni di race condition che influiscono sul funzionamento delle operazioni di streaming dei dati.

La parte restante di questo documento si riferisce alla nuova funzionalità **Streaming T-SQL**, che offre streaming dei dati, analisi ed elaborazione di eventi in tempo reale per analizzare ed elaborare volumi elevati di dati di streaming veloci da più origini contemporaneamente. La funzionalità *Streaming T-SQL* è progettata con lo stesso motore di streaming a prestazioni elevate alla base di [Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) in Microsoft Azure e supporta un set di funzionalità simile offerto da Analisi di flusso di Azure in esecuzione sul perimetro.

Come con Analisi di flusso di Azure, Streaming T-SQL consente il riconoscimento di modelli e relazioni nelle informazioni estratte da numerose origini di input IoT, tra cui dispositivi, sensori e applicazioni. Questi modelli possono essere usati per attivare azioni e avviare flussi di lavoro, ad esempio la creazione di avvisi, l'invio di informazioni a una soluzione per la creazione di report o la visualizzazione oppure l'archiviazione dei dati per un uso successivo. 

Gli scenari seguenti sono esempi di quando è possibile usare Streaming T-SQL:

* Analisi di flussi di dati di telemetria in tempo reale da dispositivi IoT.
* Analisi in tempo reale dei dati generati da veicoli autonomi e senza autista.
* Monitoraggio remoto e manutenzione predittiva di asset industriali o di produzione di valore elevato.
* Rilevamento delle anomalie e/o riconoscimento dei modelli delle letture dei sensori IoT in un'azienda agricola o in una farm energetica.

## <a name="how-does-t-sql-streaming-work"></a>Come funziona Streaming T-SQL?

Streaming T-SQL funziona esattamente come [Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work). Ad esempio, usa il concetto di processi di streaming per l'elaborazione di flussi di dati in tempo reale. 

Un processo di analisi di flusso è costituito da:

- Input di flusso - L'input di flusso definisce le connessioni a un'origine dati da cui leggere il flusso di dati. SQL Edge di Azure supporta attualmente i tipi di input di flusso seguenti:
    - Hub di Edge
    - Kafka - Il supporto per input Kafka è attualmente disponibile solo nelle versioni Intel/AMD64 di SQL Edge di Azure.

- Output di flusso - L'output di flusso definisce le connessioni a un'origine dati in cui scrivere il flusso di dati. SQL Edge di Azure supporta attualmente i tipi di output di flusso seguenti
    - Hub di Edge
    - SQL - L'output SQL può essere un database locale all'interno dell'istanza di SQL Edge o di un'istanza remota di SQL Server o di un database SQL di Azure. 
    - Archiviazione BLOB di Azure

- Query di flusso - La query di flusso definisce la trasformazione, le aggregazioni, il filtro, l'ordinamento e i join che devono essere applicati al flusso di input prima di essere scritto nell'output del flusso. La query di flusso è basata sullo stesso linguaggio di query usato da Analisi di flusso di Azure. Per altre informazioni sul linguaggio di query di Analisi di flusso di Azure, vedere [Linguaggio di query di Analisi di flusso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> Streaming T-SQL, diversamente da Analisi di flusso di Azure, attualmente non supporta l'[uso di dati di riferimento per le ricerche](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) o l'[uso di funzioni definite dall'utente e aggregazioni definite dall'utente nel processo di streaming](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> Streaming T-SQL supporta solo un subset della superficie del linguaggio supportata da Analisi di flusso di Azure. Per altre informazioni sul linguaggio di query di Analisi di flusso di Azure, vedere [Linguaggio di query di Analisi di flusso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Limitazioni e restrizioni

A Streaming T-SQL si applicano le limitazioni e restrizioni seguenti. 

- Solo un processo di streaming può essere attivo in un determinato momento. Prima di avviare un altro processo, è necessario arrestare i processi già in esecuzione.
- Ogni esecuzione di processo di streaming è a thread singolo. Se il processo di streaming contiene più query, ogni query verrà valutata in ordine seriale.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un processo di analisi di flusso in SQL Edge di Azure (anteprima) ](create-stream-analytics-job.md)
- [Visualizzare i metadati associati ai processi di streaming in SQL Edge di Azure (anteprima) ](streaming-catalog-views.md)
- [Creare un flusso esterno](create-external-stream-transact-sql.md)