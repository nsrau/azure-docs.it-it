<properties
	pageTitle="Query di tipo elenco efficienti in Azure Batch | Microsoft Azure"
	description="Migliorare le prestazioni riducendo la quantità di dati restituiti quando si eseguono query sulle entità di Azure Batch, ad esempio pool, processi, attività e nodi di calcolo."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="10/12/2015"
	ms.author="v-marsma"/>

# Eseguire query sul servizio Azure Batch in modo efficiente

In questo articolo si apprenderà come ridurre il numero di elementi e la quantità di dati restituiti quando si usa l'API [Batch .NET][api_net] per eseguire query sul servizio Batch per ottenere elenchi di processi, attività, nodi di calcolo e altro.

Azure Batch è un servizio per l'esecuzione di processi di calcolo di grandi dimensioni. Negli ambienti di produzione, le entità da elaborare, ad esempio processi, attività e nodi di calcolo, possono essere migliaia. Quando si prova a ottenere informazioni su questi elementi, viene generata una grande quantità di dati che deve essere trasferita in ogni query. Limitando il numero di elementi e il tipo di informazioni restituiti per ogni query consente di aumentarne la velocità e di migliorare le prestazioni dell'applicazione.

Elencare processi, attività, nodi di calcolo, sono solo alcuni esempi delle operazioni che quasi tutte le applicazioni che usano Azure Batch devono eseguire, spesso frequentemente. Il monitoraggio è un caso d'uso comune. La determinazione della capacità e dello stato di un pool, ad esempio, richiede l'esecuzione di una query su tutti i nodi di calcolo in quel pool. Un altro esempio è l'esecuzione di query sulle attività di un processo per stabilire se alcune di esse sono ancora nella coda.

Questo frammento di codice dell'API [Batch .NET][api_net] recupera tutte le attività associate a un processo, insieme all'intera famiglia di proprietà di quelle attività:

```
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks = batchClient.JobOperations.ListTasks("job-001");
```

Un modo molto più efficiente di eseguire una query di tipo elenco consiste tuttavia nel fornire un oggetto [ODATADetailLevel][odata] al metodo [JobOperations.ListTasks][net_list_tasks]. Questo frammento restituisce solo l'ID, la riga di comando e informazioni sulle proprietà del nodo di calcolo delle attività completate:

