---
title: Usare il partizionamento per ottimizzare i processi di analisi di flusso di Azure
description: Questo articolo descrive come usare il ripartizionamento per ottimizzare i processi di analisi di flusso di Azure che non possono essere paralleli.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: bbea71464e8a1f4e93e510106d372257f155b0c6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935057"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Usare il partizionamento per ottimizzare l'elaborazione con analisi di flusso di Azure

Questo articolo illustra come usare il partizionamento per ridimensionare la query di analisi di flusso di Azure per scenari che non possono essere completamente [paralleli](stream-analytics-scale-jobs.md).

Potrebbe non essere possibile usare la [parallelizzazione](stream-analytics-parallelization.md) se:

* Non si controlla la chiave di partizione per il flusso di input.
* L'origine "spruzza" input tra più partizioni che successivamente devono essere unite. 

## <a name="how-to-repartition"></a>Come ripartizionare

Il partizionamento o il rimescolamento è necessario quando si elaborano i dati in un flusso non partizionato in base a uno schema di input naturale, ad esempio **PartitionID** per hub eventi. Quando si esegue la ripartizione, ogni partizione può essere elaborata in modo indipendente, che consente di scalare in modo lineare la pipeline di streaming.

Per eseguire la ripartizione, utilizzare la parola chiave **in** dopo un'istruzione **Partition by** nella query. Nell'esempio seguente i dati vengono partizionati per **DeviceID** in un numero di partizioni pari a 10. L'hashing di **DeviceID** viene usato per determinare quale partizione deve accettare il sottoflusso. I dati vengono scaricati in modo indipendente per ogni flusso partizionato, supponendo che l'output supporti le Scritture partizionate e disponga di 10 partizioni.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

La query di esempio seguente unisce due flussi di dati ripartizionati. Quando si uniscono due flussi di dati ripartizionati, i flussi devono avere la stessa chiave di partizione e il numero. Il risultato è un flusso con lo stesso schema di partizione.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Lo schema di output deve corrispondere alla chiave dello schema del flusso e al conteggio in modo che ogni sottoflusso possa essere svuotato in modo indipendente. Il flusso può anche essere Unito e ripartizionato di nuovo in base a uno schema diverso prima dello scaricamento, ma è consigliabile evitare tale metodo perché aggiunge alla latenza generale dell'elaborazione e aumenta l'utilizzo delle risorse.

## <a name="streaming-units-for-repartitions"></a>Unità di streaming per le ripartizionamenti

Sperimentare e osservare l'utilizzo delle risorse del processo per determinare il numero esatto di partizioni necessarie. Il numero di [unità di streaming (su)](stream-analytics-streaming-unit-consumption.md) deve essere regolato in base alle risorse fisiche necessarie per ogni partizione. In generale, sono necessari sei SUs per ogni partizione. Se al processo sono assegnate risorse insufficienti, il sistema applicherà la ripartizione solo se il processo è vantaggioso.

## <a name="repartitions-for-sql-output"></a>Ripartizionamenti per l'output SQL

Quando il processo utilizza il database SQL per l'output, utilizzare il partizionamento esplicito in modo che corrisponda al numero di partizioni ottimale per ottimizzare la velocità effettiva. Poiché SQL funziona al meglio con otto writer, il partizionamento del flusso a otto prima dello scaricamento o in un altro upstream può trarre vantaggio dalle prestazioni dei processi. 

Quando sono presenti più di 8 partizioni di input, ereditare l'input dello schema di partizione potrebbe non essere una scelta appropriata. Si consiglia di usare [into](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) nella query per specificare in modo esplicito il numero di writer di output. 

Nell'esempio seguente viene letta dall'input, indipendentemente dal fatto che sia partizionata in modo naturale e il flusso viene ripartizionato di dieci volte in base alla dimensione DeviceID e vengono scaricati i dati nell'output. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Per altre informazioni, consultare [Output di Analisi di flusso di Azure in Database SQL di Azure](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Passaggi successivi

* [Introduzione all'uso di Analisi di flusso di Azure](stream-analytics-introduction.md)
* [Sfruttare la parallelizzazione delle query in analisi di flusso di Azure](stream-analytics-parallelization.md)
