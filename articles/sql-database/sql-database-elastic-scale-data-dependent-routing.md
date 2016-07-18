<properties 
	pageTitle="Routing dipendente dei dati | Microsoft Azure" 
	description="Come usare la classe ShardMapManager nelle app .NET per il routing basato sui dati, una funzionalità dei database elastici per il Database SQL di Azure" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2016" 
	ms.author="torsteng"/>

#Routing dipendente dei dati

Il **routing dipendente dai dati** è la possibilità di usare i dati in una query per instradare la richiesta a un database appropriato. Questo costituisce un criterio fondamentale quando si usano database partizionati. Per instradare la richiesta è anche possibile usare il contesto della richiesta stessa, soprattutto se la chiave di partizionamento orizzontale non fa parte della query. Ogni query o transazione specifica in un'applicazione che usa il routing dipendente può accedere a un unico database per richiesta. Per gli strumenti elastici del database SQL di Azure, il routing viene effettuato con la **[classe ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)** nelle applicazioni ADO.NET.

Per l'applicazione non è necessario rilevare le diverse stringhe di connessione o i percorsi dei database associati a diverse sezioni di dati nell'ambiente partizionato. È [Shard Map Manager](sql-database-elastic-scale-shard-map-management.md) che, quando necessario, apre le connessioni ai database corretti in base ai dati contenuti nella mappa partizioni e al valore della chiave di partizionamento orizzontale, che costituisce la destinazione della richiesta dell'applicazione. La chiave è in genere *customer\_id*, *tenant\_id*, *date\_key* o un altro identificatore specifico che costituisce un parametro fondamentale della richiesta di database.

