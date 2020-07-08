---
title: Progettare query di elenco efficienti
description: Migliorare le prestazioni filtrando le query quando si chiedono informazioni su risorse di Batch, ad esempio pool, processi, attività e nodi di calcolo.
ms.topic: how-to
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: bcf99dbc55d708af70a28155a3f98c20003e51f7
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960606"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Creare query per elencare le risorse di Batch in modo efficiente

Quasi tutte le applicazioni Batch devono eseguire un tipo di monitoraggio o un'altra operazione che esegue query sul servizio Batch, spesso a intervalli regolari. Per determinare ad esempio se sono ancora presenti attività in coda in un processo, è necessario ottenere dati per ogni attività nel processo. Per determinare lo stato dei nodi nel pool è necessario ottenere dati in ogni nodo nel pool. Questo articolo illustra come eseguire queste query nel modo più efficiente.

È possibile aumentare le prestazioni dell'applicazione Azure Batch riducendo la quantità di dati restituiti dal servizio quando si eseguono query su processi, attività, nodi di calcolo e altre risorse con la libreria [batch .NET](/dotnet/api/microsoft.azure.batch) .

> [!NOTE]
> Il servizio batch fornisce supporto API per gli scenari comuni di conteggio delle attività in un processo e per il conteggio dei nodi di calcolo nel pool di batch. Anziché usare una query di tipo elenco a questo scopo, è possibile chiamare le operazioni per il [recupero del numero di attività](/rest/api/batchservice/job/gettaskcounts) e l'[elenco del numero di nodi del pool](/rest/api/batchservice/account/listpoolnodecounts). Queste operazioni sono più efficienti rispetto a una query di elenco, ma restituiscono informazioni più limitate che potrebbero non essere sempre aggiornate. Per altre informazioni, vedere [conteggio delle attività e dei nodi di calcolo in base allo stato](batch-get-resource-counts.md).

## <a name="specify-a-detail-level"></a>Specificare un livello di dettaglio

In un'applicazione Batch di produzione, le entità da elaborare, ad esempio processi, attività e nodi di calcolo, possono essere migliaia. Quando si richiedono informazioni su queste risorse, una grande quantità di dati deve "transitare" dal servizio Batch all'applicazione in ogni query. Limitando il numero di elementi e il tipo di informazioni restituiti da una query, è possibile aumentarne la velocità e quindi migliorare le prestazioni dell'applicazione.

Questo frammento di codice dell'API [Batch .NET](/dotnet/api/microsoft.azure.batch) elenca *ogni* attività associata a un processo, insieme a *tutte* le proprietà dell'attività:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

È possibile eseguire una query di tipo elenco molto più efficiente, tuttavia, applicando un "livello di dettaglio" alla query. A questo scopo, specificare un oggetto [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) per il metodo [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations). Questo frammento restituisce solo l'ID, la riga di comando e informazioni sulle proprietà del nodo di calcolo delle attività completate:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Se in questo scenario di esempio il processo include migliaia di attività, il risultato della seconda query viene in genere restituito molto più rapidamente della prima. [Di seguito](#efficient-querying-in-batch-net)sono disponibili altre informazioni sull'uso di ODATADetailLevel quando si elencano elementi con l'API Batch .NET.

> [!IMPORTANT]
> È consigliabile specificare sempre un oggetto ODATADetailLevel per le chiamate di tipo elenco all'API .NET, per assicurare il massimo livello di efficienza e prestazioni dell'applicazione. Specificando un livello di dettaglio è possibile ridurre i tempi di risposta del servizio Batch, migliorare l'utilizzo della rete e ridurre l'utilizzo di memoria da parte delle applicazioni client.

## <a name="filter-select-and-expand"></a>Filtro, selezione ed espansione

Le API [Batch .NET](/dotnet/api/microsoft.azure.batch) e [Batch REST](/rest/api/batchservice/) consentono di ridurre sia il numero di elementi restituiti in un elenco sia la quantità di informazioni restituite per ogni elemento. A questo scopo, specificare stringhe di **filtro**, **selezione** ed **espansione** quando si eseguono query di tipo elenco.

