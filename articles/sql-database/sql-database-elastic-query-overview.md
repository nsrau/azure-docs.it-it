<properties
    pageTitle="Argomenti degli strumenti di database elastici di database SQL di Azure"
    description="Annuncia la funzionalità di query elastica"
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="sidneyh"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/09/2015"
    ms.author="sidneyh" />

# Panoramica di query (anteprima) di Database flessibile per il Database SQL Azure

Il **funzionalità di query del Database elastica**, in anteprima, consente di eseguire una query Transact-SQL che si estende su più database nel Database di SQL Azure. Consente di connettersi strumenti Microsoft e di terze parti (Excel, PowerBI, Tableau, ecc.) per i livelli di dati con più database, specialmente quando i database condividono uno schema comune (noto anche come orizzontale partizionamento o partizionamento orizzontale). Utilizzando questa funzionalità, è possibile scalare query ai livelli di dati di grandi dimensioni nel Database SQL e visualizzare i risultati nei report di business intelligence (BI).

Per iniziare la creazione di un'applicazione di query di database flessibile, vedere [Guida introduttiva a query di Database elastica](sql-database-elastic-query-getting-started.md).

## Scenari di query di database elastica

L'obiettivo della query del Database elastica è per agevolare gli scenari di reporting qualora più database contribuiscano righe in un singolo risultato complessivo. La query T-SQL può essere composta in modo diretto dall'utente o dall’applicazione oppure indirettamente tramite strumenti che sono connessi al database di query elastiche della query globale. Ciò è particolarmente utile durante la creazione di report, utilizzando strumenti di integrazione di Business Intelligence o dati commerciali, o qualsiasi software non può essere modificato. Con una query di database flessibile, è facilmente possibile eseguire query su più database utilizzando la familiare di connettività di SQL Server in strumenti come Excel, PowerBI, Tableau o Cognos.

Una query a database elastica inoltre facilita l'accesso a un intero insieme di database tramite query eseguite da SQL Server Management Studio o Visual Studio e facilita l'esecuzione di query tra database da Entity Framework o in altri ambienti ORM. Figura 1 mostra uno scenario in cui un'applicazione cloud esistente (che utilizza la libreria di strumenti di Database elastica) si basa su un livello dati con scalabilità orizzontale e una query a database elastica utilizzata per il reporting tra database.

**Figura 1**

![Query di database elastica utilizzato nel livello dati con scalabilità orizzontale][1]

Il livello dati viene scalato in molti database utilizzando uno schema comune. Questo approccio è noto anche come partizionamento orizzontale o il partizionamento orizzontale. Il partizionamento può essere eseguito e gestita utilizzando (1) di [libreria client di Database elastica](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) o (2) che utilizza un modello specifico dell'applicazione per la distribuzione dei dati tra più database. Con questa topologia report dispongono spesso possono estendersi su più database. Con una query di database flessibile, è ora possibile connettersi a un singolo database SQL e risultati di query dai database remoti vengono visualizzati come se generato da un singolo database virtuale.

> [AZURE.NOTE]La query di database elastico è adatta per scenari di reporting occasionali dove è possibile eseguire la maggior parte dell'elaborazione nel livello dati. Per carichi di lavoro report o di data warehousing con query più complesse, inoltre è possibile utilizzare [SQL Data Warehouse di Azure](http://azure.microsoft.com/services/sql-data-warehouse/).


## Scenari di query di database elastica

L'utilizzo di una query di database flessibile per eseguire attività relative ai report su un livello di dati partizionati orizzontalmente richiede un mapping della partizione di una certa flessibilità per rappresentare i database del livello dati. In genere, in questo scenario viene utilizzato solo un mapping della partizione singola e un database dedicato con funzionalità di query di database elastica funge da punto di ingresso per la query di report. Solo questo database dedicato deve essere configurato con gli oggetti di query di database flessibile, come descritto di seguito. Figura 2 viene illustrata questa topologia e la configurazione con il mapping dei database e partizionamento query database elastica.

