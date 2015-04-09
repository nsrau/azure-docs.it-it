<properties 
	pageTitle="Domande frequenti su DocumentDB | Azure" 
	description="Risposte alle domande frequenti sul servizio database di documenti NoSQL Azure DocumentDB. Informazioni sulle unità di capacità e richiesta e su come eseguire la scalabilità in base alle esigenze dell'applicazione." 
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
	ms.date="03/20/2015" 
	ms.author="mimig"/>


#Domande frequenti su DocumentDB

## <a id="fundamentals"></a> Dati fondamentali di Microsoft Azure DocumentDB

###Che cos'è Microsoft Azure DocumentDB? 
Microsoft Azure DocumentDB è un database di documenti distribuito come servizio NoSQL con scalabilità elevata che offre funzionalità di query avanzate su dati privi di schema, che garantisce prestazioni configurabili e affidabili e che consente lo sviluppo rapido, tutto tramite una piattaforma gestita supportata dalla tecnologia e dalla copertura di Microsoft Azure. DocumentDB è la soluzione ideale per applicazioni Web e per dispositivi mobili che necessitano di velocità effettiva prevedibile, bassa latenza e un modello di dati privo di schema. DocumentDB offre flessibilità dello schema e indicizzazione avanzata tramite un modello dati JSON nativo e include il supporto transazionale per più documenti con JavaScript integrato.  
  
Per istruzioni su come distribuire e usare questo servizio, vedere la pagina relativa alla [documentazione di DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).

###Qual è il tipo di database di DocumentDB?
DocumentDB è un database orientato ai documenti NoSQL che archivia i dati in formato JSON.  DocumentDB supporta strutture dei dati complete nidificate su cui è possibile eseguire query tramite la grammatica di query SQL avanzata di DocumentDB. DocumentDB fornisce funzionalità di elaborazione transazionale con prestazioni elevate di JavaScript lato server tramite stored procedure, trigger e funzioni definite dall'utente. Il database supporta inoltre livelli di coerenza ottimizzabili dagli sviluppatori, a cui sono associati livelli di prestazioni.
 
###I database DocumentDB contengono tabelle come i sistemi RDBMS?
No, DocumentDB archivia i dati in raccolte di documenti JSON.  Per altre informazioni sulle risorse di DocumentDB, vedere l'articolo [Modello di risorse e concetti relativi a DocumentDB](documentdb-resources.md). 

###Nei database DocumentDB sono supportati i dati privi di schema?
Sì, DocumentDB consente alle applicazioni di archiviare documenti JSON arbitrari senza hint o definizione dello schema. I dati sono immediatamente disponibili per l'esecuzione di Query tramite l'interfaccia di query del linguaggio SQL di DocumentDB.   

###In DocumentDB sono supportate le transazioni ACID?
Sì, DocumentDB supporta le transazioni tra documenti espresse come trigger e stored procedure JavaScript. L'ambito delle transazioni viene impostato su una singola raccolta e le transazioni vengono eseguite con semantica ACID isolate o non isolate da altro codice e altre richieste utente simultaneamente in esecuzione.  Se vengono generate eccezioni tramite l'esecuzione sul lato server del codice dell'applicazione JavaScript, viene eseguito il rollback dell'intera transazione. 

###Quali sono i casi di utilizzo tipici per DocumentDB?  
DocumentDB è un'ottima scelta per le nuove applicazioni Web e per dispositivi mobili in cui sono importanti scalabilità, prestazioni e la possibilità di eseguire query su dati privi di schema. DocumentDB si presta allo sviluppo rapido e al supporto dell'iterazione continua dei modelli di dati delle applicazioni. Le applicazioni che gestiscono contenuto e dati generati dall'utente rappresentano i casi di utilizzo comuni per DocumentDB.  

###Quali sono i limiti in termini di scalabilità e capacità?
Ogni account DocumentDB Azure supporta un numero massimo di unità di capacità che è possibile configurare tramite il portale di Azure. Per aggiungere altre unità di capacità, contattare il supporto tecnico per richiedere l'aumento della quota dell'account.  Per altre informazioni sulle quote delle risorse, vedere l'articolo [Limiti per la versione preliminare di DocumentDB](documentdb-limits.md).


###Quando costa Microsoft Azure DocumentDB?
Per informazioni sui prezzi, vedere l'articolo relativo ai [dettagli prezzi di DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402317).

## <a id="setup"></a> Configurare Microsoft Azure DocumentDB

###In che modo è possibile iscriversi a Microsoft Azure DocumentDB?
Microsoft Azure DocumentDB (anteprima) è disponibile nel nuovo [portale di anteprima di Azure][preview-portal].  È innanzitutto necessario iscriversi a una sottoscrizione di Microsoft Azure.  Dopo aver completato l'iscrizione a una sottoscrizione di Microsoft Azure, è possibile aggiungere un account DocumentDB alla sottoscrizione di Azure tramite il Marketplace.   

###Che cos'è una chiave master?
Una chiave master è un token di sicurezza per l'accesso a tutte le risorse di un account. Gli utenti con la chiave hanno l'accesso in lettura e scrittura a tutte le risorse nell'account del database. È quindi consigliabile prestare attenzione quando si distribuiscono le chiavi master. La chiave master primaria e la chiave master secondaria sono disponibili nel pannello Chiavi del [portale di anteprima di Azure][preview-portal].