### <a name="filter"></a>Filtro

La stringa di filtro è un'espressione che riduce il numero di elementi restituiti. È ad esempio possibile elencare solo le attività in esecuzione per un processo oppure elencare solo i nodi di calcolo pronti per l'esecuzione delle attività.

Una stringa di filtro è costituita da una o più espressioni, ciascuna delle quali è composta da un nome di proprietà, un operatore e un valore. Le proprietà che è possibile immettere sono specifiche di ogni tipo di entità su cui viene eseguita la query, come lo sono gli operatori supportati per ogni proprietà.  È possibile combinare più espressioni usando gli operatori logici `and` e `or`.

Questo esempio di stringa di filtro indica solo le attività di "rendering" in esecuzione: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Select

La stringa di selezione limita i valori della proprietà restituiti per ogni elemento. È possibile specificare un elenco di nomi di proprietà delimitati da virgole e vengono restituiti solo i valori delle proprietà per gli elementi nei risultati della query. È possibile specificare qualsiasi proprietà per il tipo di entità su cui si esegue la query.

Questa stringa di selezione di esempio specifica che dovranno essere restituiti solo i valori di tre proprietà per ogni attività: `id, state, stateTransitionTime`.

### <a name="expand"></a>Espandere

La stringa di espansione riduce il numero di chiamate API richieste per ottenere determinate informazioni. Quando si usa una stringa di espansione, si possono ottenere altre informazioni su ogni elemento con una singola chiamata API. Anziché ottenere prima l'elenco delle entità, quindi richiedere informazioni per ogni elemento nell'elenco, si usa una stringa di espansione per ottenere le stesse informazioni in una singola chiamata API, contribuendo a migliorare le prestazioni riducendo le chiamate API.

Analogamente alla stringa di selezione, la stringa di espansione controlla se determinati dati sono inclusi nei risultati di una query di tipo elenco. Quando tutte le proprietà sono obbligatorie e non è stata specificata alcuna stringa di selezione, è *necessario* usare la stringa di espansione per ottenere informazioni statistiche. Se si usa una stringa di selezione per ottenere un subset di proprietà, è possibile specificare `stats` nella stringa di selezione e non è necessario specificare la stringa di espansione.

La stringa di espansione è supportata solo quando viene usata nell'elenco di processi, pianificazioni di processi, attività e pool. Attualmente supporta solo informazioni statistiche.

Questo esempio di stringa di espansione specifica che dovranno essere restituite informazioni statistiche per ogni elemento nell'elenco: `stats`.

> [!NOTE]
> Quando si costruisce uno qualsiasi dei tre tipi di stringhe di query, ovvero filtro, selezione ed espansione, è necessario assicurarsi che i nomi delle proprietà e le lettere maiuscole/minuscole corrispondano alle relative controparti nell'API REST. Ad esempio, quando si usa la classe [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) .NET, è necessario specificare **state** invece di **State**, anche se la proprietà .NET è [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Per i mapping delle proprietà tra le API .NET e REST, vedere le tabelle seguenti.

### <a name="rules-for-filter-select-and-expand-strings"></a>Regole per le stringhe di filtro, selezione ed espansione

- I nomi delle proprietà nelle stringhe di filtro, selezione ed espansione dovrebbero apparire come nell'API [Rest di batch](/rest/api/batchservice/) , anche quando si usa [batch .NET](/dotnet/api/microsoft.azure.batch) o uno degli altri SDK di batch.
- Per tutti i nomi di proprietà viene fatta distinzione tra maiuscole e minuscole, al contrario di quanto avviene per i valori delle proprietà.
- Le stringhe relative a data/ora possono essere indicate in uno dei due formati seguenti e devono essere precedute da `DateTime`.
  
  - Esempio di formato W3C-DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Esempio di formato RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Le stringhe booleane sono `true` o `false`.