> [AZURE.NOTE]Il database di query di database elastico dedicato deve essere un database v12 DB SQL e inizialmente solo il livello Premium è supportato. Non esistono restrizioni su partizioni stesse.

**Figura 2**

![Utilizzare query di Database flessibile per la creazione di report su livelli partizionati][2]

**il complesso ** dei dati associati a un singolo valore di una chiave di partizionamento orizzontale in una partizione. **un valore di colonna** che determina la modalità di distribuzione dei dati tra le partizioni. Ad esempio, dati distribuiti da aree possono avere area ID come chiave di partizionamento orizzontale. Per ulteriori informazioni, vedere il [glossario scalabilità elastica](sql-database-elastic-scale-glossary.md).)


Nel corso del tempo, topologie aggiuntive saranno supportate dalla funzionalità di query di Database flessibile. In questo articolo verrà aggiornato per riflettere le nuove funzionalità man mano che diventano disponibili.

## Abilitazione query elastica configurando un mapping della partizione

La reazione di una soluzione di query di database elastico richiede il [**mapping della partizione**](sql-database-elastic-scale-shard-map-management.md) degli strumenti di Database elastici per rappresentare i database remoti in una query di database elastico. Se già si utilizza la libreria client di Database flessibile, è possibile utilizzare il mapping della partizione esistente. In caso contrario, è necessario creare un mapping della partizione mediante strumenti di Database flessibile.

Il codice c# di esempio seguente viene illustrato come creare un mapping della partizione con un singolo database remoto aggiunto come una partizione.

    ShardMapManagerFactory.CreateSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerCreateMode.ReplaceExisting, RetryBehavior.DefaultRetryBehavior);
    smm = ShardMapManagerFactory.GetSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerLoadPolicy.Lazy,
      RetryBehavior.DefaultRetryBehavior);
    map = smm.CreateRangeShardMap<int>("yourshardmapname");
    shard = map.CreateShard(new ShardLocation("yoursqldbserver", "yoursqldbdatabasename"));

Per ulteriori informazioni sulle mappe di partizionamento, vedere [Shardmap management](sql-database-elastic-scale-shard-map-management.md).

Per utilizzare la funzionalità di query di Database flessibile, è innanzitutto necessario creare la mappa di partizionamento e registrare i database remoti come partizioni. Si tratta di un'operazione occasionale. È necessario modificare il mapping della partizione quando si aggiungono o rimuovono database remoti, quali sono incrementali operazioni su una mappa di partizione esistente.


## Creazione di oggetti di database elastica query database

Per descrivere le tabelle remote che è possibile accedere da un endpoint di query di database flessibile, è possibile introdurre la possibilità di definire le tabelle esterne che verranno visualizzato per l'applicazione e strumenti di terze parti come se fossero tabelle locali. È possibile inviare le query su questi oggetti di database in modo implicito tramite gli strumenti, oppure in modo esplicito da SQL Server Management Studio, Visual Studio Data Tools o da un'applicazione. Come qualsiasi altra istruzione Transact-SQL viene eseguita la query di database flessibile, con la differenza rilevante query distribuirà l'elaborazione per i database remoti potenzialmente molti oggetti esterni sottostanti.

