---
title: Query di tipo elenco efficienti in Azure Batch | Documentazione Microsoft
description: "Migliorare le prestazioni filtrando le query quando si chiedono informazioni su risorse di Batch, ad esempio pool, processi, attività e nodi di calcolo."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 01/20/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: dfcf1e1d54a0c04cacffb50eca4afd39c6f6a1b1
ms.openlocfilehash: 62487d3d26ba66ce2ba88ae1a71858d68e68544c


---
# <a name="query-the-azure-batch-service-efficiently"></a>Eseguire query sul servizio Azure Batch in modo efficiente
Viene illustrato come migliorare le prestazioni dell'applicazione Azure Batch, riducendo la quantità di dati restituiti dal servizio quando si eseguono query su processi, attività e nodi di calcolo con la libreria [Batch .NET][api_net].

Quasi tutte le applicazioni Batch devono eseguire un tipo di monitoraggio o un'altra operazione che esegue query sul servizio Batch, spesso a intervalli regolari. Per determinare ad esempio se sono ancora presenti attività in coda in un processo, è necessario ottenere dati per ogni attività nel processo. Per determinare lo stato dei nodi nel pool è necessario ottenere dati in ogni nodo nel pool. Questo articolo illustra come eseguire queste query nel modo più efficiente.

## <a name="meet-the-detaillevel"></a>Definire livelli di dettaglio
In un'applicazione Batch di produzione, le entità da elaborare, ad esempio processi, attività e nodi di calcolo, possono essere migliaia. Quando si richiedono informazioni su queste risorse, una grande quantità di dati deve "transitare" dal servizio Batch all'applicazione in ogni query. Limitando il numero di elementi e il tipo di informazioni restituiti da una query, è possibile aumentarne la velocità e quindi migliorare le prestazioni dell'applicazione.

Questo frammento di codice dell'API [Batch .NET][api_net] elenca *ogni* attività associata a un processo, insieme a *tutte* le proprietà dell'attività:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

È possibile eseguire una query di tipo elenco molto più efficiente, tuttavia, applicando un "livello di dettaglio" alla query. A questo scopo, indicare un oggetto [ODATADetailLevel][odata] al metodo [JobOperations.ListTasks][net_list_tasks]. Questo frammento restituisce solo l'ID, la riga di comando e informazioni sulle proprietà del nodo di calcolo delle attività completate:

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
> È consigliabile specificare *sempre* un oggetto ODATADetailLevel per le chiamate di tipo elenco all'API .NET, per assicurare il massimo livello di efficienza e prestazioni dell'applicazione. Specificando un livello di dettaglio è possibile ridurre i tempi di risposta del servizio Batch, migliorare l'utilizzo della rete e ridurre l'utilizzo di memoria da parte delle applicazioni client.
> 
> 

## <a name="filter-select-and-expand"></a>Filtro, selezione ed espansione
Le API [Batch .NET][api_net] e [Batch REST][api_rest] consentono di ridurre sia il numero di elementi restituiti in un elenco sia la quantità di informazioni restituite per ogni elemento. A questo scopo, specificare stringhe di **filtro**, **selezione** ed **espansione** quando si eseguono query di tipo elenco.

### <a name="filter"></a>Filtro
La stringa di filtro è un'espressione che riduce il numero di elementi restituiti. Ad esempio, elencare solo le attività in esecuzione per un processo o solo i nodi di calcolo pronti per eseguire attività.

* Una stringa di filtro è costituita da una o più espressioni, ciascuna delle quali è composta da un nome di proprietà, un operatore e un valore. Le proprietà che è possibile immettere sono specifiche di ogni tipo di entità su cui viene eseguita la query, come lo sono gli operatori supportati per ogni proprietà.
* È possibile combinare più espressioni usando gli operatori logici `and` e `or`.
* Questo esempio di stringa di filtro indica solo le attività di "rendering" in esecuzione: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Selezionare
La stringa di selezione limita i valori della proprietà restituiti per ogni elemento. Si specifica un elenco di nomi di proprietà e vengono restituiti solo i valori di quelle proprietà per gli elementi nei risultati della query.

* La stringa di selezione è costituita da un elenco con valori delimitati da virgole di nomi di proprietà. È possibile specificare qualsiasi proprietà per il tipo di entità su cui si esegue la query.
* Questa stringa di selezione di esempio specifica che dovranno essere restituiti solo i valori di tre proprietà per ogni attività: `id, state, stateTransitionTime`.

### <a name="expand"></a>Espandere
La stringa di espansione riduce il numero di chiamate API richieste per ottenere determinate informazioni. Quando si usa una stringa di espansione, si possono ottenere altre informazioni su ogni elemento con una singola chiamata API. Invece di ottenere prima l'elenco delle entità e quindi richiedere informazioni per ogni elemento nell'elenco, usare una stringa di espansione per ottenere le stesse informazioni in una singola chiamata API. Meno chiamate API significano prestazioni migliori.