- Se si specifica una proprietà o un operatore non valido, viene generato un errore `400 (Bad Request)` .

## <a name="efficient-querying-in-batch-net"></a>Esecuzione efficiente di query in Batch .NET

Nell'API [Batch .NET](/dotnet/api/microsoft.azure.batch) viene usata la classe[ ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) per specificare le stringhe di filtro, selezione ed espansione alle operazioni di tipo elenco. La classe ODataDetailLevel presenta tre proprietà pubbliche di tipo stringa che possono essere specificate nel costruttore o impostate direttamente: L'oggetto ODataDetailLevel viene quindi passato come parametro alle diverse operazioni di tipo elenco, ad esempio [ListPools](/dotnet/api/microsoft.azure.batch.pooloperations), [ListJobs](/dotnet/api/microsoft.azure.batch.joboperations), e [ListTasks](/dotnet/api/microsoft.azure.batch.joboperations).

- [ODATADetailLevel. FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause): limitare il numero di elementi restituiti.
- [ODATADetailLevel. SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause): specificare i valori delle proprietà restituiti con ogni elemento.
- [ODATADetailLevel. ExpandClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.expandclause): recupera i dati per tutti gli elementi in una singola chiamata API invece di chiamate separate per ogni elemento.

Il frammento di codice seguente usa l'API Batch .NET per eseguire query efficienti sul servizio Batch per ottenere le statistiche di un set di pool specificato. In questo scenario l'utente Batch ha pool di test e di produzione. Gli ID del pool di test sono preceduti da "test", mentre quelli del pool di produzione sono preceduti da "prod". Nel frammento di codice *myBatchClient* è un'istanza della classe [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) inizializzata correttamente.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> È anche possibile passare un'istanza di [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) configurata con le clausole Select ed Expand ai metodi Get appropriati, ad esempio [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), per limitare la quantità di dati restituiti.

## <a name="batch-rest-to-net-api-mappings"></a>Mapping di API Batch REST a API .NET

I nomi delle proprietà nelle stringhe di filtro, selezione ed espansione devono riflettere le rispettive controparti dell'API REST, sia a livello di nome che di lettere maiuscole/minuscole. Le tabelle seguenti forniscono i mapping tra l'API .NET e le relative controparti dell'API REST.

### <a name="mappings-for-filter-strings"></a>Mapping per le stringhe di filtro

- **Metodi list .NET**: ogni metodo dell'API .NET in questa colonna accetta un oggetto [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) come parametro.
- **Richieste list REST**: ogni pagina dell'API REST collegata in questa colonna contiene una tabella che specifica le proprietà e le operazioni consentite nelle stringhe di *filtro*. Questi nomi di proprietà e operazioni vengono usati quando si costruisce una stringa [ODATADetailLevel. FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause) .