La funzionalità di query di Database elastica si basa su queste quattro istruzioni DDL. In genere, queste istruzioni DDL vengono utilizzate una sola volta o quando lo schema dell'applicazione vengono modificati raramente.

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
*    [CREARE LE CREDENZIALI](https://msdn.microsoft.com/library/ms189522.aspx)
*    [CREARE/ELIMINARE ORIGINE DATI ESTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
*    [ELIMINARE LA TABELLA ESTERNA](https://msdn.microsoft.com/library/dn935021.aspx)

### Chiave master con ambito database e credenziali

Una credenziale rappresenta l'ID utente e password che verrà utilizzata la query di database flessibile per la connessione per il mapping della partizione scala elastica e i database remoti in database SQL Azure. È possibile creare la chiave master e credenziali utilizzando la sintassi seguente:

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';  
    CREATE CREDENTIAL <credential_name> ON DATABASE
    WITH IDENTITY = '<shard_map_username>',
    SECRET = '<shard_map_password>'
     [;]
Assicurarsi che il & lt; shard\_map\_username > non include un suffisso "@servername".

Per eliminare la chiave master e le credenziali, è possibile utilizzare la sintassi seguente:

    DROP CREDENTIAL <credential_name> ON DATABASE;
    DROP MASTER KEY;  

### DROP EXTERNAL DATA SOURCE

Il passaggio successivo, è necessario registrare il mapping della partizione come un'origine dati esterna. La sintassi per la creazione e l'eliminazione di origini dati esterne è definita come segue:

      CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
                     (TYPE = SHARD_MAP_MANAGER,
                     LOCATION = '<fully_qualified_server_name>',
                     DATABASE_NAME = '<shardmap_database_name>',
                     CREDENTIAL = <credential_name>,
                     SHARD_MAP_NAME = '<shardmapname>'
    ) [;]

Per eliminare un'origine dati esterna, è possibile utilizzare l'istruzione seguente:

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

L'utente deve disporre dell'autorizzazione ALTER ANY origine dei dati esterni. Questa autorizzazione è inclusa nell'autorizzazione ALTER DATABASE.

**Esempio**

Nell'esempio seguente viene illustrato l'utilizzo dell'istruzione CREATE per origini dati esterne.

    CREATE EXTERNAL DATA SOURCE MyExtSrc
    WITH
    (
    TYPE=SHARD_MAP_MANAGER,
    LOCATION='myserver.database.windows.net',
    DATABASE_NAME='ShardMapDatabase',
    CREDENTIAL= SMMUser,
    SHARD_MAP_NAME='ShardMap'
    );

È possibile recuperare l'elenco di origini dati esterne corrente della vista del catalogo seguenti:

    select * from sys.external_data_sources;

Si noti che vengono utilizzate le stesse credenziali per la lettura del mapping della partizione e accedere ai dati nel database remoti durante l'elaborazione della query.


### Tabelle esterne

Con la query di Database flessibile, è possibile estendere la sintassi di tabella esterna esistente per fare riferimento a tabelle partizionate in un database remoti (più) in database SQL Azure. Utilizzando il concetto di origine dati esterna dall'alto, la sintassi per creare ed eliminare tabelle esterne è definita come segue:

    CREATE EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    )[;]

    <sharded_external_table_options> ::=
          DATA_SOURCE = <External_Data_Source>,
          DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED | ROUND_ROBIN

I criteri di partizionamento orizzontale controllano se una tabella viene considerata come una tabella partizionata o come una tabella replicata. Con una tabella partizionata, i dati da diverse partizioni non si sovrappongano. Le tabelle replicate dispone a sua volta gli stessi dati in ogni partizione. Query processor si basa su queste informazioni per l'elaborazione delle query più efficiente e con correzione. La distribuzione round robin indica che viene utilizzato un metodo specifico di applicazione per la distribuzione dei dati della tabella.

    DROP EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name[;]

Autorizzazioni per **CREARE/ELIMINARE LA TABELLA esterna**: sono necessarie autorizzazioni ALTER ANY origine dati esterna necessarie anche per fare riferimento all'origine dati sottostante.

**Esempio**: nell'esempio seguente viene illustrato come creare una tabella esterna:

    CREATE EXTERNAL TABLE [dbo].[order_line](
        [ol_o_id] [int] NOT NULL,
        [ol_d_id] [tinyint] NOT NULL,
        [ol_w_id] [int] NOT NULL,
        [ol_number] [tinyint] NOT NULL,
        [ol_i_id] [int] NOT NULL,
        [ol_delivery_d] [datetime] NOT NULL,
        [ol_amount] [smallmoney] NOT NULL,
        [ol_supply_w_id] [int] NOT NULL,
        [ol_quantity] [smallint] NOT NULL,
        [ol_dist_info] [char](24) NOT NULL
    )
    WITH
    (
        DATA_SOURCE = MyExtSrc,
        DISTRIBUTION=SHARDED(ol_w_id)
    );

Nell'esempio seguente viene illustrato come recuperare l'elenco di tabelle esterni dal database corrente:

    select * from sys.external_tables;


## Creazione di report e l'esecuzione di query

### Query
Dopo aver definito l'origine dati esterna e le tabelle esterne, è possibile utilizzare stringhe di connessione di Database SQL familiare per connettersi al database che sia abilitata la funzionalità di query Database elastica. È possibile utilizzare eseguire query completa di sola lettura tramite le tabelle esterne, con alcune limitazioni descritte nella [sezione limitazioni](#preview-limitations) sottostante.

**Esempio**: la query seguente esegue un join a tre vie tra magazzini, ordini e righe di ordine e vengono utilizzate diverse funzioni di aggregazione e un filtro selettivo. Supponendo che la colonna id warehouse vengono partizionati magazzini, ordini e righe d'ordine, una query di database flessibile può collocare i join su database remoti può orizzontale e l'elaborazione della parte della query costosa.

    select
        w_id as warehouse,
        o_c_id as customer,
        count(*) as cnt_orderline,
        max(ol_quantity) as max_quantity,
        avg(ol_amount) as avg_amount,
        min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id

### Stored procedure sp \_ EXECUTE\_FANOUT

SP\_EXECUTE\_FANOUT è una stored procedure che fornisce l'accesso ai database rappresentato da una mappa di partizionamento. La stored procedure accetta i parametri seguenti:

-    **Nome server** (nvarchar): nome completo del server logico che ospita il mapping della partizione.
-    **Nome del database mappa partizionamento** (nvarchar): il nome del database di mappa di partizionamento.
-    **Nome utente** (nvarchar): il nome utente per accedere al database della mappa il partizionamento e i database remoti.
-    **Password** (nvarchar): Password per l'utente.
-    **Nome mappa partizionamento** (nvarchar): il nome della mappa del partizionamento da utilizzare per la query.
-    **Query**: la query può essere eseguita in ogni partizione.

Utilizza le informazioni della mappa partizione fornite nei parametri di chiamata per eseguire l'istruzione specificata su tutte le partizioni registrate con il mapping della partizione. I risultati vengono uniti utilizzando la semantica di UNION ALL simile alle query con più partizioni. Il risultato include anche la colonna aggiuntiva 'virtual' con il nome del database remoto.

Si noti che le stesse credenziali vengono utilizzate per connettersi al database di mappa di partizionamento e per le partizioni.

**Esempio**:

    sp_execute_fanout 'myserver.database.windows.net', N'ShardMapDb', N'myuser', N'MyPwd', N'ShardMap', N'select count(w_id) as foo from warehouse'

## Connettività per gli strumenti
È possibile utilizzare familiare stringhe di connessione al database SQL al database di query di Database flessibile per la connessione gli strumenti di integrazione di Business Intelligence e i dati. Assicurarsi che SQL Server sia supportato come origine dati per lo strumento. Quindi utilizzare gli oggetti esterni nel database di query di Database flessibile come con qualsiasi altro database di SQL Server è necessario connettersi allo strumento.

## Procedure consigliate
*    Assicurarsi che il database di gestione partizione mappa e i database definiti nel mapping della partizione consentano l'accesso di Microsoft Azure le regole del firewall. Ciò è necessario in modo che il database di query di Database flessibile di connettersi ad essi. Per ulteriori informazioni, vedere [Firewall di database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).
*    Una query a database elastica non convalidare o imporre la distribuzione di dati definita dalla tabella esterna. Se la distribuzione di dati effettivo è diversa dalla distribuzione specificata nella definizione di tabella, le query possono restituire risultati imprevisti.
*    Una query a database elastica funziona meglio per le query di cui è possibile eseguire la maggior parte del calcolo le partizioni. In genere si ottiene le migliori prestazioni di query con predicati del filtro selettivo che può essere valutata in join le partizioni tramite le chiavi di partizionamento che possono essere eseguite in modo su tutte le partizioni allineate alle partizioni. Altri modelli di query potrebbero essere necessario caricare grandi quantità di dati dalle partizioni per il nodo head e potrebbero verificarsi una riduzione delle prestazioni.

## Costi

La query di Database flessibile è inclusa il costo del database di SQL Azure. Si noti che sono supportate le topologie cui database remoti si trovano in un data center diverso rispetto all'endpoint di query di database flessibile, ma dati in uscita dai database remoti vengono addebitati in base alle tariffe normali uscita Azure.

## Limiti di anteprima

Esistono alcuni aspetti da tenere a mente dell'anteprima:

*    La funzionalità di query di Database elastica sarà inizialmente solo essere disponibili nel livello di prestazioni Premium v12 DB SQL, anche se database remoti di accedere a una query di database flessibile possono essere di qualsiasi livello.
* Tabelle esterne a cui fa riferimento l'origine dati esterna supportano solo operazioni di lettura sul database remoti. È possibile, tuttavia, puntare tutte le funzionalità Transact-SQL nel database di query di database elastica cui si trova la definizione della tabella esterna. Può trattarsi utile, ad esempio, per rendere persistenti i risultati temporanei utilizzando SELECT elenco\_colonne INTO local\_table o per definire stored procedure nel database database elastica query che fanno riferimento a tabelle esterne.
*    Parametri nelle query attualmente non possono essere inseriti ai database remoti. Sarà necessario recuperare tutti i dati del nodo head e query con parametri potrebbe subire un peggioramento delle prestazioni in base alla dimensione dei dati. È una soluzione temporanea per evitare di parametri nelle query o per utilizzare l'opzione RECOMPILE per disporre di parametri automaticamente sostituiti con i relativi valori correnti.
* Statistiche a livello di colonna sulle tabelle esterne non sono attualmente supportate.
* La query di Database flessibile, attualmente non esegue eliminazione partizione quando predicati tramite la chiave di partizionamento orizzontale consente di escludere in modo sicuro determinati database remoti dall'elaborazione. Di conseguenza, le query verranno cancellati sempre tutti i database remoti, rappresentati da origini dati esterne della query.
* Tutte le query che includono join tra tabelle in database diversi potrebbero portare un numero elevato di righe nel database di query di database flessibile per l'elaborazione, con una riduzione delle prestazioni risultante. È consigliabile per sviluppare query che possono essere elaborate in locale in ogni database remoto o da utilizzare quando le clausole per limitare le righe coinvolte da ciascun database prima di eseguire il join.
*    La sintassi utilizzata per la definizione dei metadati di Database elastica query verrà modificato durante l'anteprima.
*    Transact-SQL script funzionalità di SQL Server Management Studio o di SSDT attualmente non funziona con gli oggetti di query di database flessibile.

## Commenti e suggerimenti
Condividere commenti e suggerimenti sulle esperienze con noi in Disqus o Stackoverflow. Siamo interessati a tutti i tipi di commenti e suggerimenti relativi al servizio (difetti, bordi approssimativo, gap di funzionalità).

## Passaggi successivi
Per iniziare a esplorare le query di Database elastico, provare la nostra esercitazione dettagliata per avere un esempio funzionante in esecuzione in minuti: [Guida introduttiva a query di Database elastico](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png

<!--anchors-->

<!---HONumber=August15_HO6-->