<properties 
	pageTitle="Gestione delle credenziali nella libreria client dei database elastici | Microsoft Azure" 
	description="Come impostare il livello corretto di credenziali, da amministratore a sola lettura, per le app dei database elastici." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="ddove" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2016" 
	ms.author="ddove"/>

# Credenziali usate per accedere alla libreria client dei database elastici

La [libreria client dei database elastici](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) usa tre diversi tipi di credenziali per accedere al [gestore delle mappe delle partizioni](sql-database-elastic-scale-shard-map-management.md). A seconda delle esigenze, usare le credenziali con il minimo livello possibile di accesso.

* **Credenziali di gestione**: per la creazione o la modifica di un gestore di mappa di partizionamento. (Vedere il [glossario](sql-database-elastic-scale-glossary.md).) 
* **Credenziali di accesso**: per accedere a un gestore di mappa di partizione esistente per ottenere informazioni sulle partizioni.
* **Credenziali di connessione**: per connettersi alle partizioni. 

Vedere anche [Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md).
 
## Informazioni sulle credenziali di gestione

Le credenziali di gestione vengono usate per creare di un oggetto [**ShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) per applicazioni che modificano le mappe partizioni. Ad esempio, vedere [Aggiunta di una partizione utilizzando gli strumenti di database elastici](sql-database-elastic-scale-add-a-shard.md) e [Routing dipendente dei dati](sql-database-elastic-scale-data-dependent-routing.md). L'utente della libreria client dei database elastici crea gli utenti e gli account di accesso SQL e garantisce che ad ognuno vengano concesse autorizzazioni di lettura/scrittura per il database della mappa globale partizioni, nonché per tutti i database delle partizioni. Queste credenziali vengono usate per la gestione della mappa globale partizioni e delle mappe locali partizioni quando si apportano modifiche alla mappa partizioni. Ad esempio, usare le credenziali di gestione per creare l'oggetto di gestione mappa partizioni (usando [**GetSqlShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx):

	// Obtain a shard map manager. 
	ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
	        smmAdminConnectionString, 
	        ShardMapManagerLoadPolicy.Lazy 
	); 

La variabile **smmAdminConnectionString** è una stringa di connessione che contiene le credenziali di gestione. L'ID utente e la password forniscono accesso in lettura/scrittura sia al database della mappa partizioni che alle singole partizioni. La stringa di connessione alla gestione include anche il nome del server e il nome del database per identificare il database della mappa globale partizioni. La seguente è una stringa di connessione tipica usata a tale scopo:

	 "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

Non usare valori nel formato "username@server", usare solo il valore "username". Infatti, le credenziali devono funzionare sia per il database del gestore mappe partizioni sia per le singole partizioni, che possono essere in server diversi.

## Credenziali di accesso
  
Quando si crea un gestore mappe partizioni in un'applicazione che non amministra mappe partizioni, usare credenziali a cui sono assegnate autorizzazioni di sola lettura per la mappa globale partizioni. Le informazioni recuperate dalla mappa globale partizioni con queste credenziali vengono utilizzate per il [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) e per popolare la cache della mappa di partizionamento orizzontale nel client. Le credenziali vengono fornite in base allo stesso modello di chiamata a **GetSqlShardMapManager** come illustrato in precedenza:

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

Si noti l'uso di **smmReadOnlyConnectionString** per rispecchiare le diverse credenziali per questo accesso usate da utenti **non amministratori**. Queste credenziali non devono infatti fornire autorizzazioni di scrittura per la mappa globale partizioni.

## Credenziali di connessione 

Sono inoltre necessarie credenziali aggiuntive quando si usa il metodo [**OpenConnectionForKey**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) per accedere a una partizione associata a una chiave di partizionamento orizzontale. Queste credenziali devono fornire autorizzazioni per l'accesso di sola lettura alle tabelle della mappa locale partizioni che risiedono nella partizione. Tale comportamento è necessario per eseguire la convalida della connessione per il routing dipendente dai dati nella partizione. Questo frammento di codice consente l'accesso ai dati nel contesto di routing dipendente dai dati:
 
	using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
	targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

In questo esempio **smmUserConnectionString** contiene la stringa di connessione per le credenziali utente. Questa è invece una stringa di connessione tipica per le credenziali utente per il database SQL di Azure:

	"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

Come con le credenziali di amministratore, nessun valore sotto forma di "username@server". Usare invece solo "username". Si noti inoltre che la stringa di connessione non contiene un nome di server e un nome di database, Ciò accade perché la chiamata a **OpenConnectionForKey** reindirizzerà automaticamente la connessione alla partizione corretta in base alla chiave. Di conseguenza, non vengono forniti il nome del database e il nome del server.

## Vedere anche
[Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md)

[Protezione del Database SQL](sql-database-security.md)

[Introduzione ai processi di Database Elastici](sql-database-elastic-jobs-getting-started.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0601_2016-->