<properties 
	pageTitle="Domande sui database DocumentDB - Domande frequenti | Microsoft Azure" 
	description="Risposte alle domande frequenti sul servizio DocumentDB di Azure, servizio di database di documenti NoSQL per JSON. Rispondere a domande sui database relative a capacità, livelli delle prestazioni e scalabilità." 
	keywords="Domande sui database - Domande frequenti, documentdb, azure, Microsoft azure"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/30/2015" 
	ms.author="mimig"/>


#Domande frequenti su DocumentDB

## Domande sui database relative ai concetti di base di Microsoft Azure DocumentDB

### Che cos'è Microsoft Azure DocumentDB? 
Microsoft Azure DocumentDB è un database di documenti distribuito come servizio NoSQL con scalabilità elevata che offre funzionalità di query avanzate su dati privi di schema, che garantisce prestazioni configurabili e affidabili e che consente lo sviluppo rapido, tutto tramite una piattaforma gestita supportata dalla tecnologia e dalla copertura di Microsoft Azure. DocumentDB è la soluzione ideale per applicazioni Web e per dispositivi mobili che necessitano di velocità effettiva prevedibile, bassa latenza e un modello di dati privo di schema. DocumentDB offre flessibilità dello schema e indicizzazione avanzata tramite un modello dati JSON nativo e include il supporto transazionale per più documenti con JavaScript integrato.
  
