<properties 
	pageTitle="Test delle prestazioni e della scalabilità in DocumentDB | Microsoft Azure" 
	description="Informazioni sull'esecuzione del test delle prestazioni e della scalabilità con Azure DocumentDB"
	keywords="test delle prestazioni"
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/20/2016" 
	ms.author="arramac"/>

# Test delle prestazioni e della scalabilità con Azure DocumentDB
Il test delle prestazioni e della scalabilità è un passaggio chiave nello sviluppo di un'applicazione. Per molte applicazioni, il livello del database ha un impatto significativo sulle prestazioni e sulla scalabilità globali, è pertanto un componente fondamentale del test delle prestazioni. [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) è appositamente progettato per garantire scalabilità elastica e prestazioni prevedibili, è pertanto un candidato ideale per applicazioni che richiedono un livello elevato di prestazioni del database.

Questo è un articolo di riferimento per gli sviluppatori che intendono implementare gruppi di test delle prestazioni per i carichi di lavoro di DocumentDB o valutare DocumentDB per scenari di applicazioni ad alte prestazioni. Approfondisce soprattutto i test isolati delle prestazioni del database, ma include anche le procedure consigliate per le applicazioni di produzione.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

- Dove è possibile trovare un'applicazione client .NET di esempio per il test delle prestazioni di Azure DocumentDB?
- Quali sono i fattori chiave che hanno impatto sulle prestazioni end-to-end delle richieste eseguite su Azure DocumentDB? 
- Com'è possibile ottenere livelli di velocità effettiva elevati con Azure DocumentDB dall'applicazione client?

Per iniziare a usare il codice, scaricare il progetto dall'esempio relativo al [test delle prestazioni di DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark).

## Opzioni principali di configurazione del client
DocumentDB è un database distribuito rapido e flessibile, perfettamente scalabile e con latenza e velocità effettiva garantite. Non è necessario apportare modifiche significative all'architettura o scrivere codice complesso per aumentare o ridurre il livello del database con DocumentDB. Aumentare o ridurre le prestazioni è semplice come eseguire una singola chiamata API o una chiamata al metodo SDK. Tuttavia, durante il test della scalabilità, è importante notare che DocumentDB è accessibile tramite chiamate di rete. Se si sta scrivendo un'applicazione client autonoma per l'esecuzione del test delle prestazioni di DocumentDB, è necessario configurarla appropriatamente per contrastare l'impatto della latenza di rete sulle misurazioni delle prestazioni.

Per ottenere migliori prestazioni end-to-end con DocumentDB, considerare le opzioni di configurazione client seguenti:

- **Aumentare il numero di thread/attività**: poiché le chiamate a DocumentDB vengono eseguite sulla rete, può essere necessario modificare il grado di parallelismo delle richieste in modo che i tempi di attesa dell'applicazione client tra le richieste siano molto ridotti. Ad esempio, se si sta usando la [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx) di .NET, creare centinaia di attività di lettura o scrittura in DocumentDB.
- **Eseguire test nella stessa area di Azure**: quando possibile, eseguire il test da una macchina virtuale o da un servizio app distribuito nella stessa area di Azure. Per un confronto approssimativo, le chiamate a DocumentDB eseguite nella stessa area vengono completate entro 1-2 ms, ma la latenza tra la costa occidentale e quella orientale degli Stati Uniti è > 50 ms.
- **Aumentare System.Net MaxConnections per ogni host**: le richieste di DocumentDB vengono eseguite su HTTPS/REST per impostazione predefinita e sono soggette ai limiti di connessione predefiniti per ogni nome host o indirizzo IP. Può essere necessario impostare questa proprietà su un valore più alto (100-1000) in modo che la libreria client possa usare più connessioni DocumentDB contemporaneamente. In .NET questa proprietà è [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx).
- **Avviare GC sul lato server**: in alcuni casi, può essere utile ridurre la frequenza di garbage collection. In .NET impostare [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) su true.
- **Usare la connettività diretta con il protocollo TCP**: per ottenere prestazioni ottimali, usare la [connettività diretta](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionmode.aspx) con il [protocollo TCP](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.protocol.aspx). 
- **Implementare il backoff a intervalli RetryAfter**: durante il test delle prestazioni, è necessario aumentare il carico fino a limitare un numero ridotto di richieste. Se limitata, l'applicazione client deve eseguire il backoff sulla limitazione per l'intervallo tra tentativi specificato dal server. In questo modo, si garantiscono tempi di attesa minimi tra i tentativi. Vedere [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
- **Aumentare il carico di lavoro client**: se si sta eseguendo il test a livelli di velocità effettiva elevati (> 50.000 UR/s), l'applicazione client può diventare un collo di bottiglia a causa della limitazione di utilizzo della CPU o della rete. In questo caso, è possibile continuare a eseguire il push dell'account di DocumentDB tramite l'aumento delle istanze delle applicazioni client su più server.

## Introduzione
Il modo più rapido per iniziare è compilare ed eseguire l'esempio .NET riportato di seguito, come descritto nella procedura seguente. È anche possibile esaminare il codice sorgente e implementare configurazioni analoghe alle applicazioni client.

**Passaggio 1**: scaricare il progetto dall'esempio relativo al [test delle prestazioni di DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark) o creare la biforcazione del repository GitHub.

**Passaggio 2**: modificare le impostazioni di EndpointUrl, AuthorizationKey, CollectionThroughput e DocumentTemplate (facoltativo) nel file app.config.

> [AZURE.NOTE] Prima del provisioning delle raccolte con velocità effettiva elevata, consultare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/documentdb/) per stimare i costi di ogni raccolta. DocumentDB addebita l'archiviazione e la velocità effettiva in modo indipendente su base oraria, è quindi possibile risparmiare eliminando o riducendo la velocità effettiva delle raccolte DocumentDB al termine del test.

