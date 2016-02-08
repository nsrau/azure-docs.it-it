<properties
    pageTitle="Configurare la replica geografica per il database SQL di Azure con Transact-SQL | Microsoft Azure"
    description="Configurare la replica geografica per il database SQL di Azure con Transact-SQL"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="01/25/2015"
    ms.author="carlrab"/>

# Configurare la replica geografica per il database SQL di Azure con Transact-SQL



> [AZURE.SELECTOR]
- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


Questo articolo illustra come configurare la replica geografica per un database SQL di Azure con Transact-SQL.


La replica geografica consente di creare un massimo di 4 database di replica (secondari) in località, o aree geografiche, di data center diverse. I database secondari sono disponibili in caso di interruzione di un data center o dell'impossibilità di connettersi al database primario.

La replica geografica è disponibile solo per i database Standard e Premium.

I database Standard possono avere un solo database secondario non accessibile in lettura e devono usare l'area consigliata. I database Premium possono avere un massimo di quattro database secondari accessibili in lettura in una qualsiasi della aree disponibili.


Per configurare la replica geografica, sono necessari gli elementi seguenti:

- Un sottoscrizione di Azure: se non è disponibile una sottoscrizione di Azure, fare clic su **VERSIONE DI VALUTAZIONE GRATUITA** nella parte superiore della pagina, quindi tornare a questo articolo per completare la procedura.
- Un server di database SQL di Azure logico <MyLocalServer> e un database SQL <MyDB>: il database primario che si vuole replicare in una area geografica diversa.
- Uno o più server di database SQL di Azure logici <MySecondaryServer(n)>: i server logici saranno i server partner in cui verranno creati i database secondari di replica geografica.
- Un account di accesso con il ruolo DBManager nel database primario e con db\_ownership del database locale per il quale verrà eseguita la replica geografica, oltre al ruolo DBManager nei server partner in cui si configurerà la replica geografica.
- La versione più recente di SQL Server Management Studio: per ottenere la versione più recente di SQL Server Management Studio (SSMS), visitare la pagina di [download di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Per informazioni sull'uso di SQL Server Management Studio per gestire database e server logici di database SQL di Azure, vedere [Gestione di database SQL di Azure tramite SQL Server Management Studio](sql-database-manage-azure-ssms.md)

## Aggiungere un database secondario

È possibile usare l'istruzione **ALTER DATABASE** per creare un database secondario in un server partner. Eseguire questa istruzione sul database master del server che contiene il database da replicare. Il database con replica geografica, ovvero il "database primario", avrà lo stesso nome del database che viene replicato e, per impostazione predefinita, lo stesso livello di servizio del database primario. Il database secondario può essere accessibile o non accessibile in lettura e può essere un database singolo o un database elastico. Per altre informazioni, vedere [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Livelli di servizio](sql-database-service-tiers.md). Dopo la creazione e il seeding del database secondario, inizierà la replica asincrona dei dati dal database primario. I passaggi seguenti descrivono come configurare la replica geografica tramite Management Studio. Vengono descritti i passaggi per creare database secondari accessibili e non accessibili in lettura con un database singolo o un database elastico.

> [AZURE.NOTE] Se il database secondario è disponibile nel server partner specificato, ad esempio perché esiste o è esistita una relazione di replica geografica, il comando non riuscirà.


### Aggiungere un database secondario non accessibile in lettura (database singolo)

Usare la procedura seguente per creare un database secondario non accessibile in lettura come database singolo.

1. Usare la versione di SQL Server Management Studio 13.0.600.65 o successiva.

 	 >[AZURE.IMPORTANT] Scaricare la versione [più recente](https://msdn.microsoft.com/library/mt238290.aspx) di SQL Server Management Studio. È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti del portale di Azure.


2. Aprire la cartella Database, espandere la cartella **Database di sistema** fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.

3. Usare l'istruzione **ALTER DATABASE** seguente per convertire un database locale in un database primario con replica geografica con un database secondario non accessibile in lettura in MySecondaryServer1, in cui MySecondaryServer1 è il nome descrittivo del server.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. Fare clic su **Execute** per eseguire la query.


### Aggiungere un database secondario accessibile in lettura (database singolo)
Usare la procedura seguente per creare un database secondario accessibile in lettura come database singolo.

1. In Management Studio connettersi al server logico di database SQL di Azure.

2. Aprire la cartella Database, espandere la cartella **Database di sistema** fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.

3. Usare l'istruzione **ALTER DATABASE** seguente per convertire un database locale in un database primario con replica geografica con un database secondario accessibile in lettura in un server secondario.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. Fare clic su **Execute** per eseguire la query.



### Aggiungere un database secondario non accessibile in lettura (database elastico)
Usare la procedura seguente per creare un database secondario non accessibile in lettura come database elastico.

1. In Management Studio connettersi al server logico di database SQL di Azure.

2. Aprire la cartella Database, espandere la cartella **Database di sistema** fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.

3. Usare l'istruzione **ALTER DATABASE** seguente per convertire un database locale in un database primario con replica geografica con un database secondario non accessibile in lettura in un pool elastico.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. Fare clic su **Execute** per eseguire la query.



### Aggiungere un database secondario accessibile in lettura (database elastico)
Usare la procedura seguente per creare un database secondario accessibile in lettura come database elastico.

1. In Management Studio connettersi al server logico di database SQL di Azure.

2. Aprire la cartella Database, espandere la cartella **Database di sistema** fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.

3. Usare l'istruzione **ALTER DATABASE** seguente per convertire un database locale in un database primario con replica geografica con un database secondario accessibile in lettura in un pool elastico.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. Fare clic su **Execute** per eseguire la query.



## Rimuovere un database secondario

È possibile usare l'istruzione **ALTER DATABASE** per terminare definitivamente la relazione di replica tra un database secondario e il relativo database primario. L'istruzione viene eseguita sul database master in cui risiede il database primario. Dopo la terminazione della relazione, il database secondario diventa un normale database accessibile in lettura e scrittura. Se la connettività al database secondario viene interrotta, il comando riesce ma il database diventerà di nuovo accessibile in lettura e scrittura al ripristino della connettività. Per altre informazioni, vedere [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Livelli di servizio](sql-database-service-tiers.md).

Usare la procedura seguente per rimuovere il database secondario con replica geografica da una relazione di replica geografica.

1. In Management Studio connettersi al server logico di database SQL di Azure.

2. Aprire la cartella Database, espandere la cartella **Database di sistema** fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.

3. Usare l'istruzione **ALTER DATABASE** seguente per rimuovere un database secondario con replica geografica.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. Fare clic su **Execute** per eseguire la query.


## Avviare un failover pianificato per alzare di livello un database secondario per diventare il nuovo database primario

È possibile usare l'istruzione **ALTER DATABASE** per alzare di livello un database secondario a nuovo database primario in maniera pianificata, abbassando di livello il database primario esistente a database secondario. Questa istruzione viene eseguita sul database master nel server logico di database SQL di Azure in cui risiede il database secondario con replica geografica che viene alzato di livello. Questa funzionalità è progettata per il failover pianificato, ad esempio durante le esercitazioni sul ripristino di emergenza, e richiede che il database primario sia disponibile.

Il comando esegue il flusso di lavoro seguente:

1. Passa temporaneamente alla modalità di replica sincrona, in modo che tutte le transazioni in sospeso siano scaricate nel server secondario e le nuove transazioni vengano bloccate.

2. Scambia i ruoli dei due database nella relazione di replica geografica.

Questa sequenza assicura che non si verifichino perdite di dati. Per un breve periodo, da 0 a 25 secondi, entrambi i database non sono disponibili mentre vengono scambiati i ruoli. Il completamento dell'intera operazione dovrebbe richiedere meno di un minuto in circostanze normali. Per altre informazioni, vedere [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Livelli di servizio](sql-database-service-tiers.md).


> [AZURE.NOTE] Se il database primario non è disponibile quando si esegue il comando, questo non riuscirà e verrà visualizzato un messaggio di errore in cui è indicato che il server primario non è disponibile. In rari casi, è possibile che l'operazione non possa essere completata e appaia bloccata. In questo caso l'utente può eseguire il comando di failover forzato e accettare la perdita dei dati.

Usare la procedura seguente per avviare un failover pianificato.

1. In Management Studio connettersi al server logico di database SQL di Azure in cui risiede un database secondario con replica geografica.

2. Aprire la cartella Database, espandere la cartella **Database di sistema** fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.

3. Usare l'istruzione **ALTER DATABASE** seguente per convertire il database con replica geografica in un database primario con replica geografica con un database secondario accessibile in lettura in <MySecondaryServer4> in <ElasticPool2>.

        ALTER DATABASE <MyDB> FAILOVER;

4. Fare clic su **Execute** per eseguire la query.



## Avviare un failover non pianificato dal database primario al database secondario

È possibile usare l'istruzione **ALTER DATABASE** per alzare di livello un database secondario a nuovo database primario in maniera non pianificata, forzando l'abbassamento di livello del database primario esistente a database secondario in un momento in cui il database primario non è più disponibile. Questa istruzione viene eseguita sul database master nel server logico di database SQL di Azure in cui risiede il database secondario con replica geografica che viene alzato di livello.

Questa funzionalità è progettata per il ripristino di emergenza quando il ripristino della disponibilità del database è critica ed è accettabile una perdita di dati. Quando si richiama il failover forzato, il database secondario specificato diventa immediatamente il database primario e inizia ad accettare le transazioni di scrittura. Non appena il database primario originale può riconnettersi a questo nuovo database primario, viene eseguito un backup incrementale nel database primario originale e il database primario precedente viene convertito in un database secondario per il nuovo database primario. In seguito si tratterà semplicemente di una replica di sincronizzazione del nuovo database.

Poiché tuttavia il ripristino temporizzato non è supportato nei database secondari, se l'utente vuole recuperare i dati di cui è stato eseguito il commit nel database primario precedente e che non sono stati replicati nel nuovo database primario prima del failover forzato, dovrà rivolgersi al supporto tecnico per il ripristino dei dati persi.

> [AZURE.NOTE] Se il comando viene eseguito quando i database primario e secondario sono online, il database primario precedente diventerà il nuovo database secondario, ma non verrà eseguita alcuna sincronizzazione dei dati. Potrebbe quindi verificarsi una perdita di dati.


Se il database primario ha più database secondari, il comando riuscirà solo sul server secondario in cui è stato eseguito il comando. Gli altri database secondari non saranno informati che si è verificato il failover forzato. L'utente dovrà ripristinare manualmente questa configurazione usando un'API di "rimozione del database secondario" e quindi riconfigurare la replica geografica nei database secondari aggiuntivi.

Usare la procedura seguente per forzare la rimozione del database secondario con replica geografica da una relazione di replica geografica.

1. In Management Studio connettersi al server logico di database SQL di Azure in cui risiede un database secondario con replica geografica.

2. Aprire la cartella Database, espandere la cartella **Database di sistema** fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.

3. Usare l'istruzione **ALTER DATABASE** seguente per convertire <MyLocalDB> in un database primario con replica geografica con un database secondario accessibile in lettura in <MySecondaryServer4> in <ElasticPool2>.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Fare clic su **Execute** per eseguire la query.

## Monitorare la configurazione di replica geografica e l'integrità

Le attività di monitoraggio includono il controllo della configurazione della replica geografica e dell'integrità della replica dei dati. È possibile usare la vista a gestione dinamica (DMV) **sys.dm\_geo\_replication\_links** in un database master per restituire informazioni su tutti i collegamenti di replica esistenti per ogni database nel server logico di database SQL di Azure. Questa vista include una riga per ogni collegamento di replica tra i database primari e secondari. È possibile usare la DMV **sys.dm\_replication\_status** per restituire una riga per ogni database SQL di Azure attualmente interessato da un collegamento di replica. Sono inclusi il database primario e i database secondari. Se esiste più di un collegamento di replica continua per un determinato database primario, questa tabella contiene una riga per ogni relazione. La vista viene creata in tutti i database, incluso il database master logico. Eseguendo query su questa vista in tale database, verrà tuttavia restituito un set vuoto. È possibile usare la DMV **sys.dm\_operation\_status** per visualizzare lo stato di tutte le operazioni di database, incluso lo stato dei collegamenti di replica. Per altre informazioni, vedere [sys.geo\_replication\_links (database SQL di Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm\_geo\_replication\_link\_status (database SQL di Azure)](https://msdn.microsoft.com/library/mt575504.aspx) e [sys.dm\_operation\_status (database SQL di Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Usare la procedura seguente per monitorare una relazione di replica geografica.

1. In Management Studio connettersi al server logico di database SQL di Azure.

2. Aprire la cartella Database, espandere la cartella **Database di sistema** fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.

3. Usare l'istruzione seguente per visualizzare tutti i database con collegamenti di replica geografica.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. Fare clic su **Execute** per eseguire la query.
5. Aprire la cartella Database, espandere la cartella **Database di sistema**, fare clic con il pulsante destro del mouse su **MyDB** e quindi scegliere **Nuova query**.
6. Usare l'istruzione seguente per visualizzare gli intervalli di replica e l'ultima ora di replica dei database secondari di MyDB.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. Fare clic su **Execute** per eseguire la query.
8. Usare l'istruzione seguente per visualizzare le operazioni di replica geografica più recenti associate al database MyDB.

        SELECT * FROM sys.dm_operation_status where major_resource_is = 'MyDB'
        ORDER BY start_time DESC

9. Fare clic su **Execute** per eseguire la query.


## Passaggi successivi

- [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery-drills.md)


## Risorse aggiuntive

- [Nuove funzionalità di replica geografica in evidenza](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Progettazione di applicazioni cloud per la continuità aziendale mediante la replica geografica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](sql-database.md)

<!---HONumber=AcomDC_0128_2016-->