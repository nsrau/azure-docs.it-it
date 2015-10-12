<properties
	pageTitle="Query di tipo elenco efficienti in Azure Batch | Microsoft Azure"
	description="Informazioni su come ridurre la quantità di dati restituiti e migliorare le prestazioni durante l'esecuzione di query su pool, processi, attività, nodi di calcolo e altri elementi di Azure Batch."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="Batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="09/24/2015"
	ms.author="davidmu;v-marsma"/>

# Query di tipo elenco efficienti in Azure Batch

Azure Batch è un servizio per l'esecuzione di processi di calcolo di grandi dimensioni. Negli ambienti di produzione, le entità da elaborare, ad esempio processi, attività e nodi di calcolo, possono essere migliaia. Quando si prova a ottenere informazioni su questi elementi, viene generata una grande quantità di dati che deve essere trasferita in ogni query. Limitando il numero di elementi e il tipo di informazioni restituiti per ogni query consente di aumentarne la velocità e di migliorare le prestazioni dell'applicazione.

I metodi dell'API [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) seguenti sono esempi di operazioni eseguite, anche di frequente, da tutte le applicazioni che usano Azure Batch:

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx)
- [ListComputeNodes](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx)

Il monitoraggio è un caso d'uso comune. La determinazione della capacità e dello stato di un pool, ad esempio, richiede l'esecuzione di una query su tutti i nodi di calcolo (macchine virtuali) in un pool. Un altro esempio è l'esecuzione di query sulle attività di un processo per stabilire se alcune di esse sono ancora nella coda. In alcuni casi è necessario un set di dati avanzato, mentre in altri è sufficiente il numero totale degli elementi o una raccolta di elementi con uno stato specifico.

È importante tenere presente che il numero di elementi restituiti e la quantità di dati necessari per rappresentare tali elementi possono essere molto alti. La semplice esecuzione di query su un numero elevato di elementi può generare risposte di grandi dimensioni, causando diversi problemi:

- I tempi di risposta dell'API Batch possono diventare eccessivamente lenti. Più elevato è il numero di elementi, maggiore è il tempo di esecuzione delle query richiesto dal servizio Batch. Grandi quantità di elementi devono essere suddivise in blocchi, perciò è possibile che la libreria client debba eseguire più chiamate API al servizio per ottenere tutti gli elementi per un singolo elenco.
- Più elevato è il numero di elementi da elaborare, maggiore sarà il tempo di elaborazione dell'API da parte dell'applicazione che chiama Batch.
- In presenza di grandi quantità di elementi o di elementi di dimensioni più elevate, l'applicazione che chiama il servizio Batch userà una maggiore quantità di memoria.
- Grandi quantità di elementi o elementi di dimensioni più elevate determinano un aumento del traffico di rete. Questo allunga i tempi di trasferimento e, a seconda dell'architettura dell'applicazione, può determinare un aumento dei costi di rete per i dati trasferiti all'esterno dell'area dell'account Batch.

> [AZURE.IMPORTANT]Per garantire la massima efficienza e le prestazioni dell'applicazione, per le chiamate API di tipo elenco è *consigliabile* usare *sempre* clausole FILTER e SELECT. Queste clausole e il relativo utilizzo sono descritti di seguito.

Per tutte le API di Azure Batch vale quanto segue:

