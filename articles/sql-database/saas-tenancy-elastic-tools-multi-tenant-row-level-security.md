---
title: Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga | Microsoft Docs
description: Usare gli strumenti di database elastici insieme alla sicurezza a livello di riga per compilare un'applicazione con un livello dati altamente scalabile.
metakeywords: azure sql database elastic tools multi tenant row level security rls
services: sql-database
documentationcenter: 
manager: jhubbard
author: tmullaney
ms.assetid: e72d3cfe-e9be-4326-b776-9c6d96c0a18e
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: thmullan;torsteng
ms.openlocfilehash: f874800e8647eac6ed94945d79e904df86207b0f
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga

Gli [strumenti di database elastici](sql-database-elastic-scale-get-started.md) e la [sicurezza a livello di riga][rls] cooperano per consentire il ridimensionamento del livello dati di un'applicazione multi-tenant con un database SQL di Azure. La combinazione di queste tecnologie consente di creare un'applicazione con un livello dati estremamente scalabile. Il livello dati supporta partizioni multi-tenant e usa **ADO.NET SqlClient** o **Entity Framework**. Per altre informazioni, vedere [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](saas-tenancy-app-design-patterns.md).

- Gli **strumenti di database elastici** consentono agli sviluppatori di scalare il livello dati tramite procedure di partizionamento orizzontale standard usando librerie .NET e modelli di servizio di Azure. La gestione delle partizioni mediante la [libreria client dei database elastici][s-d-elastic-database-client-library] consente di automatizzare e semplificare molte delle attività infrastrutturali generalmente associate al partizionamento orizzontale.
- La **sicurezza a livello di riga** consente agli sviluppatori di archiviare in modo sicuro i dati di più tenant nello stesso database. I criteri di sicurezza a livello di riga escludono le righe che non appartengono al tenant che esegue una query. La centralizzazione della logica di filtro all'interno del database semplifica inoltre la manutenzione e riduce il rischio di errori di sicurezza. L'alternativa di affidarsi esclusivamente a codice cliente per applicare la sicurezza è troppo rischiosa.

L'utilizzo combinato di queste funzionalità consente a un'applicazione di memorizzare i dati di più tenant nello stesso database di partizionamento. Se i tenant condividono uno stesso database, inoltre, il costo per tenant è inferiore. La stessa applicazione può anche offrire ai tenant "premium" la possibilità di pagare solo per la partizione a singolo tenant dedicata. L'isolamento dei singoli tenant garantisce inoltre prestazioni più stabili. In un database a singolo tenant, infine, non sono presenti altri tenant con cui competere per l'acquisizione delle risorse.

L'obiettivo consiste nell'usare le API di [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) della libreria client dei database elastici per connettere automaticamente ogni tenant al database di partizione corretto. Solo una partizione contiene il valore TenantId relativo al tenant specificato. Il TenantId corrisponde alla *chiave di partizionamento orizzontale*. Dopo aver stabilito la connessione, i criteri di sicurezza a livello di riga all'interno del database garantiscono che il tenant specificato possa accedere solo alle righe di dati contenenti il relativo TenantId.

> [!NOTE]
> L'identificatore del tenant può essere costituito da più di una colonna. Per praticità, si presuppone in questo caso che il TenantId sia costituito da una sola colonna.

![Architettura delle applicazioni di blog][1]

## <a name="download-the-sample-project"></a>Scaricare il progetto di esempio

### <a name="prerequisites"></a>Prerequisiti

