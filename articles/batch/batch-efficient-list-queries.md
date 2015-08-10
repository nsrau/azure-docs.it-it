<properties
	pageTitle="Query di elenco efficienti"
	description="Informazioni su come ridurre il numero di elementi restituiti in un elenco e la quantità di informazioni restituite per ogni elemento"
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# Query di elenco efficienti

I metodi descritti di seguito sono esempi di operazioni che praticamente tutte le applicazioni che usano Azure Batch devono eseguire, in molto casi anche di frequente:

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listjobs.aspx)
- [ListWorkitems](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listworkitems.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.listpools.aspx)
- [ListCertificates](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icertificatemanager.listcertificates.aspx)

Il monitoraggio è un caso di uso comune. La determinazione della capacità e dello stato di un pool, ad esempio, richiede l'esecuzione di query su tutte le macchine virtuali del pool. Un altro esempio è l'esecuzione di query relative alle attività di un processo per stabilire se qualcuna è ancora in coda. In alcuni casi è necessario un ampio set di dati, ma in altri casi è sufficiente il numero totale degli elementi o quello degli elementi che si trovano in un determinato stato.

È importante tenere presente che il numero degli elementi restituiti può essere molto alto. Di conseguenza, anche la dimensione dei dati necessari per rappresentare l'elenco di elementi può essere notevole. La semplice esecuzione di query su un numero elevato di elementi può generare risposte di grandi dimensioni, causando diversi problemi:

- I tempi di risposta dell'API Batch possono diventare eccessivamente lenti. Più elevato è il numero di elementi, maggiore è il tempo di esecuzione delle query richiesto dal servizio Batch. Quando il numero di elementi restituiti è particolarmente elevato, è necessario suddividerli in blocchi. Di conseguenza, è possibile che la libreria client debba eseguire più chiamate all'API del servizio per ottenere tutti gli elementi relativi a un unico elenco.
- Più elevato è il numero di elementi da elaborare, maggiore sarà il tempo di elaborazione dell'API da parte dell'applicazione che chiama Batch.
- Più elevato è il numero degli elementi o più grandi le relative dimensioni, maggiore sarà la quantità di memoria utilizzata nell'applicazione che chiama Batch.
- L'elevato numero e/o le grandi dimensioni degli elementi portano a un aumento del traffico di rete. Questo allunga i tempi di trasferimento e, a seconda dell'architettura dell'applicazione, può determinare un aumento dei costi di rete per i dati trasferiti all'esterno dell'area dell'account Batch.

L'API Batch offre la possibilità di ridurre sia il numero di elementi restituiti in un elenco sia la quantità di informazioni restituite per ogni elemento. Per le operazioni di elenco è possibile specificare un parametro di tipo [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx). DetailLevel è una classe base astratta. È necessario che venga effettivamente creato un oggetto [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) e che tale oggetto venga passato come parametro.

Per tutte le API vale quanto riportato di seguito:

- Ogni nome di proprietà è una stringa che esegue il mapping alla proprietà dell'oggetto
- Per tutti i nomi di proprietà viene fatta distinzione tra maiuscole e minuscole, al contrario di quanto avviene per i valori delle proprietà
- Le stringhe relative alla data e all'ora possono avere uno o due formati e devono essere precedute da DateTime
	- W3CDTF (ad esempio creationTime gt DateTime’2011-05-08T08:49:37Z’)
	- RFC1123 (ad esempio creationTime gt DateTime’Sun, 08 May 2011 08:49:37 GMT’)
- Le stringhe booleane hanno il valore "true" o "false"
- Se si specifica una proprietà o un operatore non valido, verrà creata un'eccezione con eccezione interna "400 (Richiesta non valida)".
- È anche possibile passare il parametro DetailLevel con clausole di tipo Select ed Expand ai metodi “Get” appropriati, ad esempio IPoolManager.GetPool()

L'oggetto ODataDetailLevel presenta tre proprietà pubbliche che possono essere specificate nel costruttore o impostate direttamente. Le tre proprietà sono tutte stringhe:

- [FilterClause](#filter): filtra e potenzialmente riduce il numero di elementi restituiti
- [SelectClause](#select): consente di indicare i valori di proprietà specifici che vengono restituiti, riducendo le dimensioni dell'elemento e della risposta
- [ExpandClause](#expand): restituisce tutti i dati richiesti in una sola chiamata anziché in più chiamate

### <a id="filter"></a> FilterClause

Il numero di elementi restituito può essere ridotto da una stringa filtro. Per assicurare che vengano restituiti solo gli elementi richiesti, è possibile specificare uno o più valori di proprietà. Ad esempio, è possibile elencare soltanto gli elementi di lavoro attivi, le attività in esecuzione per un processo e le macchine virtuali pronte per l'esecuzione di attività.

[FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) è una stringa costituita da una o più espressioni, ciascuna delle quali composta da un nome di proprietà, un operatore e un valore. Le proprietà che è possibile indicare sono specifiche di ciascuna chiamata all'API, così come gli operatori supportati per ogni proprietà. È possibile combinare più espressioni usando gli operatori logici “and” e “or”.

Di seguito è riportato un possibile filtro per l'elenco di attività:

	startswith(name, 'MyTask') and (state eq 'Running')

### <a id="select"></a> SelectClause

È possibile limitare i valori di proprietà restituiti per ogni elemento usando una stringa di selezione. È possibile specificare un elenco di proprietà per un determinato elemento. Verranno restituiti solo i valori di tali proprietà.

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) è una stringa costituita da un elenco di nomi di proprietà separati da virgole. È possibile specificare tutte le proprietà dell'elemento restituite dall'operazione di elenco.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

È possibile ridurre il numero di chiamate all'API usando una stringa di espansione. È possibile ottenere informazioni più dettagliate per ogni elemento dell'elenco con un'unica chiamata all'API elenco, anziché ottenere l'elenco e quindi effettuare una chiamata per ogni elemento incluso in quest'ultimo.

La proprietà [ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx), simile a SelectClause, controlla se determinati dati vengono restituiti nei risultati. La proprietà ExpandClause è supportata soltanto per l'elenco degli elementi di lavoro, l'elenco delle proprietà e quello dei processi. Attualmente fornisce soltanto informazioni statistiche. Quando sono necessarie tutte le proprietà e non è presente SelectClause, per ottenere informazioni statistiche è necessario usare ExpandClause. Se si usa SelectClause per ottenere un sottoinsieme di proprietà, è possibile specificare le statistiche in tale proprietà e lasciare ExpandClause impostata su null.

> [AZURE.NOTE]Per assicurare la massima efficienza e le migliori prestazioni dell'applicazione, per le chiamate all'API elenco è consigliabile usare sempre clausole di filtro e selezione.

<!---HONumber=July15_HO5-->