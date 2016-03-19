<properties
	pageTitle="Query di tipo elenco efficienti in Azure Batch | Microsoft Azure"
	description="Migliorare le prestazioni riducendo la quantità di dati restituiti quando si eseguono query sulle entità di Azure Batch, ad esempio pool, processi, attività e nodi di calcolo."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />
	
<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="01/22/2016"
	ms.author="marsma" />
	
# Eseguire query sul servizio Azure Batch in modo efficiente

In questo articolo si apprenderà come migliorare le prestazioni dell'applicazione Azure Batch, riducendo la quantità di dati restituiti quando si esegue una query sul servizio Batch con la libreria [Batch .NET][api_net].

Azure Batch offre funzionalità Big Compute per calcoli su vasta scala e nell'ambiente di produzione le entità, come processi attività e nodi di calcolo, possono essere migliaia. Quando si prova a ottenere informazioni su questi elementi, viene generata una grande quantità di dati che devono essere trasferiti dal servizio all'applicazione in ogni query. Limitando il numero di elementi e il tipo di informazioni restituiti per ogni query, è possibile aumentarne la velocità e quindi migliorare le prestazioni dell'applicazione.

Quasi tutte le applicazioni che usano Azure Batch eseguono un tipo di monitoraggio o un'altra operazione che esegue query sul servizio Batch, spesso a intervalli regolari. Ad esempio, per determinare la capacità e lo stato di un pool, è necessario eseguire query su ogni nodo nel pool. Per determinare che se una delle attività di un processo è ancora in coda, è necessario eseguire una query su ogni attività all'interno del processo. Questo articolo illustra come eseguire questi tipi di query nel modo più efficiente.

Questo frammento di codice dell'API [Batch .NET][api_net] recupera ogni attività associata a un processo, insieme a *tutte* le proprietà delle attività:

```
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks = batchClient.JobOperations.ListTasks("job-001");
```

È tuttavia possibile eseguire una query di tipo elenco molto più efficiente. A questo scopo, fornire un oggetto [ODATADetailLevel][odata] al metodo [JobOperations.ListTasks][net_list_tasks]. Questo frammento restituisce solo l'ID, la riga di comando e informazioni sulle proprietà del nodo di calcolo delle attività completate:

