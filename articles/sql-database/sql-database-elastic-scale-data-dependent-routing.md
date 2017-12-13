---
title: Routing dipendente dai dati con il database SQL di Azure | Documentazione Microsoft
description: "Come usare la classe ShardMapManager nelle app .NET per il routing dipendente dai dati, una funzionalità dei database partizionati del database SQL di Azure"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: cad09e15-5561-4448-aa18-b38f54cda004
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: ddove
ms.openlocfilehash: 2add03568f1d111010cdfb49d850d33cdab8e21b
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="data-dependent-routing"></a>Routing dipendente dei dati
**Routing dipendente dei dati** è la possibilità di usare i dati in una query per instradare la richiesta a un database appropriato. Questo costituisce un criterio fondamentale quando si usano database partizionati. Per instradare la richiesta è anche possibile usare il contesto della richiesta stessa, soprattutto se la chiave di partizionamento orizzontale non fa parte della query. Ogni query o transazione specifica in un'applicazione che usa il routing dipendente può accedere a un unico database per richiesta. Per gli strumenti elastici del database SQL di Azure, il routing viene eseguito con la classe **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager) o [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)).

Per l'applicazione non è necessario rilevare le diverse stringhe di connessione o i percorsi dei database associati a diverse sezioni di dati nell'ambiente partizionato. È [Shard Map Manager](sql-database-elastic-scale-shard-map-management.md) che, quando necessario, apre le connessioni ai database corretti in base ai dati contenuti nella mappa partizioni e al valore della chiave di partizionamento orizzontale, che costituisce la destinazione della richiesta dell'applicazione. La chiave è in genere *customer_id*, *tenant_id*, *date_key* o un altro identificatore specifico che costituisce un parametro fondamentale della richiesta del database. 

Per altre informazioni, vedere la pagina relativa [all'aumento del numero di istanze di SQL Server con il routing dipendente dai dati](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Scaricare la libreria client
Per eseguire il download:
* Per la versione Java della libreria, vedere il [repository centrale Maven](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* Per la versione .NET della libreria, vedere [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Uso di ShardMapManager in un'applicazione di routing dipendente dai dati
Le applicazioni devono creare un'istanza di **ShardMapManager** durante l'inizializzazione, usando la chiamata alla factory **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager) o [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)). In questo esempio, viene eseguita l'inizializzazione sia di **ShardMapManager** che una **ShardMap** specifica in essa contenuta. Questo esempio illustra i metodi GetSqlShardMapManager e GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getrangeshardmap) o [.NET](https://msdn.microsoft.com/library/azure/dn824173.aspx)).

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Usare credenziali con i privilegi più bassi possibile per ottenere la mappa partizioni
Se un'applicazione non gestisce direttamente la mappa partizioni, le credenziali usate nel metodo factory devono disporre solo di autorizzazioni di sola lettura per il database della **mappa globale partizioni** . Queste credenziali sono in genere diverse da quelle usate per aprire connessioni al gestore delle mappe partizioni. Vedere anche [Credenziali usate per accedere alla libreria client dei database elastici](sql-database-elastic-scale-manage-credentials.md). 

## <a name="call-the-openconnectionforkey-method"></a>Chiamare il metodo OpenConnectionForKey
Il metodo **ShardMap.OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkey) o [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)) restituisce una connessione pronta per l'invio di comandi al database appropriato in base al valore del parametro **key**. Le informazioni sulla partizione vengono memorizzate nella cache dell'applicazione tramite **ShardMapManager**, in modo che in genere tali richieste non comportino una ricerca di database sul database **Mappa globale partizioni**. 

```Java
// Syntax: 
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax: 
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```
* Il parametro **key** viene usato come chiave di ricerca nella mappa partizioni per determinare il database appropriato per la richiesta. 
* Il parametro **connectionString** viene usato per passare solo le credenziali utente per la connessione specifica. In *connectionString* non viene incluso il nome del database o del server perché il metodo determina il database e il server usando **ShardMap**. 
* Il parametro **connectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._connection_options) o [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)) deve essere impostato su **ConnectionOptions.Validate** per un ambiente in cui le mappe partizioni possono essere modificate e le righe possono essere spostate in altri database in seguito a operazioni di divisione o unione. Ciò prevede una breve query sulla mappa locale partizioni nel database di destinazione (non sulla mappa globale partizioni) prima che la connessione venga fornita all'applicazione. 

