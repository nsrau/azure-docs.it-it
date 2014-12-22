<properties title="Data Dependent Routing" pageTitle="Routing dipendente dai dati" description="How to use the ShardMapManager for data-dependent routing, a feature of Elastic Scale for Azure SQL DB" metaKeywords="sharding scaling, Azure SQL DB sharding, elastic scale, multi-shard, multishard, querying" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Routing dipendente dai dati

La classe **ShardMapManager** fornisce alle applicazioni ADO.NET la capacità di indirizzare facilmente query e comandi al database fisico corretto in un ambiente condiviso. Questo comportamento viene definito **routing dipendente dai dati** e costituisce un criterio fondamentale quando si usano database condivisi. Ogni query o transazione specifica in un'applicazione che usa il routing dipendente dai dati viene limitata all'accesso a un singolo database per richiesta.  

Se si usa il routing dipendente dai dati, non è necessario che l'applicazione rilevi le diverse stringhe di connessione o i percorsi dei database associati a diverse sezioni di dati nell'ambiente partizionato. È piuttosto [Gestore mappe partizioni](./sql-database-elastic-scale-shard-map-management.md) che si occupa di fornire connessioni aperte al database corretto, quando necessario, in base ai dati contenuti nella mappa partizioni e al valore della chiave di partizionamento orizzontale di destinazione della richiesta dell'applicazione. Questa chiave è in genere *customer_id* *tenant_id* *date_key* o un altro identificatore specifico che costituisce un parametro fondamentale della richiesta di database. 

## Uso di ShardMapManager in un'applicazione di routing dipendente dai dati 

Per le applicazioni che usano il routing dipendente dai dati, è necessario creare un'istanza di **ShardMapManager** una volta per dominio app, durante l'inizializzazione, usando la chiamata alla factory **GetSQLShardMapManager**.

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

In questo esempio, viene eseguita l'inizializzazione sia di **ShardMapManager** che una **ShardMap** specifica in essa contenuta. 

Per un'applicazione che non gestisce direttamente la mappa partizioni, le credenziali usate nel metodo factory per ottenere **ShardMapManager** (nell'esempio precedente, *smmConnectionString*) dovrebbero avere semplicemente autorizzazioni di sola lettura nel database **Mappa globale partizioni** a cui fa riferimento la stringa di connessione. Queste credenziali sono in genere diverse da quelle usate per aprire connessioni al gestore delle mappe partizioni. Vedere anche l'articolo relativo alla [gestione delle credenziali di Scalabilità elastica](sql-database-elastic-scale-manage-credentials.md). 

##Chiamata del outing dipendente dai dati 

Il metodo **ShardMap.OpenConnectionForKey(key, connectionString, connectionOptions)** restituisce una connessione ADO.Net pronta a fornire comandi al database appropriato in base al valore del parametro> **key**. Le informazioni sulla partizione vengono memorizzate nella cache dell'applicazione tramite **ShardMapManager**, in modo che in genere tali richieste non comportino una ricerca di database sul database **Mappa globale partizioni**. 

* Il parametro **key** viene usato come chiave di ricerca nella mappa partizioni per determinare il database appropriato per la richiesta. 

* Il parametro **connectionString** viene usato per passare solo le credenziali utente per la connessione specifica. Nel parametro *connectionString* non viene incluso il nome del database o del server, perché il metodo determina il database e il server usando **ShardMap**. 

* L'enumerazione **connectionOptions** viene usata per indicare se la convalida viene o meno eseguita quando viene fornita la connessione aperta. **ConnectionOptions.Validate** è l'opzione consigliata. In un ambiente in cui le mappe partizioni possono essere modificate e le righe possono essere spostate in altri database come risultato di operazioni di divisione o unione, la convalida garantisce che la ricerca memorizzata nella cache del database in base al valore di una chiave sia corretta. La convalida prevede una breve query sulla mappa locale partizioni nel database di destinazione (non sulla mappa globale partizioni) prima che la connessione venga fornita all'applicazione. 