- Utilizzare Visual Studio (2012 o versione successiva) 
- Creare tre database SQL di Azure 
- Scaricare il progetto di esempio: [Strumenti di database elastici per SQL di Azure - Partizioni multi-tenant](http://go.microsoft.com/?linkid=9888163)
  - Immettere le informazioni per i database all'inizio di **Program.cs** 

Questo progetto estende quello descritto in [Strumenti di database elastici per SQL di Azure - Integrazione di Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) aggiungendo il supporto per i database di partizionamento multi-tenant. Viene compilata una semplice applicazione console per la creazione di blog e post, con quattro tenant e due database di partizionamento multi-tenant. Questa configurazione è illustrata nel diagramma precedente. 

Compilare ed eseguire l'applicazione. In questo modo, viene eseguito il bootstrap del gestore mappa di partizionamento degli strumenti di database elastici e vengono effettuati i test seguenti: 

1. Utilizzando Entity Framework e LINQ, creare un nuovo blog e quindi visualizzare tutti i blog per ciascun tenant
2. Utilizzando ADO.NET SqlClient, visualizzare tutti i blog per un tenant
3. Provare a inserire un blog per il tenant non corretto per verificare che venga generato un errore  

Si noti che poiché RLS non è stata ancora abilitata nei database di partizionamento, ciascuno di questi test rivela un problema: i tenant sono in grado di visualizzare i blog non appartenenti ad essi, e all'applicazione non viene impedito di inserire un blog per il tenant errato. Nella parte restante di questo articolo viene descritto come risolvere questi problemi mediante l'isolamento dei tenant con RLS. Sono disponibili due passaggi: 

1. **Livello applicazione**: modificare il codice dell'applicazione per impostare sempre il TenantId corrente in SESSION\_CONTEXT dopo l'apertura di una connessione. Il progetto di esempio imposta il TenantId in questo modo. 
2. **Livello dati**: creare criteri di sicurezza a livello di riga in ogni database di partizionamento per filtrare le righe in base al TenantId archiviato in SESSION\_CONTEXT. Creare criteri per ogni database di partizionamento; in caso contrario, le righe nelle partizioni multi-tenant non verranno filtrate. 

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. Livello applicazione: impostare il TenantId in SESSION\_CONTEXT

Per prima cosa, connettersi a un database di partizionamento tramite le API di routing dipendente dai dati della libreria client dei database elastici. L'applicazione deve comunque indicare al database quale TenantId userà la connessione, mentre il TenantId indica ai criteri di sicurezza a livello di riga quali righe dovranno essere escluse in quanto appartenenti ad altri tenant. Archiviare il TenantId corrente in [SESSION\_CONTEXT](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) della connessione.

In alternativa a SESSION\_CONTEXT, è possibile usare [CONTEXT\_INFO](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql), ma SESSION\_CONTEXT è comunque preferibile. SESSION\_CONTEXT poiché è più facile da usare, restituisce NULL per impostazione predefinita e supporta coppie chiave-valore.

### <a name="entity-framework"></a>Entity Framework

Per le applicazioni che usano Entity Framework, l'approccio più semplice consiste nell'impostare SESSION\_CONTEXT all'interno dell'override ElasticScaleContext descritto in [Routing dipendente dai dati con DbContext di Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Creare ed eseguire un comando SqlCommand che imposti TenantId in SESSION\_CONTEXT sull'oggetto shardingKey specificato per la connessione. Restituire quindi la connessione negoziata tramite il routing dipendente dai dati. In questo modo, è sufficiente scrivere una sola volta il codice per impostare SESSION\_CONTEXT. 

```csharp
// ElasticScaleContext.cs 
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection 
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
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

SESSION\_CONTEXT viene ora impostato automaticamente sul TenantId specificato ogni volta che si richiama ElasticScaleContext: 

```csharp
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
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

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

Per le applicazioni che usano ADO.NET SqlClient, creare una funzione wrapper intorno al metodo ShardMap.OpenConnectionForKey e fare in modo che il wrapper imposti automaticamente il TenantId presente in SESSION\_CONTEXT sul TenantId corretto prima di restituire una connessione. Per garantire che SESSION\_CONTEXT sia sempre impostato, è consigliabile aprire le connessioni usando solo questa funzione wrapper.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
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

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Livello dati: creare criteri di sicurezza a livello di riga

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Creare i criteri di sicurezza per filtrare le righe accessibili a ogni tenant

Ora che l'applicazione imposta SESSION\_CONTEXT sul TenantId corrente prima di eseguire la query, i criteri di sicurezza a livello di riga possono filtrare le query ed escludere le righe che contengono un TenantId diverso.  

La sicurezza a livello di riga viene implementata in Transact-SQL: una funzione definita dall'utente definisce la logica di accesso e i criteri di sicurezza associano questa funzione a un numero qualsiasi di tabelle. Per questo progetto,

1. la funzione verifica che l'applicazione sia connessa al database e che il TenantId archiviato in SESSION\_CONTEXT corrisponda al TenantId di una determinata riga.
    - In questo modo, viene connessa l'applicazione, non un altro utente SQL.

2. Un predicato di FILTRO consente alle righe che soddisfano il filtro impostato dal TenantId di passare attraverso le query SELECT, UPDATE e DELETE.
    - Un predicato di BLOCCO impedisce l'inserimento e l'aggiornamento delle righe che non soddisfano le condizioni del filtro.
    - Se SESSION\_CONTEXT non è stato impostato, verrà restituito NULL e non sarà possibile vedere o inserire alcuna riga. 

Per abilitare la sicurezza a livello di riga su tutte le partizioni, eseguire l'istruzione T-SQL seguente usando Visual Studio (SSDT), SSMS o lo script di PowerShell incluso nel progetto. Se si usano [processi di database elastici](sql-database-elastic-jobs-overview.md), è possibile automatizzare l'esecuzione di questa istruzione T-SQL in tutte le partizioni.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
    RETURNS TABLE     
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO 
```

> [!TIP]
> In un progetto complesso in cui potrebbe essere necessario aggiungere il predicato in centinaia di tabelle, questa operazione può essere noiosa. In questo caso, è disponibile una stored procedure helper che genera automaticamente criteri di sicurezza e aggiunge un predicato in tutte le tabelle di uno schema. Per altre informazioni, vedere il posto di blog relativo all'[applicazione della sicurezza a livello di riga a tutte le tabelle con lo script helper](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).

Se si esegue di nuovo l'applicazione di esempio, ora i tenant potranno visualizzare unicamente le righe appartenenti ad essi. L'applicazione, inoltre, non può inserire righe che appartengono a tenant diversi da quello attualmente connesso al database di partizionamento e non può aggiornare il TenantId in alcuna delle righe visibili. Se l'app tenta di effettuare una qualsiasi di queste due operazioni, viene generata un'eccezione DbUpdateException.

Se si aggiunge una nuova tabella in un secondo momento, MODIFICARE i criteri di sicurezza in modo da aggiungere predicati di FILTRO e di BLOCCO nella nuova tabella.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Aggiungere vincoli predefiniti per inserire automaticamente il TenantId per le operazioni INSERT

È possibile inserire un vincolo predefinito in ogni tabella per popolare automaticamente il TenantId con il valore attualmente archiviato in SESSION\_CONTEXT durante l'inserimento di righe. Di seguito è riportato un esempio. 

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs     
    ADD CONSTRAINT df_TenantId_Blogs      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts     
    ADD CONSTRAINT df_TenantId_Posts      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO 
```

A questo punto non è necessario che l'applicazione specifichi un TenantId durante l'inserimento di righe: 

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);     
        db.SaveChanges();   
    } 
}); 
```

> [!NOTE]
> Se si usano i vincoli predefiniti per un progetto Entity Framework, è consigliabile *NON* includere la colonna TenantId nel modello di dati EF. Il motivo è che le query Entity Framework forniscono automaticamente valori predefiniti che sostituiranno i vincoli predefiniti creati in T-SQL che usano SESSION\_CONTEXT.
> Per utilizzare i vincoli predefiniti nel progetto di esempio, ad esempio, è necessario rimuovere TenantId da DataClasses.cs (ed eseguire Add-Migration nella console di Gestione pacchetti) e utilizzare T-SQL per garantire che il campo esista unicamente nelle tabelle del database. In questo modo, Entity Framework non fornirà automaticamente valori predefiniti errati durante l'inserimento dei dati.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Facoltativo) Abilitare un *superuser* per accedere a tutte le righe

È possibile che alcune applicazioni creino un *superuser* che possa accedere a tutte le righe, ad esempio per abilitare la creazione di report relativi a tutti i tenant di tutte le partizioni o per eseguire operazioni di suddivisione/unione in partizioni che comportano lo spostamento di righe dei tenant tra più database.

Per abilitare un superuser, creare un nuovo utente SQL (`superuser` in questo esempio) in ogni database di partizionamento. Modificare quindi i criteri di sicurezza con una nuova funzione di predicato che consenta a questo utente di accedere a tutte le righe. Di seguito è illustrata una funzione di questo tipo.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Manutenzione 

- **Aggiunta di nuove partizioni**: eseguire lo script T-SQL per abilitare RLS in tutte le nuove partizioni. In caso contrario, le query su tali partizioni non verranno filtrate.
- **Aggiunta di nuove tabelle**: aggiungere un predicato di FILTRO e di BLOCCO ai criteri di sicurezza in tutte le partizioni ogni volta che si crea una nuova tabella. In caso contrario, le query sulla nuova tabella non verranno filtrate. Questa operazione può essere automatizzata tramite un trigger DDL, come descritto nel [blog relativo all'applicazione automatica della sicurezza a livello di riga alle tabelle create di recente](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Riepilogo

Gli strumenti di database elastici e la sicurezza a livello di riga possono essere utilizzati insieme per scalare orizzontalmente il livello di dati di un'applicazione con supporto sia per le partizioni multi-tenant, sia per quelle con tenant singolo. Le partizioni multi-tenant possono essere usate per archiviare i dati in modo più efficiente, in particolar modo nei casi in cui un elevato numero di tenant dispone solo di poche righe di dati. Le partizioni a singolo tenant possono essere usate invece per supportare tenant "premium" con requisiti di prestazioni e isolamento più rigidi.  Per altre informazioni, vedere [Sicurezza a livello di riga][rls].

## <a name="additional-resources"></a>Risorse aggiuntive

- [Che cos'è un pool elastico di Azure?](sql-database-elastic-pool.md)
- [Aumento del numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md)
- [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](saas-tenancy-app-design-patterns.md)
- [Authentication in multitenant apps, using Azure AD and OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Informazioni sull'applicazione Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Domande e richieste di funzionalità

Per domande, è disponibile il [forum sul database SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Aggiungere una richiesta di funzionalità nel [forum dei commenti e suggerimenti sul database SQL](https://feedback.azure.com/forums/217321-sql-database/).


<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md