```
// Configure an ODATADetailLevel specifying a subset of tasks and their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks = batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Se nello scenario di esempio precedente il processo include migliaia di attività, il risultato della seconda query viene in genere restituito molto più rapidamente della prima. Di seguito sono disponibili altre informazioni sull'uso di ODATADetailLevel quando si elencano elementi con l'API Batch .NET.

> [AZURE.IMPORTANT]
È consigliabile fornire *sempre* un oggetto ODATADetailLevel per le chiamate di tipo elenco all'API .NET, per assicurare il massimo livello di efficienza e prestazioni dell'applicazione. Specificando un livello di dettaglio è possibile ridurre i tempi di risposta del servizio Batch, migliorare l'utilizzo della rete e ridurre l'utilizzo di memoria da parte delle applicazioni client.

## Strumenti per l'esecuzione efficiente di query

Le API [Batch .NET][api_net] e [Batch REST][api_rest] consentono di ridurre sia il numero di elementi restituiti in un elenco, sia la quantità di informazioni restituite da ognuno. A questo scopo, specificare stringhe di **filtro**, **selezione** ed **espansione** quando si eseguono query di tipo elenco.

### Filtro
La stringa di filtro è un'espressione che riduce il numero di elementi restituiti. Ad esempio, elencare solo le attività in esecuzione per un processo o solo i nodi di calcolo pronti per eseguire attività.

- Una stringa di filtro è costituita da una o più espressioni, ciascuna delle quali è composta da un nome di proprietà, un operatore e un valore. Le proprietà che è possibile immettere sono specifiche di ogni tipo di entità su cui viene eseguita la query, come lo sono gli operatori supportati per ogni proprietà.
- È possibile combinare più espressioni usando gli operatori logici `and` e `or`.
- Questo esempio di stringa di filtro elenca solo le attività di "rendering" in esecuzione: `(state eq 'running') and startswith(id, 'renderTask')`

### Selezionare
La stringa di selezione limita i valori della proprietà restituiti per ogni elemento. Si specifica un elenco di nomi di proprietà e vengono restituiti solo i valori di quelle proprietà per gli elementi nei risultati della query.

- La stringa di selezione è costituita da un elenco con valori delimitati da virgole di nomi di proprietà. È possibile specificare qualsiasi proprietà per il tipo di entità su cui si esegue la query.
- Questa stringa di selezione di esempio specifica che dovranno essere restituiti solo i valori di tre proprietà per ogni attività: `id, state, stateTransitionTime`

### Espandere
La stringa di espansione riduce il numero di chiamate API richieste per ottenere determinate informazioni. Quando si usa una stringa di espansione, si possono ottenere altre informazioni su ogni elemento con una singola chiamata API. Invece di ottenere prima l'elenco delle entità e quindi richiedere informazioni per ogni elemento nell'elenco, usare una stringa di espansione per ottenere le stesse informazioni in una singola chiamata API. Meno chiamate API significano prestazioni migliori.

- Analogamente alla stringa di selezione, la stringa di espansione controlla se determinati dati sono inclusi nei risultati di una query di tipo elenco.
- La stringa di espansione è supportata solo quando viene usata nell'elenco di processi, pianificazioni di processi, attività e pool. Attualmente supporta solo informazioni statistiche.
- Quando tutte le proprietà sono obbligatorie e non è stata specificata alcuna stringa di selezione, è *necessario* usare la stringa di espansione per ottenere informazioni statistiche. Se si usa una stringa di selezione per ottenere un subset di proprietà, è possibile specificare `stats` nella stringa di selezione e non è necessario specificare la stringa di espansione.
- Questa stringa di espansione di esempio specifica che dovranno essere restituite informazioni statistiche per ogni elemento nell'elenco: `stats`

> [AZURE.NOTE] Quando si costruisce uno qualsiasi dei tre tipi di stringhe di query, ovvero filtro, selezione ed espansione, è necessario assicurarsi che i nomi delle proprietà e le lettere maiuscole/minuscole corrispondano alle relative controparti nell'API REST. Ad esempio, quando si usa la classe [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) .NET, è necessario specificare **state** invece di **State**, anche se la proprietà .NET è [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Per i mapping delle proprietà tra le API .NET e REST, vedere le tabelle seguenti.

### Specifiche per le stringhe di filtro, selezione ed espansione

- Le proprietà specificate nelle stringhe di filtro, selezione ed espansione equivalgono ai nomi di proprietà visualizzati nell'API [Batch REST][api_rest], anche quando si usa la libreria [Batch .NET][api_net].
- Per tutti i nomi di proprietà viene fatta distinzione tra maiuscole e minuscole, al contrario di quanto avviene per i valori delle proprietà.
- Le stringhe relative a data/ora possono essere indicate in uno dei due formati seguenti e devono essere precedute da `DateTime`.
  - Esempio di formato W3CDTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`.
  - Esempio di formato RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`.
- Le stringhe booleane sono `true` o `false`
- Se si specifica una proprietà o un operatore non valido, viene generato un errore `400 (Bad Request)`.

## Esecuzione efficiente di query in Batch .NET

Nell'API [Batch .NET][api_net] viene usata la classe [ODATADetailLevel][odata] per fornire le stringhe di filtro, selezione ed espansione alle operazioni di tipo elenco. La classe ODataDetailLevel presenta tre proprietà pubbliche di tipo stringa che possono essere specificate nel costruttore o impostate direttamente: Si passa quindi l'oggetto ODataDetailLevel come parametro alle diverse operazioni di tipo elenco, ad esempio [ListPools][net_list_pools], [ListJobs][net_list_jobs] e [ListTasks][net_list_tasks].

- [ODATADetailLevel.FilterClause][odata_filter]\: limita il numero di elementi restituiti.
- [ODATADetailLevel.SelectClause][odata_select]\: specifica quali valori della proprietà vengono restituiti con ogni elemento.
- [ODATADetailLevel.ExpandClause][odata_expand]\: recupera i dati per tutti gli elementi in una singola chiamata API invece di chiamate separate per ogni elemento.

Il frammento di codice seguente usa l'API Batch .NET per eseguire query efficienti sul servizio Batch per ottenere le statistiche di un set di pool specificato. In questo scenario l'utente Batch ha pool di test e di produzione. Gli ID del pool di test sono preceduti da "test", mentre quelli del pool di produzione sono preceduti da "prod". Nel frammento di codice *myBatchClient* è un'istanza di [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) inizializzata correttamente.

```
// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
// clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned by using a
// FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to limit the
// properties that are returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned by specifying the
// detail level that we configured above
List<CloudPool> testPools = await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [AZURE.TIP] È possibile limitare la quantità di dati restituiti passando un'istanza di [ODATADetailLevel][odata] configurata con le clausole Select ed Expand ai metodi Get appropriati, ad esempio [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx).

## Mapping di API Batch REST a API .NET

I nomi delle proprietà nelle stringhe di filtro, selezione ed espansione *devono* riflettere le rispettive controparti dell'API REST, sia a livello di nome che di lettere maiuscole/minuscole. Le tabelle seguenti forniscono i mapping tra l'API .NET e le relative controparti dell'API REST.