In caso di esito negativo della convalida in base alla mappa partizioni locale (che indica che la cache non è corretta), il gestore delle mappe partizioni esegue una query sulla mappa partizioni globale per ottenere il nuovo valore corretto per la ricerca, aggiornare la cache e ottenere e restituire la connessione al database appropriato. 

Usare **ConnectionOptions.None** solo se non sono previste modifiche al mapping delle partizioni mentre l'applicazione è online. In tal caso, i valori memorizzati nella cache possono essere considerati sempre corretti e la chiamata di convalida round trip aggiuntiva al database di destinazione può essere tranquillamente ignorata. Ciò riduce il traffico del database. L'enumerazione **connectionOptions** può anche essere impostata tramite un valore in un file di configurazione per indicare se le modifiche di partizionamento sono o meno previste durante un intervallo di tempo.  

Questo esempio usa il valore di una chiave Integer **CustomerID**, tramite un oggetto **ShardMap** denominato **customerShardMap**.  

```Java 
int customerId = 12345; 
int productId = 4321; 
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345; 
int newPersonId = 4321; 

// Connect to the shard for that customer ID. No need to call a SqlConnection 
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
{ 
    // Execute a simple command. 
    SqlCommand cmd = conn.CreateCommand(); 
    cmd.CommandText = @"UPDATE Sales.Customer 
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID"; 

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
    cmd.ExecuteNonQuery(); 
}  
```

Il metodo **OpenConnectionForKey** restituisce una nuova connessione già aperta al database corretto. Le connessioni utilizzate in questo modo usufruiscono comunque del pool di connessioni. 

Se l'applicazione usa la programmazione asincrona, è disponibile anche il metodo **OpenConnectionForKeyAsync** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkeyasync) o [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)).

## <a name="integrating-with-transient-fault-handling"></a>Integrazione con la gestione degli errori temporanei
Una procedura consigliata nello sviluppo di applicazioni di accesso ai dati nel cloud consiste nel garantire che gli errori temporanei vengano rilevati dall'app e che le operazioni vengano ripetute più volte prima di generare un errore. La gestione degli errori temporanei per le applicazioni cloud è illustrata negli articoli in proposito relativi a [Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling) e [.NET](https://msdn.microsoft.com/library/dn440719\(v=pandp.60\).aspx). 

La gestione degli errori temporanei può coesistere naturalmente con il modello di routing dipendente dai dati. Il requisito principale consiste nel ripetere l'intera richiesta di accesso ai dati, incluso il blocco **using** che ha ottenuto la connessione di routing dipendente dai dati. L'esempio precedente potrebbe essere riscritto come segue. 

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Esempio: routing dipendente dai dati con gestione degli errori temporanei
```Java 
int customerId = 12345; 
int productId = 4321; 
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345; 
int newPersonId = 4321; 

Configuration.SqlRetryPolicy.ExecuteAction(() =&gt; 
{
    // Connect to the shard for a customer ID. 
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command 
        SqlCommand cmd = conn.CreateCommand(); 

        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 

        Console.WriteLine("Update completed"); 
    } 
}); 
```

I pacchetti necessari per implementare la gestione degli errori temporanei vengono scaricati automaticamente quando si compila l'applicazione esempio dei database elastici. 

## <a name="transactional-consistency"></a>Coerenza delle transazioni
Le proprietà transazionali sono garantite per tutte le operazioni locali rispetto a una partizione. Ad esempio, le transazioni inviate tramite il routing dipendente dai dati vengono eseguite nell'ambito della partizione di destinazione per la connessione. Al momento, non sono disponibili funzionalità per l'inserimento di più connessioni in una transazione e pertanto non esistono garanzie transazionale per le operazioni eseguite tra partizioni.

## <a name="next-steps"></a>Passaggi successivi
Per disconnettere o riconnettere una partizione, vedere [Uso della classe RecoveryManager per correggere i problemi delle mappe partizioni](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

