<properties 
	pageTitle="Introduzione a Microsoft Azure DocumentDB | Azure" 
	description="Informazioni su Azure DocumentDB, un account del database di documenti NoSQL, e su quali vantaggi può offrire alle applicazioni cloud e mobili. Viene inoltre illustrato come gestire i dati e come usarlo nello sviluppo di applicazioni." 
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
	ms.date="05/20/2015" 
	ms.author="mimig"/>

#Introduzione a Microsoft Azure DocumentDB

Questo articolo fornisce un'introduzione a Microsoft Azure DocumentDB, un servizio di database di documenti NoSQL per sviluppatori, professionisti IT e decisori aziendali.

Si consiglia di guardare prima di tutto il seguente video introduttivo di due minuti e di visitare la pagina [Query Playground](http://www.documentdb.com/sql/demo), in cui è possibile provare DocumentDB ed eseguire query SQL sul set di dati predefinito.

> [AZURE.VIDEO what-is-azure-documentdb]

Tornare quindi a questo articolo, che fornisce risposte alle domande seguenti:

-	[Che cos'è DocumentDB e che valore fornisce alle applicazioni cloud e mobili?](#what-is-docdb)
-	[Come vengono gestiti i dati in DocumentDB e come è possibile accedervi?](#data-management)
-	[Come si sviluppano le applicazioni usando DocumentDB?](#develop)
-	[Quali altri passaggi è necessario seguire per creare un'applicazione DocumentDB?](#next-steps)  

##<a name="what-is-docdb"></a>Che cos'è Azure DocumentDB?  

Le applicazioni attuali producono, usano e rispondono rapidamente a volumi di dati molto elevati. Queste applicazioni si evolvono molto rapidamente, insieme allo schema di dati sottostante. Gli sviluppatori scelgono quindi con maggiore frequenza database di documenti NoSQL privi di schema come soluzioni semplici, veloci e scalabili per archiviare ed elaborare i dati, mantenendo al tempo stesso la capacità di iterazione rapida su modelli di dati e di feed di dati non strutturati. Molti database privi di schema, tuttavia, non permettono query complesse ed elaborazione transazionale, complicando la gestione avanzata dei dati. Per risolvere questo problema, è possibile usare DocumentDB. Microsoft ha sviluppato DocumentDB per soddisfare questi requisiti quando si gestiscono dati per le applicazioni odierne.

DocumentDB è un servizio di database di documenti NoSQL progettato per applicazioni mobili e Web attuali. DocumentDB offre letture e scritture con velocità costante, flessibilità dello schema e possibilità aumentare o ridurre le dimensioni di un database su richiesta. DocumentDB permette l'esecuzione di query ad hoc complesse tramite un linguaggio SQL, supporta livelli di coerenza ben definiti e offre l'elaborazione di transazioni su più documenti integrata con il linguaggio JavaScript tramite il familiare modello di programmazione di stored procedure, trigger e funzioni definite dall'utente (UDF, User Defined Functions).

DocumentDB supporta in modalità nativa i documenti JSON, permettendo la semplice iterazione degli schemi applicazione. È compatibile con l'ubiquità di JSON e JavaScript, eliminando i problemi di mancata corrispondenza tra oggetti definiti dall'applicazione e schema del database. La profonda integrazione di JavaScript permette anche agli sviluppatori di eseguire la logica dell'applicazione in modo efficiente, direttamente nel motore di database in una transazione di database.

Azure DocumentDB offre le funzionalità chiave e i vantaggi seguenti:

-	**Query ad hoc con sintassi SQL familiare:** è possibile archiviare documenti JSON eterogenei in DocumentDB ed eseguire query su questi documenti tramite una sintassi SQL familiare. DocumentDB usa una tecnologia di indicizzazione a simultaneità elevata, priva di blocchi, strutturata con log per l'indicizzazione automatica dell'intero contenuto dei documenti. Ciò permette l'esecuzione di query avanzate in tempo reale, senza dovere specificare hint di schema, indici secondari o visualizzazioni.

-	**Esecuzione di JavaScript nel database:** è possibile esprimere la logica dell'applicazione sotto forma di stored procedure, trigger e funzioni definite dall'utente (UDF, User Defined Functions) tramite JavaScript standard. Ciò permette alla logica dell'applicazione di operare sui dati JSON, senza preoccuparsi della mancata corrispondenza tra l'applicazione e lo schema di database. DocumentDB offre l'esecuzione transazionale completa della logica dell'applicazione JavaScript direttamente nel motore di database. La profonda integrazione di JavaScript permette l'esecuzione di operazioni INSERT, REPLACE, DELETE e SELECT da un programma JavaScript come transazione isolata.

-	**Livelli di coerenza perfezionabili:** è possibile scegliere tra quattro livelli di coerenza ben definiti per ottenere il compromesso ottimale tra coerenza e prestazioni. Per query e operazioni di lettura, DocumentDB offre quattro livelli di coerenza distinti, ovvero avanzata, con obsolescenza associata, sessione e futura. Questi livelli di coerenza granulari e ben definiti permettono di ottenere compromessi efficaci tra coerenza, disponibilità e latenza.

-	**Completamente gestito:** è possibile eliminare la necessità di gestire risorse di database e computer. Questo servizio Microsoft Azure completamente gestito non richiede la gestione di macchine virtuali, la distribuzione e la configurazione di software o la gestione di aggiornamenti complessi per i livelli di dati. Il backup di ogni database è eseguito automaticamente e ogni database è protetto da errori nell'area geografica specifica. La possibilità di aggiungere con facilità account e capacità di provisioning di DocumentDB in base alla necessità permette di concentrarsi sull'applicazione invece che sul funzionamento e sulla gestione del database.

-	**Velocità effettiva e risorse di archiviazione a scalabilità flessibile:** è possibile aumentare o ridurre le dimensioni di DocumentDB in base alle esigenze dell'applicazione. La scalabilità è applicata tramite unità con granularità fine (raccolte) di archiviazione e velocità effettiva basate su SSD. È possibile aumentare o ridurre le dimensioni di DocumentDB in modo flessibile con prestazioni prevedibili tramite la creazione di altre unità, in base all'evoluzione dell'applicazione.

-	**Progettazione aperta:** è possibile iniziare subito a lavorare usando le competenze e gli strumenti esistenti. La programmazione per DocumentDB è semplice, intuitiva e non richiede l'adozione di nuovi strumenti o l'adesione a estensioni personalizzate per JSON o JavaScript. È possibile accedere a tutte le funzionalità del database, incluso CRUD, l'elaborazione di JavaScript e query in una semplice interfaccia HTTP RESTful. DocumentDB accetta i formati, i linguaggi e gli standard esistenti, offrendo al tempo stesso capacità di database di livello elevato.

È possibile usare DocumentDB per archiviare set di dati flessibili, che richiedono il recupero di query e l'elaborazione transazionale. Gli scenari relativi alle applicazioni possono includere i dati utente per applicazioni Web e mobili interattive, oltre ad archiviazione, recupero ed elaborazione di dati JSON dell'applicazione. Un database può archiviare un numero qualsiasi di documenti JSON. DocumentDB è quindi ideale per applicazioni eseguite in modalità scalabile su Internet.

##<a name="data-management"></a>Risorse di Azure DocumentDB
Azure DocumentDB gestisce i dati tramite risorse di database ben definite. Queste risorse sono replicate per ottenere disponibilità elevata e sono indirizzabili in modo univoco da parte dei rispettivi URI logici. DocumentDB offre un semplice modello di programmazione RESTful basato su HTTP per tutte le risorse.

L'account di database DocumentDB è uno spazio dei nomi univoco che offre l'accesso ad Azure DocumentDB. Prima di creare un account di database, è necessario avere una sottoscrizione di Azure, che consente di accedere ai diversi servizi di Azure.

Tutte le risorse disponibili in DocumentDB sono modellate e archiviate come documenti JSON. Le risorse sono gestite come elementi, ovvero documenti JSON contenenti metadati, e come feed, ovvero raccolte di elementi. Gli insiemi di elementi sono inclusi nei rispettivi feed.

La figura seguente mostra le relazioni tra le risorse di DocumentDB:

![][1]

Un account di database è costituito da un insieme di database, ognuno dei quali include più raccolte, che possono contenere stored procedure, trigger, UDF, documenti e allegati correlati. Un database include anche utenti associati, ognuno dei quali ha un insieme di autorizzazioni per accedere alle diverse raccolte, stored procedure, trigger, UDF, documenti o allegati disponibili. Mentre i database, gli utenti, le autorizzazioni e le raccolte sono risorse definite dal sistema con schemi noti, i documenti, le stored procedure, i trigger, le funzioni UDF e gli allegati includono contenuto JSON arbitrario definito dagli utenti.

##<a name="develop"></a>Sviluppo per Azure DocumentDB
Azure DocumentDB espone risorse tramite un'API REST che può essere chiamata da qualsiasi linguaggio in grado di effettuare richieste HTTP/HTTPS. In DocumentDB sono inoltre disponibili librerie di programmazione per diversi linguaggi comuni. Queste librerie semplificano molti aspetti dell'uso di Azure DocumentDB tramite la gestione di dettagli quali la memorizzazione nella cache, la gestione delle eccezioni, l'esecuzione automatica di nuovi tentativi e così via. Le librerie attualmente disponibili riguardano i linguaggi e le piattaforme seguenti, ma presto ne saranno disponibili altre:

- [.NET](http://go.microsoft.com/fwlink/?LinkID=402989)  
- [Node.JS](http://go.microsoft.com/fwlink/?LinkID=402990)
- [Java](http://go.microsoft.com/fwlink/?LinkID=402380)
- [JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991)
- [Python](http://go.microsoft.com/fwlink/?LinkID=402992)

Oltre alle operazioni di base di creazione, lettura, aggiornamento ed eliminazione, Azure DocumentDB offre un'interfaccia di query SQL query avanzata per il recupero di documenti JSON e supporto lato server per l'esecuzione transazionale di logica dell'applicazione JavaScript. Le interfacce di esecuzione di query e script sono disponibili tramite tutte le librerie delle piattaforme, oltre alle API REST.

###Query SQL
Azure DocumentDB supporta l'esecuzione di query sui documenti tramite un linguaggio SQL basato sul sistema di tipi JavaScript ed espressioni con supporto per query gerarchiche avanzate. Il linguaggio di query di DocumentDB è un'interfaccia semplice ma avanzata per l'esecuzione di query su documenti JSON. Il linguaggio supporta un sottoinsieme della grammatica SQL ANSI e aggiunge un'integrazione profonda di oggetti JavaScript, matrici, costruzione di oggetti e chiamata di funzioni. DocumentDB offre il modello di query senza schema esplicito o hint di indicizzazioni dallo sviluppatore.

Le funzioni UDF possono essere registrate con DocumentDB ed è possibile farvi riferimento come parte di una query SQL, estendendo così la grammatica in modo da supportare la logica dell'applicazione. Queste funzioni UDF sono scritte come programmi JavaScript e sono eseguite nel database.

Per gli sviluppatori .NET, DocumentDB offre anche un provider LINQ come parte di .NET SDK.

###Transazioni ed esecuzione di JavaScript
DocumentDB permette di scrivere logica dell'applicazione sotto forma di programmi con nome, scritti interamente in JavaScript. Questi programmi sono registrati per una raccolta e possono eseguire di operazioni di database nei documenti all'interno di una raccolta specifica. Il codice JavaScript può essere registrato per l'esecuzione come trigger, stored procedure o funzione UDF. I trigger e le stored procedure possono creare, leggere, aggiornare ed eliminare documenti, mentre le funzioni UDF sono eseguite come parte della logica di esecuzione delle query, senza accesso in scrittura alla raccolta.

L'esecuzione di JavaScript in DocumentDB è modellata sui concetti supportati dai sistemi di database relazionali, in cui JavaScript rappresenta un sostituto attuale di T-SQL. Tutta la logica JavaScript è eseguita in una transazione di ambiente ACID con isolamento dello snapshot. Se JavaScript genera un'eccezione durante l'esecuzione, l'intera transazione sarà interrotta.

##<a name="next-steps"></a>Passaggi successivi
Per iniziare a usare Azure DocumentDB, vedere le risorse seguenti:

-   [Prova ora DocumentDB](https://portal.azure.com/#gallery/Microsoft.DocumentDB)
-   [Query Playground](http://www.documentdb.com/sql/demo)
-	[Modello di risorse e concetti relativi a DocumentDB](documentdb-resources.md)
-	[Interagire con risorse di DocumentDB](documentdb-interactions-with-resources.md)
-	[Creare un account di database di DocumentDB](documentdb-create-account.md)
-	[Introduzione a DocumentDB .NET SDK](documentdb-get-started.md)

[1]: ./media/documentdb-introduction/intro.png
 

<!---HONumber=July15_HO3-->