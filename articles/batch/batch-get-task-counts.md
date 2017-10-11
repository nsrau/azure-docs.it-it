---
title: "Monitorare lo stato di un processo conteggiando le attività in base allo stato - Azure Batch | Microsoft Docs"
description: "Monitorare lo stato di un processo chiamando l'operazione di recupero del conteggio delle attività per conteggiare le attività per ogni processo. È possibile ottenere un conteggio delle attività attive, in esecuzione e completate e delle attività riuscite o non riuscite."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/02/2017
ms.author: tamram
ms.openlocfilehash: ceff59d7063b60a1344a47489d3d73e0e8ee07df
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="count-tasks-by-state-to-monitor-a-jobs-progress-preview"></a>Conteggiare le attività in base allo stato per monitorare lo stato di un processo (anteprima)

Azure Batch offre un pratico metodo per monitorare lo stato di un processo mentre questo esegue le proprie attività. È possibile chiamare l'operazione di [recupero dei conteggi delle attività][rest_get_task_counts] per determinare quante attività hanno stato attivo, in esecuzione o completato e quante sono riuscite o non riuscite. Conteggiando il numero di attività in ogni stato, è possibile visualizzare più facilmente lo stato del processo a un utente o rilevare ritardi o errori imprevisti che possono avere impatto sul processo.

> [!IMPORTANT]
> L'operazione di recupero dei conteggi delle attività è attualmente in versione di anteprima e non è ancora disponibile in Azure per enti pubblici, Azure Cina e Azure Germania. 
>
>

## <a name="how-tasks-are-counted"></a>Come vengono conteggiate le attività

L'operazione di recupero dei conteggi delle attività conta le attività in base allo stato, nel modo seguente:

- Un'attività viene conteggiata come **attiva** quando è accodata e può essere eseguita, ma non è attualmente assegnata a un nodo di calcolo. Un'attività viene conteggiata come **attiva** anche se dipende da un'attività padre non ancora completata. Per altre informazioni sulle dipendenze delle attività, vedere [Creare relazioni tra attività per eseguire attività che dipendono da altre attività](batch-task-dependencies.md). 
- Un'attività viene conteggiata come **in esecuzione** quando è stata assegnata a un nodo di calcolo, ma non è ancora completata. Un'attività viene conteggiata come **in esecuzione** quando il suo stato è `preparing` o `running`, come indicato dall'operazione di [recupero delle informazioni su un'attività][rest_get_task].
- Un'attività viene conteggiata come **completata** quando non è più idonea per l'esecuzione. Un'attività conteggiata come **completata** è stata in genere completata correttamente o non è riuscita e ha anche superato il limite di nuovi tentativi. 

L'operazione di recupero dei conteggi delle attività indica anche quante attività sono riuscite o non sono riuscite. Batch determina se un'attività è riuscita o meno controllando la proprietà **result** della proprietà [executionInfo][https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task#executionInfo]:

    - Un'attività viene conteggiata come **riuscita** se il risultato dell'esecuzione dell'attività è `success`.
    - Un'attività viene conteggiata come **non riuscita** se il risultato dell'esecuzione dell'attività è `failure`.

Per altre informazioni sugli stati delle attività, vedere [Get information about a task][rest_get_task] (Ottenere informazioni su un'attività).

L'esempio di codice .NET seguente mostra come recuperare i conteggi delle attività in base allo stato: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> Per ottenere i conteggi delle attività per un processo, è possibile usare un criterio simile per REST e per altri linguaggi supportati. 
> 
> 

## <a name="consistency-checking-for-task-counts"></a>Verifica della coerenza per i conteggi delle attività

Il servizio Batch aggrega i conteggi delle attività raccogliendo dati da più parti di un sistema distribuito asincrono. Per garantire che i conteggi delle attività siano corretti, Batch offre un'ulteriore convalida per i conteggi in base allo stato tramite verifiche di coerenza su più componenti del sistema. Batch esegue queste verifiche di coerenza se il processo include meno di 200.000 attività. Nell'improbabile caso che la verifica di coerenza trovi errori, Batch corregge il risultato dell'operazione di recupero dei conteggi delle attività in base ai risultati della verifica di coerenza. La verifica di coerenza è una misura aggiuntiva per garantire che i clienti che usano l'operazione di recupero dei conteggi delle attività ricevano le informazioni corrette necessarie per la propria soluzione.

La proprietà **validationStatus** nella risposta indica se Batch ha eseguito la verifica di coerenza. Se Batch non è stato in grado di verificare i conteggi in base allo stato rispetto agli stati effettivi contenuti nel sistema, la proprietà **validationStatus** è impostata su `unvalidated`. Poiché per motivi di prestazioni Batch non esegue la verifica di coerenza se il processo include più di 200.000 attività, la proprietà **validationStatus** potrebbe essere impostata su `unvalidated` in questo caso. Tuttavia, il conteggio delle attività non è necessariamente errato in questo caso, perché anche una perdita di dati molto limitata è altamente improbabile. 

Quando un'attività cambia stato, la pipeline di aggregazione elabora la modifica in pochi secondi. L'operazione di recupero dei conteggi delle attività riflette i conteggi delle attività aggiornati in questo periodo di tempo. Tuttavia, se la pipeline di aggregazione non rileva una modifica nello stato di un'attività, la modifica non viene registrata fino al successivo passaggio di convalida. Durante questo periodo di tempo i conteggi delle attività possono essere leggermente imprecisi a causa dell'evento ignorato, ma verranno corretti nel successivo passaggio di convalida.

## <a name="best-practices-for-counting-a-jobs-tasks"></a>Procedure consigliate per il conteggio della attività di un processo

La chiamata dell'operazione di recupero dei conteggi delle attività è il modo più efficiente per restituire un conteggio di base delle attività di un processo in base allo stato. Se si usa il servizio Batch versione 2017-06-01.5.1, è consigliabile scrivere o aggiornare il codice in modo da usare l'operazione di recupero dei conteggi delle attività.

Questa operazione non è disponibile nelle versioni del servizio Batch precedenti alla 2017-06-01.5.1. Se si usa una versione meno recente del servizio, usare una query di tipo elenco per conteggiare le attività in un processo. Per altre informazioni, vedere [Creare query per elencare le risorse di Batch in modo efficiente](batch-efficient-list-queries.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui concetti e sulle funzionalità del servizio Batch, vedere [Panoramica delle funzionalità di Batch](batch-api-basics.md). L'articolo descrive le risorse principali di Batch, tra cui pool, nodi di calcolo, processi e attività, e include una panoramica delle funzionalità del servizio.
* Apprendere le nozioni di base dello sviluppo di un'applicazione abilitata per Batch con la [libreria client Batch .NET](batch-dotnet-get-started.md) o con [Python](batch-python-tutorial.md). Gli articoli introduttivi mostrano un'applicazione funzionante che usa il servizio Batch per eseguire un carico di lavoro in più nodi di calcolo.


[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_task]: https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task
[rest_list_tasks]: https://docs.microsoft.com/rest/api/batchservice/list-the-tasks-associated-with-a-job
