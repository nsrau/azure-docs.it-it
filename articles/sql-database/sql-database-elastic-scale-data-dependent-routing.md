<properties 
	pageTitle="Routing dipendente dei dati | Microsoft Azure" 
	description="Informazioni su come usare ShardMapManager per il routing basato sui dati, una funzionalità dei database elastici database per il Database SQL di Azure" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="torsteng;sidneyh"/>

#Routing dipendente dei dati

La classe **ShardMapManager** consente alle applicazioni ADO.NET di indirizzare facilmente query e comandi al database fisico corretto in un ambiente condiviso. Questo comportamento viene definito **routing dipendente dai dati** e costituisce un criterio fondamentale quando si usano database condivisi. Ogni query o transazione specifica in un'applicazione che usa il routing dipendente dai dati viene limitata all'accesso a un singolo database per richiesta.

Se si usa il routing dipendente dai dati, non è necessario che l'applicazione rilevi le diverse stringhe di connessione o i percorsi dei database associati a diverse sezioni di dati nell'ambiente partizionato. Piuttosto, il [Gestore mappe partizioni](sql-database-elastic-scale-shard-map-management.md) si occupa di fornire connessioni aperte al database corretto, quando necessario, in base ai dati contenuti nella mappa partizioni e al valore della chiave di partizionamento orizzontale di destinazione della richiesta dell'applicazione. Questa chiave è in genere *customer\_id*, *tenant\_id*, *date\_key* o un altro identificatore specifico che costituisce un parametro fondamentale della richiesta di database.

## Scaricare la libreria client

Per installare la libreria, passare a [Libreria client di database elastici](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## Uso di ShardMapManager in un'applicazione di routing dipendente dai dati 

Per le applicazioni che usano il routing dipendente dai dati, è necessario creare un'istanza di **ShardMapManager** una volta per ogni dominio di app durante l'inizializzazione usando la chiamata alla factory **GetSQLShardMapManager**.

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

In questo esempio viene eseguita l'inizializzazione sia della classe **ShardMapManager** sia di un oggetto **ShardMap** specifico in essa contenuto.

Per un'applicazione che non gestisce direttamente la mappa partizioni, le credenziali usate nel metodo factory per ottenere **ShardMapManager** (nell'esempio precedente, *smmConnectionString*) dovrebbero avere semplicemente autorizzazioni di sola lettura nel database **Mappa globale partizioni** a cui fa riferimento la stringa di connessione. Queste credenziali sono in genere diverse da quelle usate per aprire connessioni al gestore delle mappe partizioni. Vedere anche [Utilizzo delle credenziali nelle librerie client dei database elastici](sql-database-elastic-scale-manage-credentials.md).

## Chiamata del routing dipendente dai dati 

Il metodo **ShardMap.OpenConnectionForKey(key, connectionString, connectionOptions)** restituisce una connessione ADO.Net pronta per l'invio di comandi al database appropriato in base al valore del parametro **key**. Le informazioni sulla partizione vengono memorizzate nella cache dell'applicazione tramite **ShardMapManager**, in modo che in genere tali richieste non comportino una ricerca di database sul database della **mappa globale partizioni**.

* Il parametro **key** viene usato come chiave di ricerca nella mappa partizioni per determinare il database appropriato per la richiesta. 

* Il parametro **connectionString** viene usato per passare solo le credenziali utente per la connessione specifica. Nel parametro *connectionString* non viene incluso il nome del database o del server, perché il metodo determina il database e il server usando **ShardMap**.

* L'enumerazione **connectionOptions** viene usata per indicare se la convalida viene o meno eseguita quando viene fornita la connessione aperta. **ConnectionOptions.Validate** è l'opzione consigliata. In un ambiente in cui le mappe partizioni possono essere modificate e le righe possono essere spostate in altri database come risultato di operazioni di divisione o unione, la convalida garantisce che la ricerca memorizzata nella cache del database in base al valore di una chiave sia corretta. La convalida prevede una breve query sulla mappa locale partizioni nel database di destinazione (non sulla mappa globale partizioni) prima che la connessione venga fornita all'applicazione.

Se la convalida della mappa locale partizioni non riesce (indicando che la cache non è corretta), tramite Gestore mappe partizioni viene eseguita una query sulla mappa globale partizioni per ottenere il nuovo valore corretto per la ricerca, aggiornare la cache e ottenere e restituire la connessione al database appropriato.

L’unico caso in cui **ConnectionOptions.None** (non viene eseguita la convalida) è accettabile si verifica quando le modifiche al mapping di partizioni non sono previste mentre l'applicazione è online. In tal caso, i valori memorizzati nella cache possono essere considerati sempre corretti e la chiamata di convalida round trip aggiuntiva al database di destinazione può essere tranquillamente ignorata. Che può ridurre le latenze della transazione e il traffico del database. L'enumerazione **connectionOptions** può anche essere impostata tramite un valore in un file di configurazione per indicare se le modifiche di partizionamento sono o meno previste durante un intervallo di tempo.

Di seguito è riportato un esempio di codice che usa il gestore mappe partizioni per eseguire il routing dipendente dai dati in base al valore di una chiave numero intero **CustomerID**, usando un oggetto **ShardMap** denominato **customerShardMap**.

## Esempio: routing dipendente dai dati 

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

Il metodo **OpenConnectionForKeyAsync** è disponibile anche se l'applicazione consente di usare la programmazione asincrona con ADO.Net. Il comportamento di tale metodo è l'equivalente del routing dipendente dai dati del metodo **ADO.Net Connection.OpenAsync**.

## Integrazione con la gestione degli errori temporanei 

Una procedura consigliata nello sviluppo di applicazioni di accesso ai dati nel cloud consiste nel garantire che gli errori temporanei durante la connessione o l'esecuzione di query al database vengano rilevati dall'app e che le operazioni vengono ripetute più volte prima di generare un errore. La gestione degli errori temporanei per le applicazioni cloud è illustrata nella pagina relativa alla [gestione degli errori temporanei](http://msdn.microsoft.com/library/dn440719(v=pandp.60).aspx).
 
La gestione degli errori temporanei può coesistere naturalmente con il modello di routing dipendente dai dati. Il requisito principale consiste nel ripetere l'intera richiesta di accesso ai dati, incluso il blocco **using** che ha ottenuto la connessione di routing dipendente dai dati. L'esempio precedente potrebbe essere riscritto come riportato di seguito (notare la modifica evidenziata).

### Esempio: routing dipendente dai dati con gestione degli errori temporanei 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() => </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Connessione alla partizione per l’ID cliente 
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

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_1210_2015-->