**Passaggio 3**: compilare ed eseguire l'app console dalla riga di comando. Verrà visualizzato un output simile al seguente:

	Summary:
	---------------------------------------------------------------------
	Endpoint: https://docdb-scale-demo.documents.azure.com:443/
	Collection : db.testdata at 50000 request units per second
	Document Template*: Player.json
	Degree of parallelism*: 500
	---------------------------------------------------------------------

	DocumentDBBenchmark starting...
	Creating database db
	Creating collection testdata
	Creating metric collection metrics
	Retrying after sleeping for 00:03:34.1720000
	Starting Inserts with 500 tasks
	Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
	Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
	Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
	Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
	Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
	Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
	Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
	Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
	Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
	Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
	Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
	Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
	Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
	Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
	Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
	Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
	Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
	Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
	Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
	Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
	Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
	Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

	Summary:
	---------------------------------------------------------------------
	Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
	---------------------------------------------------------------------
	DocumentDBBenchmark completed successfully.


**Passaggio 4 (se necessario)**: la velocità effettiva segnalata (UR/s) dallo strumento deve essere analoga o maggiore di quella della raccolta. In caso contrario, l'aumento di DegreeOfParallelism in incrementi ridotti può aiutare a raggiungere il limite. Se la velocità effettiva dall'app client su stabilizza, l'avvio di più istanze dell'app nelle stesse macchine o in macchine diverse consente di raggiungere il limite di provisioning tra istanze diverse. Se occorre assistenza con questi passaggi, contattare Microsoft tramite l'[apposita pagina](askdocdb@microsoft.com) o creando un ticket di supporto.

Quando l'app è in esecuzione, è possibile provare diversi [criteri di indicizzazione](documentdb-indexing-policies.md) e [livelli di coerenza](documentdb-consistency-levels.md) per comprenderne l'impatto sulla velocità effettiva e sulla latenza. È anche possibile esaminare il codice sorgente e implementare configurazioni analoghe alle suite di test o alle applicazioni di produzione.

## Riepilogo
In questo articolo è stato descritto come eseguire il test delle prestazioni e della scalabilità con DocumentDB usando un'app console .NET e opzioni di configurazione delle chiavi riesaminate per ottenere prestazioni ottimali da Azure DocumentDB. Per informazioni aggiuntive, fare riferimento ai collegamenti seguenti sull'utilizzo di DocumentDB.

* [Esempio di test delle prestazioni in DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Partizionamento lato server in DocumentDB](documentdb-partition-data.md)
* [Raccolte e livelli di prestazioni in DocumentDB](documentdb-performance-levels.md)
* [Documentazione di DocumentDB .NET SDK in MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Esempi di .NET in DocumentDB](https://github.com/Azure/azure-documentdb-net)
* [Blog di DocumentDB sui suggerimenti per le prestazioni](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)

<!---HONumber=AcomDC_0525_2016-->