<properties title="Managing Elastic Scale Credentials" pageTitle="Gestione delle credenziali di scalabilità elastica" description="How to set the right level of credentials, admin to read-only, for Elastic Scale apps." metaKeywords="Azure SQL Database, elastic scale, about user credentials in elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Gestione delle credenziali di scalabilità elastica  

Nelle [API del client di Scalabilità elastica](http://go.microsoft.com/?linkid=9862605) vengono usate credenziali per i diversi tipi di operazioni, in particolare per la creazione o la modifica di un [Gestore mappe partizioni](http://go.microsoft.com/?linkid=9862595), per fare riferimento a un Gestore mappe partizioni esistente per ottenere informazioni sulle partizioni, nonché per la connessione alle partizioni. Di seguito vengono illustrate le credenziali per questi tipi di operazioni. 


* **Credenziali di gestione per l'accesso alla mappa partizioni**: Le credenziali di gestione vengono usate quando si crea un'istanza di un oggetto **ShardMapManager** per applicazioni che modificano le mappe partizioni. L'utente delle API di Scalabilità elastica devono creare gli utenti SQL e gli account di accesso SQL necessari e assicurarsi di concedere loro autorizzazioni in lettura/scrittura per il database della mappa globale partizioni, nonché per tutti i database delle partizioni. Queste credenziali vengono usate per la gestione della mappa globale partizioni e delle mappe locali partizioni quando si apportano modifiche alla mappa partizioni. Usare ad esempio le credenziali di gestione per creare un'istanza dell'oggetto di gestione mappa partizioni, come illustrato nel codice seguente: 

        // Obtain a shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmAdminConnectionString, 
                ShardMapManagerLoadPolicy.Lazy 
        ); 


     La variabile **smmAdminConnectionString** è una stringa di connessione che contiene le credenziali di gestione. L'ID utente e la password forniscono accesso in lettura/scrittura sia al database della mappa partizioni che alle singole partizioni. La stringa di connessione alla gestione include anche il nome del server e il nome del database per identificare il database della mappa globale partizioni. La seguente è una stringa di connessione tipica usata a tale scopo:

        "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" 

* **Credenziali utente per l'accesso a Gestore mappe partizioni**:  Quando si crea un'istanza di Gestore mappe partizioni in un'applicazione che non verrà usata per amministrare mappe partizioni, usare credenziali cui sono assegnate autorizzazioni di sola lettura per la mappa globale partizioni. Le informazioni recuperate dalla mappa globale partizioni con queste credenziali verranno usate per il [routing dipendente dai dati](./sql-database-elastic-scale-data-dependent-routing.md) e per popolare la cache della mappa partizioni nel client. Le credenziali vengono fornite in base allo stesso modello di chiamata a **GetSqlShardMapManager** come illustrato in precedenza: 
 
        // Obtain shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmReadOnlyConnectionString, 
                ShardMapManagerLoadPolicy.Lazy
        );  

     Si noti l'uso di **smmReadOnlyConnectionString** per rispecchiare le diverse credenziali per questo accesso usate da utenti **non amministratori**. Queste credenziali non devono infatti fornire autorizzazioni di scrittura per la mappa globale partizioni. 

* **Credenziali utente per l'accesso ai dati utente**: Sono inoltre necessarie credenziali aggiuntive quando si usa il metodo **OpenConnectionForKey** per accedere a una partizione associata a una chiave. Queste credenziali devono fornire autorizzazioni per l'accesso di sola lettura alle tabelle della mappa locale partizioni che risiedono nella partizione. Tale comportamento è necessario per eseguire la convalida della connessione per il routing dipendente dai dati nella partizione. Il frammento di codice seguente illustra l'uso delle credenziali utente per l'accesso ai dati utente nel contesto del routing dipendente dai dati: 
 
        using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
        targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

    In questo esempio **smmUserConnectionString** contiene la stringa di connessione per le credenziali utente. Questa è invece una stringa di connessione tipica per le credenziali utente per il database SQL di Azure: 

        "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;"  

    Si noti che la stringa di connessione non contiene un nome di server e un nome di database, in quando la chiamata a **OpenConnectionForKey** reindirizzerà automaticamente la connessione alla partizione corretta in base alla chiave. Di conseguenza non è necessario fornire il nome del database e il nome del server. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