### Mapping per le stringhe di filtro

- **Metodi list .NET**: ogni metodo dell'API .NET in questa colonna accetta un oggetto [ODATADetailLevel][odata] come parametro.
- **Richieste list REST**: ogni pagina dell'API REST collegata in questa colonna contiene una tabella che specifica le proprietà e le operazioni consentite nelle stringhe di *filtro*. Questi nomi di proprietà e operazioni verranno usati per costruire una stringa [ODATADetailLevel.FilterClause][odata_filter].

| Metodi list .NET | Richieste list REST |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Elencare i certificati in un account][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Elencare i file associati a un'attività][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [Elencare lo stato della preparazione del processo e le attività di rilascio di un processo per un processo][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Elencare i processi in un account][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Elencare i file in un nodo][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [Elencare le attività associate a un processo][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Elencare le programmazioni processi in un account][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Elencare i processi associati a una programmazione processi][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Elencare i nodi di calcolo in un pool][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [Elencare i pool in un account][rest_list_pools]

### Mapping per le stringhe di selezione

- **Tipi di Batch .NET**: tipi di API Batch .NET.
- **Entità di API REST**: ogni pagina di questa colonna contiene una o più tabelle che elencano i nomi delle proprietà dell'API REST per il tipo. Questi nomi di proprietà vengono usati per la costruzione di stringhe di *selezione*. Questi stessi nomi di proprietà verranno usati per costruire una stringa [ODATADetailLevel.SelectClause][odata_select].

| Tipi di Batch .NET | Entità di API REST |
|---|---|
| [Certificate][net_cert] | [Ottenere informazioni su un certificato][rest_get_cert] |
| [CloudJob][net_job] | [Ottenere informazioni su un processo][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Ottenere informazioni su una programmazione processi][rest_get_schedule] |
| [ComputeNode][net_node] | [Ottenere informazioni su un nodo][rest_get_node] |
| [CloudPool][net_pool] | [Ottenere informazioni su un pool][rest_get_pool] |
| [CloudTask][net_task] | [Ottenere informazioni su un'attività][rest_get_task] |

### Esempio: costruire una stringa di filtro

Quando si costruisce una stringa di filtro per un oggetto [ODATADetailLevel.FilterClause][odata_filter], vedere "Mapping per le stringhe di filtro" nella tabella precedente per trovare la pagina di documentazione dell'API REST corrispondente all'operazione di tipo elenco da eseguire. Le proprietà filtrabili e gli operatori supportati sono disponibili nella prima tabella con più righe in quella pagina. Per recuperare ad esempio tutte le attività il cui codice di uscita non è pari a zero, questa riga in [Elencare le attività associate a un processo][rest_list_tasks] specifica la stringa della proprietà applicabile e gli operatori consentiti:

| Proprietà | Operazioni consentite | Tipo |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

La stringa di filtro per elencare tutte le attività con un codice di uscita non pari a zero sarà:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

### Esempio: costruire una stringa di selezione

Per costruire una stringa [ODATADetailLevel.SelectClause][odata_select], vedere la tabella in "Mapping per le stringhe di selezione" e passare alla pagina dell'API REST che corrisponde al tipo di entità che si vuole elencare. Le proprietà selezionabili e gli operatori supportati sono disponibili nella prima tabella con più righe in quella pagina. Se si vuole recuperare solo l'ID e la riga di comando per ogni attività in un elenco, ad esempio, queste righe si trovano nella tabella applicabile in [Ottenere informazioni su un'attività][rest_get_task]\:

| Proprietà | Tipo | Note |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

La stringa di selezione per includere solo l'ID e la riga di comando con ogni attività elencata sarà:

`id, commandLine`

## Passaggi successivi

Per verificare in che modo una query di tipo elenco può influire efficacemente sulle prestazioni in un'applicazione, vedere il progetto di esempio [EfficientListQueries][efficient_query_sample] su GitHub. Questa applicazione console C# crea e aggiunge un numero elevato di attività a un processo. Esegue quindi più chiamate al metodo [JobOperations.ListTasks][net_list_tasks] e passa gli oggetti [ODATADetailLevel][odata] configurati con valori di proprietà diversi per variare la quantità di dati da restituire. L'output generato sarà simile al seguente:

		Adding 5000 tasks to job jobEffQuery...
		5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

		4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
		0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
		59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
		5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
		5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
		5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

		Sample complete, hit ENTER to continue...

Come illustrato nelle informazioni sul tempo trascorso, è possibile ridurre notevolmente i tempi di risposta della query limitando le proprietà e il numero di elementi restituiti. Questo e altri progetti di esempio sono disponibili nel repository [azure-batch-samples][github_samples] su GitHub.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
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

<!---HONumber=AcomDC_0128_2016-->