Per altre domande sui database, risposte e istruzioni su come distribuire e usare questo servizio, vedere la [pagina relativa alla documentazione di DocumentDB](http://azure.microsoft.com/documentation/services/documentdb/).

### Qual è il tipo di database di DocumentDB?
DocumentDB è un database orientato ai documenti NoSQL che archivia i dati in formato JSON. DocumentDB supporta strutture dei dati complete nidificate su cui è possibile eseguire query tramite la [grammatica di query SQL](documentdb-sql-query.md) avanzata di DocumentDB. DocumentDB fornisce funzionalità di elaborazione transazionale con prestazioni elevate di JavaScript lato server tramite [stored procedure, trigger e funzioni definite dall'utente](documentdb-programming.md). Il database supporta inoltre livelli di coerenza ottimizzabili dagli sviluppatori, a cui sono associati [livelli di prestazioni](documentdb-performance-levels.md).
 
### I database DocumentDB contengono tabelle come i database relazionali (RDBMS)?
No, DocumentDB archivia i dati in raccolte di documenti JSON. Per altre informazioni sulle risorse di DocumentDB, vedere [Modello di risorse e concetti relativi a DocumentDB](documentdb-resources.md).

### Nei database DocumentDB sono supportati i dati privi di schema?
Sì, DocumentDB consente alle applicazioni di archiviare documenti JSON arbitrari senza hint o definizione dello schema. I dati sono immediatamente disponibili per l'esecuzione di Query tramite l'interfaccia di query del linguaggio SQL di DocumentDB.

### In DocumentDB sono supportate le transazioni ACID?
Sì, DocumentDB supporta le transazioni tra documenti espresse come trigger e stored procedure JavaScript. L'ambito delle transazioni viene impostato su una singola raccolta e le transazioni vengono eseguite con semantica ACID isolate o non isolate da altro codice e altre richieste utente simultaneamente in esecuzione. Se vengono generate eccezioni tramite l'esecuzione sul lato server del codice dell'applicazione JavaScript, viene eseguito il rollback dell'intera transazione.

### Quali sono i casi di utilizzo tipici per DocumentDB?  
DocumentDB è un'ottima scelta per le nuove applicazioni Web e per dispositivi mobili in cui sono importanti scalabilità, prestazioni e la possibilità di eseguire query su dati privi di schema. DocumentDB si presta allo sviluppo rapido e al supporto dell'iterazione continua dei modelli di dati delle applicazioni. Le applicazioni che gestiscono contenuto e dati generati dall'utente rappresentano i [casi di utilizzo comuni per DocumentDB](documentdb-use-cases.md).

### Quali sono i limiti di scala di DocumentDB?
Gli account DocumentDB possono essere scalati in termini di archiviazione e la velocità effettiva mediante l'aggiunta di insiemi. Per informazioni sulle quote del servizio relative al numero di raccolte, vedere l'articolo relativo ai [limiti di DocumentDB](documentdb-limits.md). Per aggiungere altre raccolte, [contattare il supporto tecnico](documentdb-increase-limits.md) per richiedere l'aumento della quota dell'account.

### Quando costa Microsoft Azure DocumentDB?
Per informazioni dettagliate sui prezzi, vedere la pagina relativa ai [prezzi di DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402317). Gli addebiti per l'utilizzo di DocumentDB sono determinati dal numero di raccolte in uso, dal numero di ore in cui le raccolte sono state online e dal [livello di prestazioni](documentdb-performance-levels.md) di ogni raccolta.

### È disponibile una versione di valutazione gratuita?
I nuovi utenti possono effettuare l'iscrizione alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/) che è valida 30 giorni e include un credito di 200 dollari statunitensi per provare tutti i servizi di Azure. Se si possiede una sottoscrizione a Visual Studio si ha invece diritto a [150 dollari statunitensi di crediti Azure gratuiti al mese](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) da usare per qualsiasi servizio di Azure.

### Dove è possibile reperire ulteriori informazioni e supporto su DocumentDB?
Per ottenere informazioni o supporto, è possibile visitare il sito [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb), i [forum per sviluppatori MSDN relativi ad Azure DocumentDB](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) o pianificare una [chat 1:1 con il team di progettazione di DocumentDB](http://www.askdocdb.com/). Per rimanere sempre aggiornati sulle novità e sulle funzionalità più recenti di DocumentDB, è possibile seguirci su [Twitter](https://twitter.com/DocumentDB).

## Configurare Microsoft Azure DocumentDB

### In che modo è possibile iscriversi a Microsoft Azure DocumentDB?
Microsoft Azure DocumentDB è disponibile nel [portale di Azure][azure-portal]. È innanzitutto necessario iscriversi a una sottoscrizione di Microsoft Azure. Una volta creata la sottoscrizione di Microsoft Azure, sarà possibile aggiungere un account DocumentDB alla sottoscrizione di Azure. Per istruzioni su come aggiungere un account DocumentDB, vedere [Creare un account di database di DocumentDB](documentdb-create-account.md).

### Che cos'è una chiave master?
Una chiave master è un token di sicurezza per l'accesso a tutte le risorse di un account. Gli utenti con la chiave hanno l'accesso in lettura e scrittura a tutte le risorse nell'account del database. È quindi consigliabile prestare attenzione quando si distribuiscono le chiavi master. La chiave master primaria e la chiave master secondaria sono disponibili nel pannello **Chiavi** del [portale di Azure][azure-portal]. Per altre informazioni sulle chiavi, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso](documentdb-manage-account.md#keys).

### Come si crea un database?
Per creare i database, è possibile usare il [portale di Azure](), come descritto in [Creare un database DocumentDB](documentdb-create-database.md), uno degli [SDK di DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx) o le [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### Che cos'è una raccolta?
Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata. L'ambito di query e transazioni è impostato sulle raccolte. È possibile archiviare un set di documenti JSON eterogenei in una singola raccolta, nella quale tutti i documenti vengono indicizzati automaticamente.

Le raccolte costituiscono anche le entità di fatturazione per DocumentDB. Gli addebiti mensili per l'utilizzo di DocumentDB sono determinati dal numero di raccolte in uso, dal numero di ore in cui le raccolte sono state online e dal [livello di prestazioni](documentdb-performance-levels.md) di ogni raccolta. Per altre informazioni, vedere [DocumentDB - Prezzi](https://azure.microsoft.com/pricing/details/documentdb/).

### Sono previsti limiti per i database e le raccolte?
Ogni raccolta viene fornita con un'allocazione di spazio di archiviazione di database e il provisioning di unità da elaborare in base a uno dei [livelli di prestazioni](documentdb-performance-levels.md) supportati. Sono inoltre previste quote per ogni risorsa gestita dal servizio. Per un elenco di tutti i limiti, vedere l'articolo relativo ai [limiti di DocumentDB](documentdb-limits.md). Per richiedere una modifica dei limiti dell'account, vedere [Richiedere un aumento dei limiti per l'account DocumentDB](documentdb-increase-limits.md).

### Come è possibile configurare gli utenti e le autorizzazioni?
È possibile creare gli utenti e le autorizzazioni usando uno degli [SDK di DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx) o tramite le [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

## Domande sui database relative allo sviluppo con Microsoft Azure DocumentDB

### Come è possibile iniziare a sviluppare per DocumentDB?
Sono disponibili [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) per .NET, Python, Node.js, JavaScript e Java. Gli sviluppatori possono anche usare le [API HTTP RESTful](https://msdn.microsoft.com/library/azure/dn781481.aspx) per interagire con le risorse di DocumentDB da un'ampia gamma di piattaforme e linguaggi.

Nel sito Web GitHub sono disponibili esempi per gli SDK di DocumentDB per [.NET](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](https://github.com/Azure/azure-documentdb-node/tree/master/samples) e [Python](https://github.com/Azure/azure-documentdb-python).

### In DocumentDB è supportato SQL?
Il linguaggio di query SQL di DocumentDB offre operatori avanzati gerarchici e relazionali ed estendibilità tramite funzioni definite dall'utente basate su JavaScript. La grammatica JSON consente la creazione di modelli di documenti JSON come alberi con etichette come nodi dell'albero. Questa struttura è usata dalle tecniche di indicizzazione automatica di DocumentDB e dal dialetto di query SQL di DocumentDB. Per informazioni dettagliate su come usare la grammatica SQL, vedere [Esecuzione di query tramite DocumentDB][query].

### Quali sono i tipi di dati supportati da DocumentDB?
I tipi di dati primitivi supportati in DocumentDB sono gli stessi supportati in JSON. JSON è caratterizzato da un sistema di tipo semplice costituito da stringhe, numeri (IEEE754 precisione doppia), valori booleani (True e False) e valori Null. È possibile rappresentare tipi di dati più complessi, ad esempio DateTime, Guid, Int64 e Geometry, sia in JSON che in DocumentDB tramite la creazione di oggetti annidati usando l'operatore { } e di matrici usando l'operatore [ ].

### In che modo viene fornita la concorrenza DocumentDB?
DocumentDB supporta il controllo della concorrenza ottimistica tramite tag di entità HTTP o Etag. Ogni risorsa di DocumentDB ha un ETag e i client DocumentDB includono la relativa versione di lettura più recente nelle richieste di scrittura. Se l'ETag è aggiornato, viene eseguito il commit della modifica. Se il valore è stato modificato esternamente, il server rifiuta la scrittura con un codice di risposta "HTTP 412 - Errore della condizione preliminare". I client devono leggere la versione più recente della risorsa e ripetere la richiesta.

### Come si eseguono le transazioni in DocumentDB?
DocumentDB supporta le transazioni Language Integrated tramite trigger e stored procedure JavaScript. Tutte le operazioni di database all'interno degli script sono eseguite nell'isolamento dello snapshot con ambito impostato sulla raccolta. Viene acquisito uno snapshot delle versioni (ETag) del documento all'inizio della transazione e il commit della transazione viene eseguito solo se lo script ha esito positivo. Se JavaScript genera un errore, viene eseguito il rollback della transazione. Per informazioni dettagliate, vedere l'articolo relativo alla [programmazione sul lato server di DocumentDB](documentdb-programming.md).

### Come è possibile eseguire inserimenti bulk in DocumentDB? 
Sono disponibili tre modi per eseguire l'inserimento in blocco di documenti in DocumentDB:

- Lo strumento per la migrazione dei dati, come descritto in [Importare i dati in DocumentDB](documentdb-import-data.md).
- La funzionalità Esplora documenti nel portale di Azure, come descritto in [Aggiungere documenti in blocco con Esplora documenti](documentdb-view-json-document-explorer.md#BulkAdd).
- Le stored procedure, come descritto in [Programmazione sul lato server di DocumentDB](documentdb-programming.md).

### In DocumentDB è supportata la memorizzazione nella cache dei collegamenti alle risorse?
Sì. Dal momento che DocumentDB è un servizio RESTful, i collegamenti alle risorse sono immutabili e possono essere memorizzati nella cache. I client DocumentDB possono specificare un'intestazione "If-None-Match" per le letture in ogni risorsa come documento o raccolta e aggiornare le copie locali solo quando viene modificata la versione del server.




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 

<!---HONumber=AcomDC_1203_2015-->