| Metodi list .NET | Richieste list REST |
| --- | --- |
| [CertificateOperations.ListCertificates](/dotnet/api/microsoft.azure.batch.certificateoperations) |[Elencare i certificati in un account](/rest/api/batchservice/certificate/list) |
| [CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask) |[Elencare i file associati a un'attività](/rest/api/batchservice/file/listfromtask) |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus](/dotnet/api/microsoft.azure.batch.joboperations) |[Elencare lo stato della preparazione e le attività di rilascio per un processo](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) |
| [JobOperations.ListJobs](/dotnet/api/microsoft.azure.batch.joboperations) |[Elencare i processi in un account](/rest/api/batchservice/job/list) |
| [JobOperations.ListNodeFiles](/dotnet/api/microsoft.azure.batch.joboperations) |[Elencare i file in un nodo](/rest/api/batchservice/file/listfromcomputenode) |
| [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) |[Elencare le attività associate a un processo](/rest/api/batchservice/task/list) |
| [JobScheduleOperations.ListJobSchedules](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[Elencare le pianificazioni di processi in un account](/rest/api/batchservice/jobschedule/list) |
| [JobScheduleOperations.ListJobs](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[Elencare i processi associati a una pianificazione di processi](/rest/api/batchservice/job/listfromjobschedule) |
| [PoolOperations.ListComputeNodes](/dotnet/api/microsoft.azure.batch.pooloperations) |[Elencare i nodi di calcolo in un pool](/rest/api/batchservice/computenode/list) |
| [PoolOperations.ListPools](/dotnet/api/microsoft.azure.batch.pooloperations) |[Elencare i pool in un account](/rest/api/batchservice/pool/list) |

### <a name="mappings-for-select-strings"></a>Mapping per le stringhe di selezione

- **Tipi di Batch .NET**: Tipi di API Batch .NET.
- **Entità di API REST**: ogni pagina di questa colonna contiene una o più tabelle che indicano i nomi delle proprietà dell'API REST per il tipo. Questi nomi di proprietà vengono usati per la costruzione di stringhe di *selezione* . Usare questi stessi nomi di proprietà quando si costruisce una stringa [ODATADetailLevel. SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause) .

| Tipi di Batch .NET | Entità di API REST |
| --- | --- |
| [Certificate](/dotnet/api/microsoft.azure.batch.certificate) |[Ottenere informazioni su un certificato](/rest/api/batchservice/certificate/get) |
| [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) |[Ottenere informazioni su un processo](/rest/api/batchservice/job/get) |
| [CloudJobSchedule](/dotnet/api/microsoft.azure.batch.cloudjobschedule) |[Ottenere informazioni su una pianificazione di processi](/rest/api/batchservice/jobschedule/get) |
| [ComputeNode](/dotnet/api/microsoft.azure.batch.computenode) |[Ottenere informazioni su un nodo](/rest/api/batchservice/computenode/get) |
| [CloudPool](/dotnet/api/microsoft.azure.batch.cloudpool) |[Ottenere informazioni su un pool](/rest/api/batchservice/pool/get) |
| [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) |[Ottenere informazioni su un'attività](/rest/api/batchservice/task/get) |

## <a name="example-construct-a-filter-string"></a>Esempio: costruire una stringa di filtro

Quando si costruisce una stringa di filtro per una stringa [ODATADetailLevel.FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause), vedere la tabella in "Mapping per le stringhe di filtro" per trovare la pagina di documentazione dell'API REST corrispondente all'operazione di tipo elenco da eseguire. Le proprietà filtrabili e gli operatori supportati sono disponibili nella prima tabella con più righe in quella pagina. Per recuperare ad esempio tutte le attività il cui codice di uscita non è pari a zero, questa riga in [Elencare le attività associate a un processo](/rest/api/batchservice/task/list) specifica la stringa della proprietà applicabile e gli operatori consentiti:

| Proprietà | Operazioni consentite | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

La stringa di filtro per elencare tutte le attività con un codice di uscita non pari a zero sarà:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Esempio: costruire una stringa di selezione

Per costruire una stringa [ODATADetailLevel.SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause), vedere la tabella in "Mapping per le stringhe di selezione" e passare alla pagina dell'API REST che corrisponde al tipo di entità da elencare. Le proprietà selezionabili e gli operatori supportati sono disponibili nella prima tabella con più righe in quella pagina. Se ad esempio si vuole recuperare solo l'ID e la riga di comando per ogni attività in un elenco, queste righe si trovano nella tabella applicabile in [Ottenere informazioni su un'attività](/rest/api/batchservice/task/get):

| Proprietà | Type | Note |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

La stringa di selezione per includere solo l'ID e la riga di comando con ogni attività elencata sarà:

`id, commandLine`

## <a name="code-samples"></a>Esempi di codice

### <a name="efficient-list-queries-code-sample"></a>Esempio di codice per query di elenco efficienti

Il progetto di esempio [EfficientListQueries](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries) su GitHub illustra il modo in cui le query di elenco efficienti possono influire sulle prestazioni in un'applicazione. Questa applicazione console C# crea e aggiunge un numero elevato di attività a un processo. Esegue quindi più chiamate al metodo [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) e passa gli oggetti [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) configurati con valori di proprietà diversi per variare la quantità di dati da restituire. L'output generato sarà simile al seguente:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Come illustrato nelle informazioni sul tempo trascorso, è possibile ridurre notevolmente i tempi di risposta della query limitando le proprietà e il numero di elementi restituiti. Questo e altri progetti di esempio sono disponibili nel repository [azure-batch-samples](https://github.com/Azure-Samples/azure-batch-samples) in GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Libreria BatchMetrics ed esempio di codice

Oltre all'esempio di codice EfficientListQueries precedente, il progetto di esempio [BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) illustra come monitorare in modo efficiente Azure batch lo stato di avanzamento del processo tramite l'API batch.

L'esempio [BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) include un progetto di libreria di classi .NET che è possibile incorporare nei progetti e un semplice programma della riga di comando per apprendere l'uso della libreria.

L'applicazione di esempio nel progetto illustra le operazioni seguenti:

1. Selezione degli attributi specifici per scaricare solo le proprietà necessarie
2. Filtro delle ore di transizione allo stato per scaricare solo le modifiche apportate dopo l'ultima query

Ad esempio, il metodo seguente è presente nella libreria BatchMetrics. Restituisce un elemento ODATADetailLevel che specifica che dovranno essere ottenute solo le proprietà `id` e `state` per le entità sulle quali viene eseguita una query. Specifica anche che dovranno essere restituite solo le entità il cui stato è stato modificato dopo il parametro `DateTime` specificato.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [ottimizzare Azure batch l'utilizzo delle risorse di calcolo con attività del nodo simultanee](batch-parallel-node-tasks.md). Alcuni tipi di carichi di lavoro possono trarre vantaggio dall'esecuzione di attività parallele su nodi di calcolo più grandi (ma meno). Vedere lo [scenario di esempio](batch-parallel-node-tasks.md#example-scenario) nell'articolo per informazioni dettagliate su questo scenario.
- Informazioni su come [monitorare le soluzioni batch contando le attività e i nodi in base allo stato](batch-get-resource-counts.md)


[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_ctor]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_expand]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_filter]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_select]: /dotnet/api/microsoft.azure.batch.odatadetaillevel

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[rest_list_certs]: /rest/api/batchservice/certificate/list
[rest_list_compute_nodes]: /rest/api/batchservice/computenode/list
[rest_list_job_schedules]: /rest/api/batchservice/jobschedule/list
[rest_list_jobprep_status]: /rest/api/batchservice/job/listpreparationandreleasetaskstatus
[rest_list_jobs]: /rest/api/batchservice/job/list
[rest_list_nodefiles]: /rest/api/batchservice/file/listfromcomputenode
[rest_list_pools]: /rest/api/batchservice/pool/list
[rest_list_schedule_jobs]: /rest/api/batchservice/job/listfromjobschedule
[rest_list_task_files]: /rest/api/batchservice/file/listfromtask
[rest_list_tasks]: /rest/api/batchservice/task/list

[rest_get_cert]: /rest/api/batchservice/certificate/get
[rest_get_job]: /rest/api/batchservice/job/get
[rest_get_node]: /rest/api/batchservice/computenode/get
[rest_get_pool]: /rest/api/batchservice/pool/get
[rest_get_schedule]: /rest/api/batchservice/jobschedule/get
[rest_get_task]: /rest/api/batchservice/task/get

[net_cert]: /dotnet/api/microsoft.azure.batch.certificate
[net_job]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_node]: /dotnet/api/microsoft.azure.batch.computenode
[net_pool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_schedule]: /dotnet/api/microsoft.azure.batch.cloudjobschedule
[net_task]: /dotnet/api/microsoft.azure.batch.cloudtask

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
