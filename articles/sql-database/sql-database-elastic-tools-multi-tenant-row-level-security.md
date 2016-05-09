<properties 
	pageTitle="Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga" 
	description="Informazioni su come utilizzare gli strumenti di database elastici insieme alla sicurezza a livello di riga per compilare un'applicazione con un livello dati altamente scalabile in un database SQL di Azure che supporta partizioni multi-tenant." 
	metaKeywords="azure sql database elastic tools multi tenant row level security rls" 
	services="sql-database" 
    documentationCenter=""  
	manager="jhubbard" 
	authors="tmullaney"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2016" 
	ms.author="thmullan;torsteng;sidneyh" />

# Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga 

Gli [strumenti di database elastici](sql-database-elastic-scale-get-started.md) e la [sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131) offrono un potente set di funzionalità per ridimensionare in modo flessibile ed efficace il livello dati di un'applicazione multi-tenant con database SQL di Azure. In questo articolo viene illustrato come utilizzare queste tecnologie insieme per compilare un’applicazione con un livello di dati altamente scalabile che supporta partizioni multi-tenant, utilizzando **ADO.NET SqlClient** e/o **Entity Framework**.

* Gli **strumenti di database elastici** consentono agli sviluppatori di scalare il livello dati di un'applicazione tramite procedure di partizionamento orizzontale standard del settore, utilizzando un set di librerie .NET e i modelli di servizio di Azure. La gestione delle partizioni mediante la libreria client di database elastici consente di automatizzare e semplificare molte delle attività infrastrutturali generalmente associate al partizionamento orizzontale. 

* La **sicurezza a livello di riga** consente agli sviluppatori di archiviare i dati per più tenant nello stesso database utilizzando criteri di sicurezza per filtrare le righe che non appartengono al tenant che esegue una query. La centralizzazione della logica di accesso con RLS all'interno del database, anziché dell'applicazione, semplifica la manutenzione e riduce il rischio di errori nel momento in cui la base di codici di un'applicazione cresce. RLS richiede la versione più recente dell’[aggiornamento di database SQL di Azure (V12)](sql-database-v12-whats-new.md).

L'utilizzo combinato di queste funzionalità consente a un'applicazione di usufruire di miglioramenti in termini di efficienza e di risparmiare sui costi grazie alla possibilità di memorizzare i dati per più tenant nello stesso database di partizionamento. Allo stesso tempo, un'applicazione continua ad avere la flessibilità per offrire partizioni single-tenant per tenant "premium" che richiedono garanzie di prestazioni più severe, poiché le partizioni multi-tenant non garantiscono un'uguale distribuzione delle risorse tra i tenant.

In breve, le API di [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) della libreria client del database elastico connettono automaticamente i tenant al database di partizionamento corretto contenente la relativa chiave di partizionamento (in genere, "TenantId"). Una volta connessi, un criterio di sicurezza RLS all'interno del database garantisce che i tenant siano in grado di accedere solo alle righe che contengono il relativo TenantId. Si presuppone che tutte le tabelle contengano una colonna TenantId per indicare quali righe appartengono a ciascun tenant.

![Architettura delle applicazioni di blog][1]

## Scaricare il progetto di esempio