* Analogamente alla stringa di selezione, la stringa di espansione controlla se determinati dati sono inclusi nei risultati di una query di tipo elenco.
* La stringa di espansione è supportata solo quando viene usata nell'elenco di processi, pianificazioni di processi, attività e pool. Attualmente supporta solo informazioni statistiche.
* Quando tutte le proprietà sono obbligatorie e non è stata specificata alcuna stringa di selezione, è *necessario* usare la stringa di espansione per ottenere informazioni statistiche. Se si usa una stringa di selezione per ottenere un subset di proprietà, è possibile specificare `stats` nella stringa di selezione e non è necessario specificare la stringa di espansione.
* Questo esempio di stringa di espansione specifica che dovranno essere restituite informazioni statistiche per ogni elemento nell'elenco: `stats`.

> [!NOTE]
> Quando si costruisce uno qualsiasi dei tre tipi di stringhe di query, ovvero filtro, selezione ed espansione, è necessario assicurarsi che i nomi delle proprietà e le lettere maiuscole/minuscole corrispondano alle relative controparti nell'API REST. Ad esempio, quando si usa la classe [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) .NET, è necessario specificare **state** invece di **State**, anche se la proprietà .NET è [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Per i mapping delle proprietà tra le API .NET e REST, vedere le tabelle seguenti.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Regole per le stringhe di filtro, selezione ed espansione
* I nomi delle proprietà nelle stringhe di filtro, selezione ed espansione devono corrispondere a quelli presenti nell'API [Batch REST][api_rest] anche quando si usa [Batch .NET][api_net] o uno degli altri SDK di Batch.
* Per tutti i nomi di proprietà viene fatta distinzione tra maiuscole e minuscole, al contrario di quanto avviene per i valori delle proprietà.
* Le stringhe relative a data/ora possono essere indicate in uno dei due formati seguenti e devono essere precedute da `DateTime`.
  
  * Esempio di formato W3C-DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Esempio di formato RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Le stringhe booleane sono `true` o `false`.
* Se si specifica una proprietà o un operatore non valido, viene generato un errore `400 (Bad Request)` .

