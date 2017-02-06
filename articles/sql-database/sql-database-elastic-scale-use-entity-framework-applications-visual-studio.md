---
title: Uso della libreria client dei database elastici con Entity Framework | Documentazione Microsoft
description: Usare la libreria client del database elastico e Entity Framework per la codifica di database
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: b9c3065b-cb92-41be-aa7f-deba23e7e159
ms.service: sql-database
ms.custom: multiple databases
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: torsteng
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a57ad6211403712bc35f20710e0c8c1728d2639d


---
# <a name="elastic-database-client-library-with-entity-framework"></a>Libreria client dei database elastici con Entity Framework
Questo documento illustra le modifiche necessarie in un'applicazione Entity Framework per l'integrazione con le funzionalità degli [strumenti del database elastico](sql-database-elastic-scale-introduction.md). Vengono descritti in particolare la [gestione delle mappe partizioni](sql-database-elastic-scale-shard-map-management.md) e il [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) con l'approccio **Code First** di Entity Framework. L'esercitazione [Code First per un nuovo database](http://msdn.microsoft.com/data/jj193542.aspx) per Entity Framework servirà da esempio nell'intero documento. Il codice di esempio che accompagna questo documento fa parte del set di esempi sugli strumenti dei database elastici negli esempi di codice di Visual Studio.

## <a name="downloading-and-running-the-sample-code"></a>Download ed esecuzione del codice di esempio
Per scaricare il codice per questo articolo:

* È richiesto Visual Studio 2012 o versione successiva. 
* Avviare Visual Studio. 
* In Visual Studio selezionare File -> Nuovo progetto. 
* Nella finestra di dialogo "Nuovo progetto" passare agli **esempi online** per **Visual C#** e digitare "database elastico" nella casella di ricerca in alto a destra.
  
    ![Entity Framework e applicazione di esempio dei database elastici][1] 
  
    Selezionare l'esempio denominato **Strumenti del database elastico per SQL di Azure - Integrazione con Entity Framework**. Dopo aver accettato la licenza, l'esempio verrà caricato. 

Per eseguire l'esempio è necessario creare tre database vuoti nel database SQL di Azure:

* Database di gestione delle mappe partizioni
* Database partizione 1
* Database partizione 2

Dopo aver creato questi database, riempire i segnaposto nel file **Program.cs** con il nome del server di database SQL di Azure, i nomi dei database e le credenziali per la connessione ai database. Compilare la soluzione in Visual Studio. Visual Studio scaricherà i pacchetti NuGet necessari per la libreria client dei database elastici, Entity Framework e la gestione degli errori temporanei come parte del processo di compilazione. Verificare che per la soluzione sia abilitato il ripristino dei pacchetti NuGet. Per abilitare questa impostazione, fare clic con il pulsante destro del mouse sul file di soluzione in Esplora soluzioni di Visual Studio. 

## <a name="entity-framework-workflows"></a>Flussi di lavoro di Entity Framework
Gli sviluppatori Entity Framework si basano su uno dei quattro seguenti flussi di lavoro per compilare le applicazioni e garantire la persistenza degli oggetti applicazione: 

* **Code First (nuovo database)**: lo sviluppatore Entity Framework crea il modello nel codice dell'applicazione ed Entity Framework genera il database a partire dal codice. 
* **Code First (database esistente)**: lo sviluppatore consente a Entity Framework di generare il codice dell'applicazione per il modello da un database esistente.
* **Model First**: lo sviluppatore crea il modello in Entity Framework Designer e quindi Entity Framework crea il database a partire dal modello.
* **Database First**: lo sviluppatore usa gli strumenti di Entity Framework per dedurre il modello da un database esistente. 

Tutti questi approcci si basano sulla classe DbContext per gestire in modo trasparente le connessioni di database e lo schema del database per un'applicazione. Come illustrato in dettaglio più avanti nel documento, costruttori diversi della classe base DbContext offrono livelli di controllo diversi sulla creazione delle connessioni, il bootstrap del database e la creazione dello schema. Le difficoltà sorgono principalmente dal fatto che la gestione delle connessioni di database fornita da Entity Framework si sovrappone alle funzionalità di gestione connessioni delle interfacce di routing dipendente dai dati fornite dalla libreria client dei database elastici. 

## <a name="elastic-database-tools-assumptions"></a>Presupposti degli strumenti dei database elastici
Per le definizioni dei termini, vedere il [glossario degli strumenti del database elastico](sql-database-elastic-scale-glossary.md).

La libreria client dei database elastici consente di definire partizioni dei dati applicativi denominate shardlet. Gli shardlet sono identificati da una chiave di partizionamento orizzontale e sono mappati a database specifici. Un'applicazione può disporre di tutti i database necessari e distribuire gli shardlet per fornire capacità o prestazioni sufficienti in base ai requisiti aziendali correnti. Il mapping dei valori delle chiavi di partizionamento orizzontale ai database è archiviato in una mappa partizioni fornita dalle API client dei database elastici. Questa funzionalità è denominata **Gestione mappe partizioni**. La mappa partizioni funge anche da gestore delle connessioni di database per le richieste che contengono una chiave di partizionamento orizzontale. Questa funzionalità viene definita **routing dipendente dai dati**. 

Il gestore delle mappe partizioni protegge gli utenti da visualizzazioni incoerenti nei dati degli shardlet potenzialmente provocate dall'esecuzione simultanea di operazioni di gestione degli shardlet, ad esempio la rilocazione di dati da una partizione all'altra. A tale scopo, le mappe di partizionamento gestite dalla libreria client gestiscono le connessioni al database per un'applicazione. In questo modo la funzionalità mappa partizioni può terminare automaticamente una connessione di database quando le operazioni di gestione delle partizioni potrebbero influire sullo shardlet per cui è stata creata la connessione. Questo approccio richiede l'integrazione con alcune funzionalità di Entity Framework, ad esempio la creazione di nuove connessioni a partire da una connessione esistente per verificare l'esistenza del database. In generale, è stato osservato che i costruttori DbContext standard funzionano in modo affidabile solo per le connessioni di database chiuse che possono essere clonate in sicurezza per le operazioni di Entity Framework. Il principio di progettazione dei database elastici prevede invece solo la gestione delle connessioni aperte. Si potrebbe pensare che chiudere una connessione gestita dalla libreria client prima di passarla al costruttore DbContext di Entity Framework consenta di risolvere il problema. Tuttavia, chiudendo la connessione e affidandone la riapertura a Entity Framework, si rinuncia alla convalida e ai controlli di coerenza eseguiti dalla libreria. La funzionalità per le migrazioni di Entity Framework, tuttavia, usa queste connessioni per gestire lo schema del database sottostante in modo trasparente per l'applicazione. La soluzione ideale sarebbe mantenere e combinare tutte le funzionalità della libreria client dei database elastici ed Entity Framework nella stessa applicazione. La seguente sezione illustra queste proprietà e i requisiti in maggiore dettaglio. 

## <a name="requirements"></a>Requisiti
Quando si usano sia le API della libreria client dei database elastici che di Entity Framework, si vogliono conservare le seguenti proprietà: 

* **Scalabilità orizzontale**: per aggiungere o rimuovere database dal livello dati dell'applicazione partizionata a seconda delle necessità, per soddisfare le esigenze di capacità dell'applicazione. Ciò significa controllo sulla creazione e sull'eliminazione dei database e uso delle API del gestore delle mappe partizioni del database elastico per gestire i database e i mapping degli shardlet. 
* **Coerenza**: l'applicazione usa il partizionamento orizzontale e si serve delle funzionalità di routing dipendente dai dati della libreria client. Per evitare il danneggiamento dei dati o la restituzione di risultati di query errati, le connessioni vengono gestite tramite il gestore delle mappe partizioni. In questo modo vengono mantenute anche la convalida e la coerenza.
* **Code First**: per mantenere la praticità del paradigma Code First di Entity Framework. In Code First, le classi nell'applicazione vengono mappate in modo trasparente alle strutture di database sottostanti. Il codice dell'applicazione interagisce con DbSet che mascherano la maggior parte degli aspetti coinvolti nell'elaborazione di database sottostante.
* **Schema**: Entity Framework gestisce la creazione iniziale dello schema del database e la successiva evoluzione dello schema mediante migrazioni. Conservando queste funzionalità, adattare l'applicazione in base all'evoluzione dei dati è molto semplice. 

Le seguenti informazioni aggiuntive illustrano come soddisfare questi requisiti per le applicazioni Code First usando gli strumenti dei database elastici. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Routing dipendente dai dati con DbContext di Entity Framework
Le connessioni di database con Entity Framework vengono in genere gestite tramite sottoclassi di **DbContext**. Creare le sottoclassi derivandole da **DbContext**. Questa è la posizione in cui si definiscono i **DbSet** che implementano le raccolte supportate da database di oggetti CLR per l'applicazione. Nel contesto del routing dipendente dai dati è possibile identificare diverse proprietà utili che non sono necessariamente rilevanti per altri scenari di applicazioni Code First di Entity Framework: 

* Il database esiste già ed è stato registrato nella mappa partizioni del database elastico. 
* Lo schema dell'applicazione è già stato distribuito al database (come illustrato di seguito). 
* Le connessioni di routing dipendente dai dati al database vengono gestite dalla mappa partizioni. 

Per integrare i **DbContext** con il routing dipendente dai dati per la scalabilità orizzontale:

1. Creare connessioni di database fisiche mediante le interfacce client del database elastico del gestore delle mappe partizioni 
2. Eseguire il wrapping della connessione con la sottoclasse **DbContext**
3. Passare la connessione alle classi base **DbContext** per assicurare che venga eseguita anche tutta l'elaborazione sul lato Entity Framework. 

Il seguente esempio di codice illustra questo approccio. Il codice è disponibile anche nel progetto di Visual Studio associato.

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data dependent routing. This call will open a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call will fail for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>Punti principali
* Un nuovo costruttore sostituisce il costruttore predefinito nella sottoclasse DbContext 
* Il nuovo costruttore accetta gli argomenti necessari per il routing dipendente dai dati mediante la libreria client dei database elastici:
  
  * la mappa partizioni per l'accesso alle interfacce di routing dipendente dai dati
  * la chiave di partizionamento orizzontale per l'identificazione dello shardlet
  * una stringa di connessione con le credenziali per la connessione di routing dipendente dai dati alla partizione. 
* La chiamata al costruttore di classe base accetta una deviazione in un metodo statico che esegue tutti i passaggi necessari per il routing dipendente dai dati. 
  
  * Usa la chiamata OpenConnectionForKey delle interfacce client dei database elastici sulla mappa partizioni per stabilire una connessione aperta.
  * La mappa partizioni crea la connessione aperta alla partizione che contiene lo shardlet per la chiave di partizionamento orizzontale specificata.
  * Tale connessione aperta viene nuovamente passata al costruttore di classe base di DbContext per indicare che Entity Framework deve usare quella anziché crearne automaticamente una nuova. In questo modo, la connessione è stata contrassegnata dall’API client dei database elastici al fine di garantire coerenza durante le operazioni di gestione della mappa partizioni.

Nel proprio codice usare il nuovo costruttore per la sottoclasse DbContext anziché il costruttore predefinito. Di seguito è fornito un esempio: 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

Il nuovo costruttore apre la connessione alla partizione che contiene i dati per lo shardlet identificato dal valore di **tenantid1**. Il codice nel blocco **using** resta invariato per accedere al **DbSet** per i blog usando Entity Framework sulla partizione per **tenantid1**. Questo modifica la semantica per il codice nel blocco using in modo tale che tutte le operazioni di database abbiano come ambito la partizione in cui è conservato **tenantid1** . Ad esempio, una query LINQ sul **DbSet** dei blog restituirebbe solo i blog archiviati nella partizione corrente, ma non quelli archiviati in altre partizioni.  

#### <a name="transient-faults-handling"></a>Gestione degli errori temporanei
Il team Microsoft Patterns & Practices ha pubblicato un articolo sul [blocco applicazione per la gestione degli errori temporanei](https://msdn.microsoft.com/library/dn440719.aspx). La libreria viene usata con la libreria client della scalabilità elastica in combinazione con Entity Framework. È però necessario assicurarsi che qualsiasi eccezione temporanea venga restituita in una posizione in cui ci si può accertare che dopo un errore temporaneo venga usato il nuovo costruttore, in modo che qualsiasi nuovo tentativo di connessione venga effettuato usando i costruttori modificati. In caso contrario, la connessione alla partizione corretta non è garantita, né esistono garanzie che la connessione venga mantenuta in caso di modifica della mappa partizioni. 

L'esempio di codice seguente illustra il possibile uso di un criterio di ripetizione SQL sui nuovi costruttori della sottoclasse **DbContext** : 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

**SqlDatabaseUtils.SqlRetryPolicy** nel codice precedente viene definito come **SqlDatabaseTransientErrorDetectionStrategy** con un numero di tentativi pari a 10 e un tempo di attesa tra i tentativi pari a 5 secondi. Questo approccio corrisponde alle informazioni aggiuntive per le transazioni di Entity Framework e avviate dall'utente; vedere l'argomento relativo alle limitazioni per le strategie di esecuzione con ripetizione dei tentativi (vedere [Entity Framework 6 e versioni successive)](http://msdn.microsoft.com/data/dn307226). Entrambe le situazioni richiedono che il programma applicativo controlli l'ambito in cui viene restituita l'eccezione temporanea: per riaprire la transazione o (come mostrato) ricreare il contesto dal costruttore corretto che usa la libreria client dei database elastici.

La necessità di controllare la posizione a cui si viene riportati dalle eccezioni temporanee all'interno dell'ambito preclude anche l'uso dell'oggetto **SqlAzureExecutionStrategy** in dotazione con Entity Framework. **SqlAzureExecutionStrategy** riaprirebbe una connessione, ma non userebbe **OpenConnectionForKey**, di conseguenza verrebbe ignorata qualsiasi forma di convalida eseguita come parte della chiamata **OpenConnectionForKey**. L'esempio di codice usa invece l'oggetto **DefaultExecutionStrategy** , anch'esso incluso in Entity Framework. A differenza di **SqlAzureExecutionStrategy**, funziona correttamente con i criteri di ripetizione derivanti dalla gestione degli errori temporanei. I criteri di esecuzione vengono impostati nella classe **ElasticScaleDbConfiguration** . Si noti che si è deciso di non usare **DefaultSqlExecutionStrategy** perché suggerisce l'uso di **SqlAzureExecutionStrategy** in caso di eccezioni temporanee, situazione che causerebbe un comportamento errato, come spiegato in precedenza. Per altre informazioni sui diversi criteri di ripetizione ed Entity Framework, vedere l'articolo relativo alla [resilienza delle connessioni in Entity Framework](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Riscritture dei costruttori
Gli esempi di codice precedenti illustrano le riscritture del costruttore predefinito necessarie perché l'applicazione possa usare il routing dipendente dai dati con Entity Framework. La seguente tabella generalizza questo approccio per altri costruttori. 

| Costruttore corrente | Costruttore riscritto per i dati | Costruttore base | Note |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |La connessione deve essere una funzione della mappa partizioni e della chiave di routing dipendente dai dati. È necessario ignorare la creazione automatica della connessione da parte di Entity Framework e gestire la connessione mediante la mappa partizioni. |
| MyContext(string) |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |La connessione è una funzione della mappa partizioni e della chiave di routing dipendente dai dati. Non è possibile usare una stringa di connessione o un nome di database fisso, in quanto ignoreranno la convalida da parte della mappa partizioni. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |Verrà creata la connessione per la mappa partizioni e per la chiave di partizionamento specificate con il modello fornito. Il modello compilato verrà passato al costruttore base. |
| MyContext(DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext(DbConnection, bool) |La connessione deve essere dedotta dalla mappa partizioni e dalla chiave. Non può essere fornita come input (a meno che l'input non usi già la mappa partizioni e la chiave). Il valore booleano verrà passato. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |La connessione deve essere dedotta dalla mappa partizioni e dalla chiave. Non può essere fornita come input (a meno che l'input non usi la mappa partizioni e la chiave). Il modello compilato verrà passato. |
| MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext, bool) |Il nuovo costruttore deve garantire che qualsiasi connessione in ObjectContext passato come input venga reinstradata a una connessione gestita da Scalabilità elastica. La descrizione dettagliata di ObjectContext esula dall'ambito di questo documento. |
| MyContext(DbConnection, DbCompiledModel,bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool); |La connessione deve essere dedotta dalla mappa partizioni e dalla chiave. Non può essere fornita come input (a meno che l'input non usi già la mappa partizioni e la chiave). Il modello e il valore booleano verranno passati al costruttore di classe base. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Distribuzione dello schema partizione tramite migrazioni di Entity Framework
La gestione automatica dello schema è una funzionalità disponibile in Entity Framework. Nel contesto di applicazioni che utilizzano gli strumenti dei database elastici è desiderabile mantenere questa funzionalità per il provisioning automatico dello schema alle nuove partizioni quando si aggiungono database all'applicazione partizionata. Il caso di utilizzo principale è l'aumento della capacità al livello dati per le applicazioni partizionate mediante Entity Framework. L'uso delle le funzionalità di Entity Framework per la gestione dello schema riduce le attività di amministrazione di database necessarie per un'applicazione partizionata basata su Entity Framework. 

La distribuzione dello schema tramite migrazioni di Entity Framework funziona al meglio con le **connessioni non aperte**. Si tratta di uno scenario diverso rispetto al routing dipendente dai dati, che usa la connessione aperta fornita dall’API client dei database elastici. Un’altra differenza è il requisito di coerenza: sebbene sia opportuno garantire la coerenza per tutte le connessioni di routing dipendente dai dati per la protezione dalle modifiche simultanee alla mappa partizioni, questo non rappresenta un problema nel caso della distribuzione iniziale dello schema in un nuovo database non ancora registrato nella mappa partizioni e non ancora allocato per contenere shardlet. Per questi scenari è pertanto possibile usare le normali connessioni di database anziché il routing dipendente dai dati.  

Questo conduce a un approccio in cui la distribuzione dello schema tramite migrazioni di Entity Framework è strettamente legata alla registrazione del nuovo database come partizione nella mappa partizioni dell'applicazione. L'approccio è basato sui seguenti prerequisiti: 

* Il database è già stato creato. 
* Il database è vuoto, non contiene schemi utente o dati utente.
* Il database non è ancora accessibile alle API client dei database elastici per il routing dipendente dai dati. 

Se questi prerequisiti sono soddisfatti, è possibile creare una normale connessione **SqlConnection** non aperta per avviare le migrazioni di Entity Framework per la distribuzione dello schema. Il seguente esempio di codice illustra questo approccio. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 


Questo esempio illustra il metodo **RegisterNewShard** , che registra la partizione nella mappa partizioni, distribuisce lo schema tramite migrazioni di Entity Framework e archivia il mapping di una chiave di partizionamento orizzontale nella partizione. Si basa su un costruttore della sottoclasse **DbContext** (**ElasticScaleContext** nell'esempio) che accetta come input una stringa di connessione SQL. Il codice di questo costruttore è semplice, come illustrato nel seguente esempio: 

        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // We want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 

Si sarebbe potuta usare la versione del costruttore ereditata dalla classe base, ma il codice deve garantire che per la connessione venga usato l'inizializzatore predefinito di Entity Framework. Da qui la breve deviazione nel metodo statico prima della chiamata al costruttore di classe base con la stringa di connessione. Notare che la registrazione delle partizioni deve essere eseguita in un dominio applicativo o in un processo diverso, per garantire l'assenza di conflitti con le impostazioni dell'inizializzatore per Entity Framework. 

## <a name="limitations"></a>Limitazioni
Gli approcci descritti in questo documento implicano due limitazioni: 

* Prima di usare la libreria client del database elastico, è necessario eseguire la migrazione delle applicazioni Entity Framework che usano **LocalDb** a un normale database di SQL Server. Con **LocalDb**non è possibile scalare orizzontalmente un'applicazione mediante il partizionamento orizzontale con la scalabilità elastica. Per lo sviluppo è comunque possibile usare **LocalDb**. 
* Le modifiche all'applicazione che implicano modifiche dello schema del database devono passare attraverso le migrazioni di Entity Framework su tutte le partizioni. Il codice di esempio per questo documento non illustra come eseguire questa operazione. Provare a usare Update-Database con un parametro ConnectionString per eseguire l'iterazione su tutte le partizioni oppure estrarre lo script T-SQL per la migrazione in sospeso usando Update-Database con l'opzione –Script e applicare lo script T-SQL alle partizioni.  
* Data una richiesta, si presuppone che tutta la relativa elaborazione di database sia contenuta in una singola partizione identificata dalla chiave di partizionamento orizzontale fornita dalla richiesta. Questo presupposto, tuttavia, non è sempre valido, ad esempio quando non è possibile rendere disponibile una chiave di partizionamento orizzontale. A questo scopo, la libreria client fornisce la classe **MultiShardQuery** che implementa un'astrazione delle connessioni per l'esecuzione di query su più partizioni. L'uso di **MultiShardQuery** in combinazione con Entity Framework esula dall'ambito di questo documento

## <a name="conclusion"></a>Conclusione
Seguendo le procedure descritte in questo documento, le applicazioni Entity Framework possono usufruire della funzionalità di routing dipendente dai dati della libreria client dei database elastici mediante il refactoring dei costruttori delle sottoclassi **DbContext** usate nelle applicazioni stesse. Questo limita il numero di modifiche necessarie nelle posizioni in cui sono già presenti classi **DbContext**. Inoltre, le applicazioni Entity Framework possono continuare a usufruire della distribuzione automatica dello schema combinando le operazioni che richiamano le migrazioni Entity Framework con la registrazione di nuove partizioni e mapping nella mappa partizioni. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png



<!--HONumber=Dec16_HO2-->