### Prerequisiti
* Utilizzare Visual Studio (2012 o versione successiva) 
* Creare tre database SQL di Azure 
* Scaricare il progetto di esempio: [Strumenti di database elastici per SQL di Azure - Partizioni multi-tenant](http://go.microsoft.com/?linkid=9888163)
  * Immettere le informazioni per i database all'inizio di **Program.cs** 

Questo progetto estende quello descritto in [Strumenti di database elastici per SQL di Azure - Integrazione di Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) aggiungendo il supporto per i database di partizionamento multi-tenant. Viene compilata una semplice applicazione console per la creazione di blog e post, con quattro tenant e due database di partizionamento multi-tenant, come illustrato nel diagramma precedente.

Compilare ed eseguire l'applicazione. In tal modo verrà eseguito il bootstrap del gestore mappa di partizionamento degli strumenti di database elastici e verranno effettuati i test seguenti:

1. Utilizzando Entity Framework e LINQ, creare un nuovo blog e quindi visualizzare tutti i blog per ciascun tenant
2. Utilizzando ADO.NET SqlClient, visualizzare tutti i blog per un tenant
3. Provare a inserire un blog per il tenant non corretto per verificare che venga generato un errore  

Si noti che poiché RLS non è stata ancora abilitata nei database di partizionamento, ciascuno di questi test rivela un problema: i tenant sono in grado di visualizzare i blog non appartenenti ad essi, e all'applicazione non viene impedito di inserire un blog per il tenant errato. Nella parte restante di questo articolo viene descritto come risolvere questi problemi mediante l'isolamento dei tenant con RLS. Sono disponibili due passaggi:

1. **Livello applicazione**: modificare il codice dell'applicazione per impostare sempre il TenantId corrente in SESSION\_CONTEXT dopo l'apertura di una connessione. Nel progetto di esempio questa operazione è già stata eseguita. 
2. **Livello dati**: creare un criterio di sicurezza RLS in ogni database di partizionamento per filtrare le righe in base al TenantId archiviato in SESSION\_CONTEXT. È necessario eseguire questa operazione per ciascun database di partizionamento, altrimenti le righe nelle partizioni multi-tenant non verranno filtrate. 


## Passaggio 1) Livello applicazione: impostare il TenantId in SESSION\_CONTEXT

Dopo la connessione a un database di partizionamento tramite le API di routing dipendente dai dati della libreria dei client di database elastico, l'applicazione dovrà comunque indicare al database quale TenantId utilizza tale connessione, in modo che un criterio di sicurezza RLS sia in grado di filtrare le righe appartenenti ad altri tenant. Il metodo consigliato per passare queste informazioni consiste nell'archiviare il TenantId corrente per tale connessione in [SESSION\_CONTEXT](https://msdn.microsoft.com/library/mt590806.aspx). Nota: in alternativa è possibile usare [CONTEXT\_INFO](https://msdn.microsoft.com/library/ms180125.aspx), ma SESSION\_CONTEXT è preferibile perché è più facile da usare, restituisce NULL per impostazione predefinita e supporta le coppie chiave-valore.

### Entity Framework

Per le applicazioni che usano Entity Framework, l'approccio più semplice consiste nell'impostare SESSION\_CONTEXT all'interno dell'override ElasticScaleContext descritto in [Routing dipendente dai dati con DbContext di Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md/#data-dependent-routing-using-ef-dbcontext). Prima di restituire la connessione negoziata tramite il routing dipendente dai dati, creare ed eseguire semplicemente un SqlCommand che imposti "TenantId" in SESSION\_CONTEXT sull'oggetto shardingKey specificato per la connessione. In questo modo, è sufficiente scrivere una sola volta il codice per impostare SESSION\_CONTEXT.

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data dependent routing. This call will open a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call will fail for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
} 
// ... 
```

Ora SESSION\_CONTEXT viene impostato automaticamente sul TenantId specificato ogni volta che si richiama ElasticScaleContext:

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
	using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
	{     
		var query = from b in db.Blogs
	                orderby b.Name
	                select b;
		
		Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
		foreach (var item in query)     
		{       
			Console.WriteLine(item.Name);     
		}   
	} 
}); 
```

### ADO.NET SqlClient 

Per le applicazioni che usano ADO.NET SqlClient, l'approccio consigliato consiste nel creare una funzione wrapper intorno a ShardMap.OpenConnectionForKey() che imposti automaticamente "TenantId" in SESSION\_CONTEXT sul TenantId corretto prima di restituire una connessione. Per garantire che SESSION\_CONTEXT sia sempre impostato, è consigliabile aprire le connessioni usando solo questa funzione wrapper.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that SESSION_CONTEXT is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs will be listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## Passaggio 2) Livello dati: creare criteri di sicurezza a livello di riga

### Creare i criteri di sicurezza per filtrare le righe accessibili a ogni tenant

Ora che l'applicazione imposta SESSION\_CONTEXT sul TenantId corrente prima di eseguire la query, i criterio di sicurezza RLS possono filtrare le query ed escludere le righe che contengono un TenantId diverso.

RLS viene implementata in T-SQL: una funzione definita dall'utente definisce la logica di accesso e i criteri di sicurezza associano questa funzione a un numero qualsiasi di tabelle. Per questo progetto, la funzione verificherà semplicemente che l'applicazione, invece di un altro utente SQL, sia connessa al database e che il "TenantId" archiviato in SESSION\_CONTEXT corrisponda al TenantId di una determinata riga. Un predicato del filtro consentirà alle righe che soddisfano tali condizioni di passare attraverso il filtro per le query SELECT, UPDATE e DELETE. Un predicato di blocco impedirà l'inserimento e l'aggiornamento delle righe che violano tali condizioni. Se SESSION\_CONTEXT non è stato impostato, verrà restituito NULL e non sarà possibile vedere o inserire alcuna riga.

Per abilitare RLS, eseguire l'istruzione T-SQL seguente in tutte le partizioni utilizzando Visual Studio (SSDT), SSMS o lo script di PowerShell incluso nel progetto (o se si utilizzano i [processi di database elastici](sql-database-elastic-jobs-overview.md), è possibile utilizzarli per automatizzare l'esecuzione di questa istruzione T-SQL in tutte le partizioni):

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
	RETURNS TABLE     
	WITH SCHEMABINDING
AS
	RETURN SELECT 1 AS fn_accessResult          
		WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
		AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
	ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
	ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [AZURE.TIP] Per i progetti più complessi in cui è necessario aggiungere il predicato in centinaia di tabelle, è possibile usare una stored procedure helper che genera automaticamente criteri di sicurezza aggiungendo un predicato in tutte le tabelle di uno schema. Vedere il [blog relativo all'applicazione della sicurezza a livello di riga a tutte le tabelle con lo script helper](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).

Ora se si esegue di nuovo l'applicazione di esempio, i tenant potranno visualizzare unicamente le righe appartenenti ad essi. L'applicazione non può inoltre inserire righe che appartengono a tenant diversi da quello attualmente connesso al database di partizionamento e non può aggiornare le righe visibili in modo che abbiano un TenantId diverso. Se l'applicazione tenta di effettuare una qualsiasi delle due operazioni, verrà generata un'eccezione DbUpdateException.

Se si aggiunge una nuova tabella in un secondo momento, è sufficiente MODIFICARE i criteri di sicurezza e aggiungere predicati di filtro e di blocco nella nuova tabella:

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
	ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable
GO 
```

### Aggiungere vincoli predefiniti per inserire automaticamente il TenantId per le operazioni INSERT 

È possibile inserire un vincolo predefinito in ogni tabella per popolare automaticamente il TenantId con il valore attualmente archiviato in SESSION\_CONTEXT durante l'inserimento di righe. Ad esempio:

```
-- Create default constraints to auto-populate TenantId with the value of SESSION_CONTEXT for inserts 
ALTER TABLE Blogs     
	ADD CONSTRAINT df_TenantId_Blogs      
	DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO

ALTER TABLE Posts     
	ADD CONSTRAINT df_TenantId_Posts      
	DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO 
```

A questo punto non è necessario che l'applicazione specifichi un TenantId durante l'inserimento di righe:

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
	using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
	{
		var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
		db.Blogs.Add(blog);     
		db.SaveChanges();   
	} 
}); 
```

> [AZURE.NOTE] Se si utilizzano i vincoli predefiniti per un progetto Entity Framework, è consigliabile NON includere la colonna TenantId nel modello di dati EF. Il motivo è che le query Entity Framework forniscono automaticamente valori predefiniti che sostituiranno i vincoli predefiniti creati in T-SQL che usano SESSION\_CONTEXT. Per utilizzare i vincoli predefiniti nel progetto di esempio, ad esempio, è necessario rimuovere TenantId da DataClasses.cs (ed eseguire Add-Migration nella console di Gestione pacchetti) e utilizzare T-SQL per garantire che il campo esista unicamente nelle tabelle del database. In questo modo, Entity Framework non fornirà automaticamente valori predefiniti errati durante l'inserimento dei dati.

### (Facoltativo) Abilitare un "superuser" per accedere a tutte le righe
Alcune applicazioni potrebbero creare un "superuser" che possa accedere a tutte le righe, ad esempio, per abilitare la creazione di rapporti fra tutti i tenant in tutte le partizioni o per eseguire operazioni di suddivisione/unione in partizioni che comportano lo spostamento di righe del tenant tra database. A tale scopo, è necessario creare un nuovo utente SQL ("superuser" in questo esempio) in ogni database di partizionamento. Modificare quindi i criteri di sicurezza con una nuova funzione di predicato che consenta a questo utente di accedere a tutte le righe:

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### Manutenzione 

* **Aggiunta di nuove partizioni**: è necessario eseguire lo script T-SQL per abilitare RLS in tutte le nuove partizioni. In caso contrario, le query su tali partizioni non verranno filtrate.

* **Aggiunta di nuove tabelle**: è necessario aggiungere un predicato di filtro e di blocco ai criteri di sicurezza in tutte le partizioni ogni volta che si crea una nuova tabella. In caso contrario, le query sulla nuova tabella non verranno filtrate. Questa operazione può essere automatizzata tramite un trigger DDL, come descritto nel [blog relativo all'applicazione automatica della sicurezza a livello di riga alle tabelle create di recente](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).


## Riepilogo 

Gli strumenti di database elastici e la sicurezza a livello di riga possono essere utilizzati insieme per scalare orizzontalmente il livello di dati di un'applicazione con supporto sia per le partizioni multi-tenant, sia per quelle con tenant singolo. Le partizioni multi-tenant possono essere utilizzate per archiviare i dati in modo più efficiente (in particolare nei casi in cui un gran numero di tenant dispone solo di poche righe di dati), mentre le partizioni con tenant singolo possono essere utilizzate per supportare i tenant premium con requisiti di prestazioni e isolamento più rigidi. Per altre informazioni, vedere [Sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->

 

<!---HONumber=AcomDC_0427_2016-->