###Come si crea un database?
È possibile creare i database usando uno degli SDK di DocumentDB o tramite le API REST.  Per informazioni su come sviluppare le applicazioni, vedere la sezione Sviluppo nella [pagina della documentazione di DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319). 

###Che cos'è una raccolta?
Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata. L'ambito di query e transazioni è impostato sulle raccolte. È possibile archiviare un set di documenti JSON eterogenei in una singola raccolta, nella quale tutti i documenti vengono indicizzati automaticamente.   

###Sono previsti limiti per i database e le raccolte?
Ogni unità di capacità acquistata include un'allocazione di spazio di archiviazione database e di velocità effettiva sottoposta a provisioning. Sono inoltre previste quote per ogni risorsa gestita dal servizio. Per altre informazioni, vedere l'articolo [Limiti per la versione preliminare di DocumentDB](documentdb-limits.md).  

###Come è possibile configurare gli utenti e le autorizzazioni?
È possibile creare gli utenti e le autorizzazioni usando uno degli SDK di DocumentDB o tramite le API REST. Per informazioni dettagliate su come sviluppare le applicazioni, vedere la sezione Sviluppo nella [pagina della documentazione di DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).  


## <a id="develop"></a>Sviluppare per Microsoft Azure DocumentDB

###Come è possibile iniziare a sviluppare per DocumentDB?
Nell'anteprima sono disponibili gli SDK per .NET, Python, Node.js, JavaScript e Java.  Gli sviluppatori possono anche usare le API HTTP RESTful per interagire con le risorse di DocumentDB da un'ampia gamma di piattaforme e linguaggi. Per informazioni dettagliate su come usare gli SDK, vedere la sezione Sviluppo nella [pagina della documentazione di DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).

###In DocumentDB è supportato SQL?
Il linguaggio di query SQL di DocumentDB offre operatori avanzati gerarchici e relazionali ed estendibilità tramite funzioni definite dall'utente basate su JavaScript. La grammatica JSON consente la creazione di modelli di documenti JSON come alberi con etichette come nodi dell'albero. Questa struttura è usata dalle tecniche di indicizzazione automatica di DocumentDB e dal dialetto di query SQL di DocumentDB.  Per informazioni dettagliate su come usare la grammatica SQL, vedere l'articolo su come [eseguire query tramite il linguaggio SQL di DocumentDB][query].

###Quali sono i tipi di dati supportati da DocumentDB?
I tipi di dati primitivi supportati in DocumentDB sono gli stessi supportati in JSON. JSON è caratterizzato da un sistema di tipo semplice costituito da stringhe, numeri (IEEE754 precisione doppia), valori booleani (True e False) e valori Null.  È possibile rappresentare tipi di dati più complessi, ad esempio DateTime, Guid, Int64 e Geometry, sia in JSON che in DocumentDB tramite la creazione di oggetti annidati usando l'operatore { } e di matrici usando l'operatore [ ]. 

###In che modo viene fornita la concorrenza DocumentDB?
DocumentDB supporta il controllo della concorrenza ottimistica tramite tag di entità HTTP o Etag. Ogni risorsa di DocumentDB ha un ETag e i client DocumentDB includono la relativa versione di lettura più recente nelle richieste di scrittura. Se l'ETag è aggiornato, viene eseguito il commit della modifica. Se il valore è stato modificato esternamente, il server rifiuta la scrittura con un codice di risposta "HTTP 412 - Errore della condizione preliminare". I client devono leggere la versione più recente della risorsa e ripetere la richiesta. 

###Come si eseguono le transazioni in DocumentDB?
DocumentDB supporta le transazioni Language Integrated tramite trigger e stored procedure JavaScript. Tutte le operazioni di database all'interno degli script sono eseguite nell'isolamento dello snapshot con ambito impostato sulla raccolta. Viene acquisito uno snapshot delle versioni (ETag) del documento all'inizio della transazione e il commit della transazione viene eseguito solo se lo script ha esito positivo. Se JavaScript genera un errore, viene eseguito il rollback della transazione.

###Come è possibile eseguire inserimenti bulk in DocumentDB? 
DocumentDB supporta le stored procedure, che offrono una soluzione efficiente per gli inserimenti batch. È possibile eseguire inserimenti bulk sviluppando una semplice stored procedure JavaScript che accetta e inserisce documenti. Questa operazione garantisce che l'inserimento bulk venga eseguito come una transazione, lasciando la raccolta in uno stato coerente. Per informazioni dettagliate sul modello di programmazione, vedere la sezione Sviluppo nella [pagina della documentazione di DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).

###In DocumentDB è supportata la memorizzazione nella cache dei collegamenti alle risorse?
Sì. Dal momento che DocumentDB è un servizio RESTful, i collegamenti alle risorse sono immutabili e possono essere memorizzati nella cache. I client DocumentDB possono specificare un'intestazione "If-None-Match" per le letture in ogni risorsa come documento o raccolta e aggiornare le copie locali solo quando viene modificata la versione del server. 




[preview-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md

<!--HONumber=49-->