Per altre informazioni, vedere la pagina relativa [all'aumento del numero di istanze di SQL Server con il routing dipendente dai dati](https://technet.microsoft.com/library/cc966448.aspx).

## Scaricare la libreria client

Per ottenere la classe, installare la [libreria client dei database elastici](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## Uso di ShardMapManager in un'applicazione di routing dipendente dai dati 

Le applicazioni devono creare un'istanza di **ShardMapManager** durante l'inizializzazione, tramite la chiamata alla factory **[GetSQLShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)**. In questo esempio viene eseguita l'inizializzazione sia della classe **ShardMapManager** sia di un oggetto **ShardMap** specifico in essa contenuto. Questo esempio illustra i metodi GetSqlShardMapManager e [GetRangeShardMap](https://msdn.microsoft.com/library/azure/dn824173.aspx).

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

### Usare credenziali con i privilegi più bassi possibile per ottenere la mappa partizioni

Se un'applicazione non gestisce direttamente la mappa partizioni, le credenziali usate nel metodo factory devono disporre solo di autorizzazioni di sola lettura per il database della **mappa globale partizioni**. Queste credenziali sono in genere diverse da quelle usate per aprire connessioni al gestore delle mappe partizioni. Vedere anche [Credenziali usate per accedere alla libreria client dei database elastici](sql-database-elastic-scale-manage-credentials.md).

## Chiamare il metodo OpenConnectionForKey

Il **[metodo ShardMap.OpenConnectionForKey ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)** restituisce una connessione ADO.Net pronta per l'invio di comandi al database appropriato in base al valore del parametro **key**. Le informazioni sulla partizione vengono memorizzate nella cache dell'applicazione tramite **ShardMapManager**, in modo che in genere tali richieste non comportino una ricerca di database sul database della **mappa globale partizioni**.

	// Syntax: 
	public SqlConnection OpenConnectionForKey<TKey>(
		TKey key,
		string connectionString,
		ConnectionOptions options
	)


* Il parametro **key** viene usato come chiave di ricerca nella mappa partizioni per determinare il database appropriato per la richiesta.

* Il parametro **connectionString** viene usato per passare solo le credenziali utente per la connessione specifica. Nel parametro *connectionString* non viene incluso il nome del database o del server, perché il metodo determina il database e il server usando **ShardMap**.

* Il parametro **[connectionOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)** deve essere impostato su **ConnectionOptions.Validate** per un ambiente in cui le mappe partizioni possono cambiare e le righe possono passare ad altri database a seguito di operazioni di divisione o unione. Ciò prevede una breve query sulla mappa locale partizioni nel database di destinazione (non sulla mappa globale partizioni) prima che la connessione venga fornita all'applicazione.

Se la convalida della mappa locale partizioni non riesce (indicando che la cache non è corretta), tramite Gestore mappe partizioni viene eseguita una query sulla mappa globale partizioni per ottenere il nuovo valore corretto per la ricerca, aggiornare la cache e ottenere e restituire la connessione al database appropriato.

Usare **ConnectionOptions.None** solo se non sono previste modifiche al mapping delle partizioni mentre l'applicazione è online. In tal caso, i valori memorizzati nella cache possono essere considerati sempre corretti e la chiamata di convalida round trip aggiuntiva al database di destinazione può essere tranquillamente ignorata. Ciò riduce il traffico del database. L'enumerazione **connectionOptions** può anche essere impostata tramite un valore in un file di configurazione per indicare se le modifiche di partizionamento sono o meno previste durante un intervallo di tempo.

Questo esempio usa il valore di una chiave Integer **CustomerID**, tramite un oggetto **ShardMap** denominato **customerShardMap**.

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connect to the shard for that customer ID. No need to call a SqlConnection 
	// constructor followed by the Open method.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command. 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

Il metodo **OpenConnectionForKey** restituisce una nuova connessione già aperta al database corretto. Le connessioni usate in questo modo usufruiscono comunque del pool di connessioni ADO.Net. Fino a quando le transazioni e le richieste possono essere soddisfatti da una partizione alla volta, questa dovrebbe essere l'unica modifica necessaria in un'applicazione che già usa ADO.Net.

È disponibile anche il metodo **[OpenConnectionForKeyAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)** se l'applicazione usa la programmazione asincrona con ADO.Net. Il comportamento di tale metodo è l'equivalente del routing dipendente da dati del metodo ADO.Net **[Connection.OpenAsync](https://msdn.microsoft.com/library/hh223688(v=vs.110).aspx)**.

## Integrazione con la gestione degli errori temporanei 

Una procedura consigliata nello sviluppo di applicazioni di accesso ai dati nel cloud consiste nel garantire che gli errori temporanei vengano rilevati dall'app e che le operazioni vengano ripetute più volte prima di generare un errore. La gestione degli errori temporanei per le applicazioni cloud è illustrata nella pagina relativa alla [gestione degli errori temporanei]https://msdn.microsoft.com/library/dn440719(v=pandp.60).aspx).
 
La gestione degli errori temporanei può coesistere naturalmente con il modello di routing dipendente dai dati. Il requisito principale consiste nel ripetere l'intera richiesta di accesso ai dati, incluso il blocco **using** che ha ottenuto la connessione di routing dipendente dai dati. L'esempio precedente potrebbe essere riscritto come riportato di seguito (notare la modifica evidenziata).

### Esempio: routing dipendente dai dati con gestione degli errori temporanei 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() => </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Connect to the shard for a customer ID. 
        usando (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Eseguire un comando semplice 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

            cmd.Parameters.AddWithValue("@customerID", customerId); 
            cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine("Update completed"); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


I pacchetti necessari per implementare la gestione degli errori temporanei vengono scaricati automaticamente quando si compila l'applicazione esempio dei database elastici. I pacchetti sono disponibili anche separatamente nella pagina relativa al [blocco di applicazioni di gestione degli errori temporanei nella libreria Enterprise](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/). Usare la versione 6.0 o successiva.

## Coerenza delle transazioni 

Le proprietà transazionali sono garantite per tutte le operazioni locali rispetto a una partizione. Ad esempio, le transazioni inviate tramite il routing dipendente dai dati vengono eseguite nell'ambito della partizione di destinazione per la connessione. Al momento, non sono disponibili funzionalità per l'inserimento di più connessioni in una transazione e pertanto non esistono garanzie transazionale per le operazioni eseguite tra partizioni.

## Passaggi successivi
Per disconnettere o riconnettere una partizione, vedere [Uso della classe RecoveryManager per correggere i problemi delle mappe partizioni](sql-database-elastic-database-recovery-manager.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0706_2016-->