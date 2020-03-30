---
title: Usare il ripartizionamento per ottimizzare i processi di Analisi di flusso di AzureUse repartitioning to optimize Azure Stream Analytics jobs
description: Questo articolo descrive come usare il ripartizionamento per ottimizzare i processi di Analisi di flusso di Azure che non possono essere parallelizzati.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732383"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Usare il ripartizionamento per ottimizzare l'elaborazione con Analisi di flusso di AzureUse repartitioning to optimize processing with Azure Stream Analytics

Questo articolo illustra come usare il ripartizionamento per ridimensionare la query di Analisi di flusso di Azure per scenari che non possono essere [completamente parallelizzati.](stream-analytics-scale-jobs.md)

Potrebbe non essere possibile usare la parallelizzazione se:You might not be able to use [parallelization](stream-analytics-parallelization.md) if:

* Non si controlla la chiave di partizione per il flusso di input.
* L'input di origine "spruzza" tra più partizioni che in seguito devono essere unite.

Il partizionamento, o rimescolamento, è necessario quando si elaborano dati in un flusso che non è partizionato in base a uno schema di input naturale, ad esempio **PartitionId** per gli hub eventi. Quando si esegue la ripartizione, ogni partizione può essere elaborata in modo indipendente, che consente di scalare in modo lineare la pipeline di streaming.

## <a name="how-to-repartition"></a>Come ripartizionare

Per ripartizionare, utilizzare la parola chiave **INTO** dopo un'istruzione **PARTITION BY** nella query. L'esempio seguente partiziona i dati in base a DeviceID in un numero di partizioni pari a 10.The following example partitions the data by **DeviceID** into a partition count of 10. L'hashing di **DeviceID** viene utilizzato per determinare quale partizione deve accettare quale flusso secondario. I dati vengono scaricati in modo indipendente per ogni flusso partizionato, presupponendo che l'output supporti le scritture partizionate e disponga di 10 partizioni.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

La query di esempio seguente unisce due flussi di dati ripartizionati. Quando si uniscono due flussi di dati ripartizionati, i flussi devono avere la stessa chiave di partizione e la stessa chiave di partizione. Il risultato è un flusso con lo stesso schema di partizione.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Lo schema di output deve corrispondere alla chiave e al conteggio dello schema di flusso in modo che ogni flusso secondario possa essere scaricato in modo indipendente. Il flusso potrebbe anche essere unito e ripartizionato da uno schema diverso prima dello svuotamento, ma è consigliabile evitare tale metodo perché aumenta la latenza generale dell'elaborazione e aumenta l'utilizzo delle risorse.

## <a name="streaming-units-for-repartitions"></a>Unità di streaming per ripartizioni

Sperimentare e osservare l'utilizzo delle risorse del processo per determinare il numero esatto di partizioni necessarie. Il numero di unità di [streaming (SU)](stream-analytics-streaming-unit-consumption.md) deve essere regolato in base alle risorse fisiche necessarie per ogni partizione. In generale, sono necessarie sei di gestione per ogni partizione. Se non sono presenti risorse sufficienti al processo, il sistema applicherà la ripartizione solo se ne trae vantaggio.

## <a name="repartitions-for-sql-output"></a>Ripartizioni per l'output SQLRepartitions for SQL output

Quando il processo usa il database SQL per l'output, usare il partizionamento esplicito in modo che corrisponda al numero ottimale di partizioni per ottimizzare la velocità effettiva. Poiché SQL funziona meglio con otto writer, il ripartizionamento del flusso a otto prima dello svuotamento o in un altro punto più a monte può migliorare le prestazioni del processo. 

Quando sono presenti più di 8 partizioni di input, ereditare l'input dello schema di partizione potrebbe non essere una scelta appropriata. È consigliabile usare [INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) nella query per specificare in modo esplicito il numero di writer di output. 

L'esempio seguente legge dall'input, indipendentemente dal fatto che venga partizionato naturalmente, e ripartiziona il flusso di dieci volte in base alla dimensione DeviceID e scarica i dati nell'output. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Per altre informazioni, consultare [Output di Analisi di flusso di Azure in Database SQL di Azure](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Passaggi successivi

* [Introduzione all'uso di Analisi di flusso di Azure](stream-analytics-introduction.md)
* [Sfruttare i vantaggi della parallelizzazione delle query in Analisi di flusso di Azure](stream-analytics-parallelization.md)