Se la convalida della mappa locale partizioni non riesce (indicando che la cache non è corretta), tramite Gestore mappe partizioni viene eseguita una query sulla mappa globale partizioni per ottenere il nuovo valore corretto per la ricerca, aggiornare la cache e ottenere e restituire la connessione al database appropriato. 

Il solo caso in cui **ConnectionOptions.None** (non viene eseguita la convalida) è accettabile si verifica quando le modifiche al mapping di partizioni non sono previste mentre l'applicazione è online. In tal caso, i valori memorizzati nella cache possono essere considerati sempre corretti e la chiamata di convalida round trip aggiuntiva al database di destinazione può essere tranquillamente ignorata. Che può ridurre le latenze della transazione e il traffico del database. L'enumerazione **connectionOptions** può anche essere impostata tramite un valore in un file di configurazione per indicare se le modifiche di partizionamento sono o meno previste durante un intervallo di tempo.  

Di seguito è riportato un esempio di codice che usa Gestore mappe partizioni per eseguire il routing dipendente dai dati in base al valore di una chiave numero intero **CustomerID**, usando un oggetto **ShardMap** denominato **customerShardMap**.  

###Esempio: Routing dipendente dai dati 

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connection to the shard for that customer ID
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

Si noti che invece di usare un costruttore per **SqlConnection**, seguito da una chiamata **Open()** all'oggetto connessione, viene usato il metodo **OpenConnectionForKey** e viene fornita una nuova connessione già aperta al database corretto. Le connessioni usate in questo modo usufruiscono comunque del pool di connessioni ADO.Net. Fino a quando le transazioni e le richieste possono essere soddisfatti da una partizione alla volta, questa dovrebbe essere l'unica modifica necessaria in un'applicazione che già usa ADO.Net. 

##Integrazione con la gestione degli errori temporanei 

Una procedura consigliata nello sviluppo di applicazioni di accesso ai dati nel cloud consiste nel garantire che gli errori temporanei durante la connessione o l'esecuzione di query al database vengano rilevati dall'app e che le operazioni vengono ripetute più volte prima di generare un errore. La gestione degli errori temporanei per le applicazioni cloud è illustrata nell'articolo relativo alla [gestione degli errori temporanei](http://msdn.microsoft.com/it-it/library/dn440719\(v=pandp.60\).aspx). 
 
La gestione degli errori temporanei può coesistere naturalmente con il modello di routing dipendente dai dati. Il requisito principale consiste nel ripetere l'intera richiesta di accesso ai dati, incluso il blocco **using** che ha ottenuto la connessione di routing dipendente dai dati. L'esempio precedente potrebbe essere riscritto come riportato di seguito (notare la modifica evidenziata). 

###Esempio: Routing dipendente dai dati con gestione degli errori temporanei 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() =&gt; </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Connection to the shard for that customer ID 
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Execute a simple command 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @&quot;UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID&quot;; 

            cmd.Parameters.AddWithValue(&quot;@customerID&quot;, customerId); 
            cmd.Parameters.AddWithValue(&quot;@newPersonID&quot;, newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine(&quot;Update completed&quot;); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


I pacchetti necessari per implementare la gestione degli errori temporanei vengono scaricati automaticamente quando si compila l'applicazione Starter Kit di scalabilità elastica. I pacchetti sono disponibili separatamente anche nella pagina relativa al [blocco applicazione di accesso ai dati della libreria Enterprise](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/). Usare la versione 6.0 o successiva. 

##Coerenza delle transazioni 

Le proprietà transazionali sono garantite per tutte le operazioni locali rispetto a una partizione. Ad esempio, le transazioni inviate tramite il routing dipendente dai dati vengono eseguite nell'ambito della partizione di destinazione per la connessione. Al momento, non sono disponibili funzionalità per l'inserimento di più connessioni in una transazione e pertanto non esistono garanzie transazionale per le operazioni eseguite tra partizioni.  

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