- Ogni nome di proprietà è una stringa che esegue il mapping alla proprietà dell'oggetto
- Per tutti i nomi di proprietà viene fatta distinzione tra maiuscole e minuscole, al contrario di quanto avviene per i valori delle proprietà
- I nomi di proprietà e le maiuscole/minuscole devono corrispondere agli elementi visualizzati nell'API Batch REST
- Le stringhe relative a data e ora possono essere specificate in uno dei due formati consentiti e devono essere precedute da DateTime
	- W3CDTF (ad esempio: *creationTime gt DateTime'2011-05-08T08:49:37Z'*)
	- RFC1123 (ad esempio: *creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'*)
- Le stringhe booleane hanno il valore "true" o "false"
- Se viene specificata una proprietà o un operatore non valido, viene restituito un errore con il codice "400 (Richiesta non valida)"

## Esecuzione efficiente di query in Batch .NET

L'API Batch .NET consente di ridurre sia il numero di elementi restituiti in un elenco sia la quantità di informazioni restituite per ogni elemento specificando l'oggetto [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) di una query. DetailLevel è una classe base astratta che richiede che venga effettivamente creato un oggetto [ODATADetailLevel][odata] che viene passato come parametro ai metodi appropriati.

L'oggetto ODataDetailLevel presenta tre proprietà di stringa pubblica che possono essere specificate nel costruttore o impostate direttamente:

- [FilterClause](#filter): filtra e potenzialmente riduce il numero di elementi restituiti
- [SelectClause](#select): consente di specificare un sottoinsieme di valori della proprietà da restituire per ogni elemento, riducendo le dimensioni dell'elemento e della risposta
- [ExpandClause](#expand): restituisce tutti i dati richiesti in una sola chiamata anziché in più chiamate

> [AZURE.TIP]È possibile limitare la quantità di dati restituita passando un'istanza di DetailLevel configurata con le clausole SELECT ed EXPAND ai metodi GET appropriati, ad esempio [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx).

### <a id="filter"></a> FilterClause

Il numero di elementi restituito può essere ridotto da una stringa filtro. È possibile specificare uno o più valori della proprietà con qualificatori per garantire che vengano restituiti solo gli elementi pertinenti alla query. Ad esempio, è possibile ottenere un elenco contenente solo le attività in esecuzione per un processo o un elenco contenente solo i nodi di calcolo pronti per l'esecuzione di attività.

 [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) è una stringa costituita da una o più espressioni, con un'espressione costituta da un *nome di proprietà*, un *operatore* e un *valore*. Le proprietà che è possibile indicare sono specifiche di ciascuna chiamata all'API, così come gli operatori supportati per ogni proprietà. È possibile combinare più espressioni usando gli operatori logici **and** e **or**.

Questo filtro, ad esempio, restituisce solo le attività in esecuzione il cui attributo *displayName* inizia con "MyTask":

	startswith(displayName, 'MyTask') and (state eq 'Running')

Ogni articolo relativo all'API Batch REST contiene una tabella che specifica le proprietà e le operazioni supportate su tali proprietà per le diverse operazioni di tipo elenco.

- [Elencare i pool in un account](https://msdn.microsoft.com/library/azure/dn820101.aspx)
- [Elencare i nodi di calcolo in un pool](https://msdn.microsoft.com/library/azure/dn820159.aspx)
- [Elencare i processi in un account](https://msdn.microsoft.com/library/azure/dn820117.aspx)
- [Elencare lo stato della preparazione del processo e le attività di rilascio di un processo per un processo](https://msdn.microsoft.com/library/azure/mt282170.aspx)
- [Elencare le pianificazioni dei processi in un account](https://msdn.microsoft.com/library/azure/mt282174.aspx)
- [Elencare i processi associati a una pianificazione di processi](https://msdn.microsoft.com/library/azure/mt282169.aspx)
- [Elencare le attività associate a un processo](https://msdn.microsoft.com/library/azure/dn820187.aspx)
- [Elencare i file associati a un'attività](https://msdn.microsoft.com/library/azure/dn820142.aspx)
- [Elencare i certificati in un account](https://msdn.microsoft.com/library/azure/dn820154.aspx)
- [Elencare i file in un nodo](https://msdn.microsoft.com/library/azure/dn820151.aspx)

> [AZURE.IMPORTANT]Quando si specificano le proprietà in uno dei tre tipi di clausole, assicurarsi che il nome della proprietà e le lettere maiuscole/minuscole corrispondano alle relative controparti nell'API Batch REST. Ad esempio, quando si usa l'oggetto [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) .NET, è necessario specificare **state** invece di **State** anche se la proprietà .NET è [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Per verificare il nome e le lettere maiuscole/minuscoli corretti per la proprietà **state**, sarà necessario controllare il nome dell'elemento in [Ottenere le informazioni su un'attività](https://msdn.microsoft.com/library/azure/dn820133.aspx) nella documentazione dell'API Batch REST.

### <a id="select"></a> SelectClause

È possibile limitare i valori di proprietà restituiti per ogni elemento usando una stringa di selezione. È possibile specificare un elenco di proprietà per un determinato elemento. Verranno restituiti solo i valori di tali proprietà.

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) è una stringa costituita da un elenco di nomi di proprietà separati da virgole. È possibile specificare qualsiasi combinazione di proprietà disponibile per un elemento restituito da un'operazione elenco.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

È possibile ridurre il numero di chiamate API usando una clausola EXPAND. È possibile ottenere informazioni più dettagliate per ogni elemento dell'elenco con una singola chiamata API, anziché ottenere prima l'elenco, scorrerlo e quindi effettuare una chiamata per ogni elemento incluso in quest'ultimo.

[ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) è simile alla clausola SELECT per il fatto che controlla se determinati dati vengono restituiti nei risultati. La clausola EXPAND è supportata solo per le richieste di elenco relative a processi, attività e pool e attualmente fornisce soltanto informazioni statistiche. Quando tutte le proprietà sono obbligatorie e non è stata specificata alcuna clausola SELECT, è necessario usare la clausola EXPAND per ottenere informazioni statistiche. Se viene usata una clausola SELECT per ottenere un sottoinsieme di proprietà, è possibile specificare "stats" nella clausola SELECT proprietà e lasciare la clausola EXPAND impostata su Null.

## Esempio di query efficiente

Di seguito è riportato un frammento di codice che usa l'API Batch .NET per eseguire query efficienti sul servizio Batch per ottenere le statistiche di un set di pool specificato. In questo scenario, l'utente Batch ha un pool di test e un pool di produzione. Gli ID del pool di test hanno il prefisso "test" mentre gli ID del pool di produzione hanno il prefisso "prod". Nel frammento di codice, *myBatchClient* è un'istanza correttamente inizializzata di [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient):

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// We want to pull only the "test" pools, so we limit the items returned by using a Filterclause and
	// specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = myBatchClient.PoolOperations.ListPools(detailLevel).ToList();

## Progetto di esempio

Estrarre il progetto di esempio [EfficientListQueries][efficient_query_sample] su GitHub per vedere come un’esecuzione di query efficiente dell’elenco può influire sulle prestazioni in un'applicazione. Questa applicazione console C# crea e aggiunge un numero elevato di attività a un processo, quindi esegue una query nel servizio Batch utilizzando diverse specifiche [ODATADetailLevel][odata], visualizzando un output simile al seguente:

		Adding 5000 tasks to job jobEffQuery...
		5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

		4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
		0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
		59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
		5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
		5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
		5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

		Sample complete, hit ENTER to continue...

Come illustrato nelle informazioni del tempo trascorso, limitare le proprietà e il numero di elementi restituiti può ridurre notevolmente i tempi di risposta alla query. È possibile trovare questo e altri progetti di esempio nell’archivio [esempi di batch azure][github_samples] su GitHub.

## Passaggi successivi

1. Se non è già stato fatto, leggere la documentazione relativa all'API Batch corrispondente al proprio scenario di sviluppo
    - [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)
    - [Batch .NET](https://msdn.microsoft.com/library/azure/dn865466.aspx)
2. Scaricare gli [esempi per Azure Batch](https://github.com/Azure/azure-batch-samples) da GitHub ed esaminare il codice

[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx

<!---HONumber=Oct15_HO1-->