## <a name="efficient-querying-in-batch-net"></a>Esecuzione efficiente di query in Batch .NET
Nell'API [Batch .NET][api_net] viene usata la classe [ODATADetailLevel][odata] per specificare le stringhe di filtro, selezione ed espansione alle operazioni di tipo elenco. La classe ODataDetailLevel presenta tre proprietà pubbliche di tipo stringa che possono essere specificate nel costruttore o impostate direttamente: L'oggetto ODataDetailLevel viene quindi passato come parametro alle diverse operazioni di tipo elenco, ad esempio [ListPools][net_list_pools], [ListJobs][net_list_jobs] e [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[FilterClause][odata_filter]: limita il numero di elementi restituiti.
* [ODATADetailLevel][odata].[SelectClause][odata_select]: specifica i valori della proprietà restituiti con ogni elemento.
* [ODATADetailLevel][odata].[ExpandClause][odata_expand]: recupera i dati per tutti gli elementi in una singola chiamata all'API anziché con chiamate separate per ogni elemento.

Il frammento di codice seguente usa l'API Batch .NET per eseguire query efficienti sul servizio Batch per ottenere le statistiche di un set di pool specificato. In questo scenario l'utente Batch ha pool di test e di produzione. Gli ID del pool di test sono preceduti da "test", mentre quelli del pool di produzione sono preceduti da "prod". Nel frammento di codice *myBatchClient* è un'istanza della classe [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) inizializzata correttamente.

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
> È anche possibile passare un'istanza di [ODATADetailLevel][odata] configurata con le clausole Select ed Expand ai metodi Get appropriati, ad esempio [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), per limitare la quantità di dati restituiti.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Mapping di API Batch REST a API .NET
I nomi delle proprietà nelle stringhe di filtro, selezione ed espansione *devono* riflettere le rispettive controparti dell'API REST, sia a livello di nome che di lettere maiuscole/minuscole. Le tabelle seguenti forniscono i mapping tra l'API .NET e le relative controparti dell'API REST.

### <a name="mappings-for-filter-strings"></a>Mapping per le stringhe di filtro
* **Metodi list .NET**: ogni metodo dell'API .NET in questa colonna accetta un oggetto [ODATADetailLevel][odata] come parametro.
* **Richieste list REST**: ogni pagina dell'API REST collegata in questa colonna contiene una tabella che specifica le proprietà e le operazioni consentite nelle stringhe di *filtro* . Questi nomi di proprietà e queste operazioni verranno usati per costruire una stringa [ODATADetailLevel.FilterClause][odata_filter].

| Metodi list .NET | Richieste list REST |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Elencare i certificati in un account][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Elencare i file associati a un'attività][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Elencare lo stato della preparazione e le attività di rilascio per un processo specifico][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Elencare i processi in un account][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Elencare i file in un nodo][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Elencare le attività associate a un processo][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Elencare le pianificazioni di processi in un account][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Elencare i processi associati a una pianificazione di processi][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Elencare i nodi di calcolo in un pool][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Elencare i pool in un account][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mapping per le stringhe di selezione
* **Tipi di Batch .NET**: tipi di API Batch .NET.
* **Entità di API REST**: ogni pagina di questa colonna contiene una o più tabelle che indicano i nomi delle proprietà dell'API REST per il tipo. Questi nomi di proprietà vengono usati per la costruzione di stringhe di *selezione* . Questi stessi nomi di proprietà verranno usati per costruire una stringa [ODATADetailLevel.SelectClause][odata_select].

| Tipi di Batch .NET | Entità di API REST |
| --- | --- |
| [Certificate][net_cert] |[Ottenere informazioni su un certificato][rest_get_cert] |
| [CloudJob][net_job] |[Ottenere informazioni su un processo][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Ottenere informazioni su una pianificazione di processi][rest_get_schedule] |
| [ComputeNode][net_node] |[Ottenere informazioni su un nodo][rest_get_node] |
| [CloudPool][net_pool] |[Ottenere informazioni su un pool][rest_get_pool] |
| [CloudTask][net_task] |[Ottenere informazioni su un'attività][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Esempio: costruire una stringa di filtro
Quando si costruisce una stringa di filtro per un oggetto [ODATADetailLevel.FilterClause][odata_filter], vedere la tabella in "Mapping per le stringhe di filtro" per trovare la pagina di documentazione dell'API REST corrispondente all'operazione di tipo elenco da eseguire. Le proprietà filtrabili e gli operatori supportati sono disponibili nella prima tabella con più righe in quella pagina. Per recuperare ad esempio tutte le attività il cui codice di uscita non è pari a zero, questa riga in [Elencare le attività associate a un processo][rest_list_tasks] specifica la stringa della proprietà applicabile e gli operatori consentiti:

| Proprietà | Operazioni consentite | Tipo |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

La stringa di filtro per elencare tutte le attività con un codice di uscita non pari a zero sarà:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Esempio: costruire una stringa di selezione
Per costruire una stringa [ODATADetailLevel.SelectClause][odata_select], vedere la tabella in "Mapping per le stringhe di selezione" e passare alla pagina dell'API REST che corrisponde al tipo di entità da specificare. Le proprietà selezionabili e gli operatori supportati sono disponibili nella prima tabella con più righe in quella pagina. Se ad esempio si desidera recuperare solo l'ID e la riga di comando per ogni attività in un elenco, queste righe si trovano nella tabella applicabile in [Ottenere informazioni su un'attività][rest_get_task]:

| Proprietà | Tipo | Note |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

La stringa di selezione per includere solo l'ID e la riga di comando con ogni attività elencata sarà:

`id, commandLine`

## <a name="code-samples"></a>Esempi di codice
### <a name="efficient-list-queries-code-sample"></a>Esempio di codice per query di elenco efficienti
Per verificare il modo in cui una query di tipo elenco può influire efficacemente sulle prestazioni in un'applicazione, vedere il progetto di esempio [EfficientListQueries][efficient_query_sample] in GitHub. Questa applicazione console C# crea e aggiunge un numero elevato di attività a un processo. Esegue quindi più chiamate al metodo [JobOperations.ListTasks][net_list_tasks] e passa gli oggetti [ODATADetailLevel][odata] configurati con valori di proprietà diversi per variare la quantità di dati da restituire. L'output generato sarà simile al seguente:

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

Come illustrato nelle informazioni sul tempo trascorso, è possibile ridurre notevolmente i tempi di risposta della query limitando le proprietà e il numero di elementi restituiti. Questo e altri progetti di esempio sono disponibili nel repository [azure-batch-samples][github_samples] in GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Libreria BatchMetrics ed esempio di codice
Oltre all'esempio di codice EfficientListQueries precedente, è possibile trovare il progetto [BatchMetrics][batch_metrics] nel repository [azure-batch-samples][github_samples] in GitHub. Il progetto di esempio BatchMetrics illustra come monitorare in modo efficiente lo stato dei processi di Azure Batch con l'API di Batch.

L'esempio [BatchMetrics][batch_metrics] include un progetto di libreria di classi .NET che è possibile incorporare nei propri progetti e un semplice programma della riga di comando per apprendere l'uso della libreria.

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
### <a name="parallel-node-tasks"></a>Attività parallele sui nodi
[Ottimizzare l'utilizzo delle risorse di calcolo di Azure Batch con attività dei nodi simultanee](batch-parallel-node-tasks.md) è un altro articolo correlato alle prestazioni per l'applicazione Batch. Alcuni tipi di carichi di lavoro possono trarre vantaggio dall'esecuzione di attività in parallelo su nodi di calcolo più grandi, ma in numero inferiore. Vedere lo [scenario di esempio](batch-parallel-node-tasks.md#example-scenario) nell'articolo per informazioni dettagliate su questo scenario.

### <a name="batch-forum"></a>Forum di Batch
Il [forum di Azure Batch][forum] su MSDN consente di seguire discussioni su Batch e inviare domande sul servizio. Leggere i post contrassegnati e inviare domande durante le procedure di sviluppo delle soluzioni Batch.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx



<!--HONumber=Dec16_HO2-->