```
// Configure an ODATADetailLevel specifying a subset of tasks and their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks = batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Se nello scenario di esempio precedente il processo include migliaia di attività, il risultato della seconda query viene in genere restituito molto più rapidamente della prima. Altre informazioni sull'uso di un oggetto ODATADetailLevel quando si elencano elementi con l'API Batch .NET sono disponibili di seguito.

> [AZURE.IMPORTANT]È consigliabile fornire **sempre** un oggetto ODATADetailLevel alle chiamate di tipo elenco all'API .NET per assicurare il massimo livello di efficienza e prestazioni per l'applicazione. L'indicazione di un livello di dettaglio consente di ridurre i tempi di risposta del servizio Batch, migliorare l'utilizzo della rete e ridurre l'utilizzo di memoria da parte delle applicazioni client.

## Strumenti per l'esecuzione efficiente di query

Le API [Batch .NET][api_net] e [Batch REST][api_rest] consentono di ridurre sia il numero di elementi restituiti in un elenco, sia la quantità di informazioni restituite da ognuno, specificando stringhe di *filtro*, *selezione* ed *espansione* quando si eseguono query di tipo elenco.

- **filtro**: la *stringa di filtro* è un'espressione che riduce il numero di elementi restituiti. Ad esempio, elencare solo le attività in esecuzione per un processo o solo i nodi di calcolo pronti per eseguire attività.
  - Una stringa di filtro è costituita da una o più espressioni, ciascuna delle quali è composta da un nome di proprietà, un operatore e un valore. Le proprietà che possono essere immesse sono specifiche di ogni tipo di chiamata API, come lo sono gli operatori supportati per ogni proprietà.
  - È possibile combinare più espressioni usando gli operatori logici `and` e `or`.
  - Stringa di filtro di esempio che elenca solo attività di rendering in esecuzione: `startswith(id, 'renderTask') and (state eq 'running')`
- **selezione**: la *stringa di selezione* limita i valori della proprietà restituiti per ogni elemento. Nella stringa di selezione è possibile specificare un elenco di proprietà per un elemento e quindi nei risultati della query di tipo elenco verranno restituiti solo i valori di quelle proprietà per ogni elemento.
  - Una stringa di selezione è costituita da un elenco con valori delimitati da virgole di nomi di proprietà. È possibile specificare qualsiasi proprietà di un elemento restituito da un'operazione di tipo elenco.
  - Stringa di selezione di esempio che specifica la restituzione solo di tre proprietà per ogni attività: `id, state, stateTransitionTime`
- **espansione**: la *stringa di espansione* riduce il numero di chiamate API richieste per ottenere determinate informazioni. È possibile ottenere informazioni più dettagliate per ogni elemento dell'elenco con una singola chiamata API di tipo elenco, invece di ottenere l'elenco e quindi eseguire una chiamata per ogni elemento incluso nell'elenco.
  - Analogamente alla stringa di selezione, la stringa di espansione controlla se determinati dati sono inclusi nei risultati di una query di tipo elenco.
  - La stringa di espansione è supportata solo quando vengono elencati processi, programmazioni processi, attività e pool e attualmente supporta solo informazioni statistiche.
  - Esempio di stringa di espansione che specifica che dovranno essere restituite informazioni statistiche per ogni elemento: `stats`
  - Quando tutte le proprietà sono obbligatorie e non è stata specificata alcuna stringa di selezione, è *necessario* usare la stringa di espansione per ottenere informazioni statistiche. Se si usa una stringa di selezione per ottenere un subset di proprietà, è possibile specificare `stats` nella stringa di selezione e non è necessario specificare la stringa di espansione.

> [AZURE.NOTE]Quando si costruisce uno qualsiasi dei tre tipi di stringhe di query (filtro, selezione, espansione), è necessario assicurarsi che i nomi delle proprietà e le lettere maiuscole/minuscole corrispondano alle relative controparti nell'API REST. Ad esempio, quando si usa l'oggetto [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) .NET, è necessario specificare **state** invece di **State**, anche se la proprietà .NET è [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Per i mapping delle proprietà tra le API .NET e REST, vedere le tabelle seguenti.

### Specifiche per le stringhe di filtro, selezione ed espansione

- Le proprietà specificate nelle stringhe di filtro, selezione ed espansione equivalgono ai nomi di proprietà visualizzati nell'API [Batch REST][api_rest],anche quando si usa la libreria [Batch .NET][api_net].
- Per tutti i nomi di proprietà viene fatta distinzione tra maiuscole e minuscole, al contrario di quanto avviene per i valori delle proprietà
- Le stringhe relative a data/ora possono essere indicate in uno dei due formati seguenti e devono essere precedute da `DateTime`
  - Esempio di formato W3CDTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Esempio di formato RFC1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Le stringhe booleane sono `true` o `false`
- Se si specifica una proprietà o un operatore non valido, viene generato un errore `400 (Bad Request)`

## Esecuzione efficiente di query in Batch .NET

Nell'API [Batch .NET][api_net] viene usato l'oggetto [ODATADetailLevel][odata] per fornire le stringhe di filtro, selezione ed espansione alle operazioni di tipo elenco. Un oggetto ODataDetailLevel ha tre proprietà della stringa pubbliche che possono essere specificate nel costruttore o impostate direttamente e questo oggetto viene poi passato come parametro alle diverse operazioni di tipo elenco, quali [ListPools][net_list_pools], [ListJobs][net_list_jobs] e [ListTasks][net_list_tasks].

- [ODATADetailLevel.FilterClause][odata_filter]\: limita il numero di elementi restituiti
- [ODATADetailLevel.SelectClause][odata_select]\: specifica un subset di valori della proprietà restituiti con ogni elemento
- [ODATADetailLevel.ExpandClause][odata_expand]\: recupera i dati dell'elemento in una singola chiamata API, invece di generare chiamate per ognuno

Il frammento di codice seguente usa l'API Batch .NET per eseguire query efficienti sul servizio Batch per ottenere le statistiche di un set di pool specificato. In questo scenario, l'utente Batch ha un pool di test e un pool di produzione. Gli ID del pool di test hanno il prefisso "test" mentre gli ID del pool di produzione hanno il prefisso "prod". Nel frammento di codice, *myBatchClient* è un'istanza correttamente inizializzata di [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient).

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// We want to pull only the "test" pools, so we limit the number of items returned by using a
	// FilterClause and specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();

> [AZURE.TIP]È possibile limitare la quantità di dati restituita passando un'istanza di [ODATADetailLevel][odata] configurata con le clausole Select ed Expand ai metodi Get appropriati, ad esempio [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx).

## Mapping di API Batch REST a API .NET

I nomi delle proprietà nelle stringhe di filtro, selezione ed espansione *devono* riflettere le rispettive controparti dell'API REST, sia a livello di nome che di lettere maiuscole/minuscole. Le tabelle seguenti forniscono i mapping tra l'API .NET e le relative controparti dell'API REST.

### Mapping per le stringhe di filtro

- **METODI LIST .NET**: ogni metodo dell'API .NET in questa colonna accetta un oggetto [ODATADetailLevel][odata] come parametro.
- **RICHIESTE LIST REST**: ogni pagina dell'API REST in questa colonna contiene una tabella che specifica le proprietà e le operazioni consentite nelle stringhe di *filtro*. Questi nomi di proprietà e operazioni verranno usati per la costruzione di una stringa [ODATADetailLevel.FilterClause][odata_filter].

| Metodi List .NET | Metodi List REST |
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

- **TIPI DI BATCH .NET**: tipi di API Batch .NET
- **ENTITÀ DI API REST**: ogni pagina di questa colonna contiene una o più tabelle che elencano i nomi delle proprietà dell'API REST per il tipo. Questi nomi di proprietà vengono usati per la costruzione di stringhe di *selezione*. Questi stessi nomi di proprietà verranno usati per la costruzione di una stringa [ODATADetailLevel.SelectClause][odata_select].

| Tipo di Batch .NET | Entità di API REST |
|---|---|
| [Certificate][net_cert] | [Ottenere informazioni su un certificato][rest_get_cert] |
| [CloudJob][net_job] | [Ottenere informazioni su un processo][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Ottenere informazioni su una programmazione processi][rest_get_schedule] |
| [ComputeNode][net_node] | [Ottenere informazioni su un nodo][rest_get_node] |
| [CloudPool][net_pool] | [Ottenere informazioni su un pool][rest_get_pool] |
| [CloudTask][net_task] | [Ottenere informazioni su un'attività][rest_get_task] |

### Esempio: costruire una stringa di filtro

Quando si costruisce una stringa di filtro per un oggetto [ODATADetailLevel.FilterClause][odata_filter], vedere *Mapping per le stringhe di filtro* nella tabella precedente per trovare la pagina di documentazione dell'API REST corrispondente all'operazione di tipo elenco da eseguire. Le proprietà filtrabili e gli operatori supportati sono disponibili nella prima tabella con più righe in quella pagina. Per recuperare tutte le attività il cui codice di uscita non è pari a zero, ad esempio, questa riga in [Elencare le attività associate a un processo][rest_list_tasks] specifica la stringa della proprietà applicabile e gli operatori consentiti:

| Proprietà | Operazioni consentite | Tipo |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

La stringa di filtro per elencare tutte le attività con un codice di uscita non pari a zero sarà:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

### Esempio: costruire una stringa di selezione

Per costruire una stringa [ODATADetailLevel.SelectClause][odata_select], vedere la tabella in *Mapping per le stringhe di selezione* e passare alla pagina dell'API REST corrispondente al tipo di entità che si sta elencando. Le proprietà selezionabili e gli operatori supportati sono disponibili nella prima tabella con più righe in quella pagina. Se si vuole recuperare solo l'ID e la riga di comando per ogni attività in un elenco, ad esempio, queste righe si trovano nella tabella applicabile in [Ottenere informazioni su un'attività][rest_get_task]\:

| Proprietà | Tipo | Note |
| :--- | :--- | :--- |
| `id` | `String` | `The id of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

La stringa di selezione per includere solo l'ID e la riga di comando con ogni attività elencata sarà:

`id, commandLine`

## Passaggi successivi

Per verificare in che modo una query di tipo elenco può influire efficacemente sulle prestazioni in un'applicazione, vedere il progetto di esempio [EfficientListQueries][efficient_query_sample] su GitHub. Questa applicazione console in C# crea e aggiunge una grande quantità di attività a un processo, quindi esegue una query sul servizio Batch usando diverse specifiche di [ODATADetailLevel][odata] e visualizza un output simile al seguente:

		Adding 5000 tasks to job jobEffQuery...
		5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

		4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
		0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
		59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
		5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
		5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
		5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

		Sample complete, hit ENTER to continue...

Come illustrato nelle informazioni del tempo trascorso, limitare le proprietà e il numero di elementi restituiti può ridurre notevolmente i tempi di risposta alla query. Questo e altri progetti di esempio sono disponibili nel repository [azure-batch-samples][github_samples] su GitHub.

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

<!---HONumber=Oct15_HO3-->