---
title: Aumentare il numero di istanze di un database SQL di Azure | Documentazione Microsoft
description: Come utilizzare ShardMapManager, libreria client dei database elastici
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 03/16/2018
ms.author: sstein
ms.openlocfilehash: cf8d4427cddbe6368ac265fe9ecc0f408f7fb1fb
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Aumentare il numero di istanze dei database con il gestore delle mappe partizioni
Per aumentare facilmente il numero di istanze dei database in SQL Azure, usare un gestore delle mappe partizioni. Il gestore delle mappe partizioni è un database speciale che gestisce le informazioni di mapping globale su tutte le partizioni (database) in un set di partizioni. I metadati consentono all'applicazione di connettersi al database corretto in base al valore della **chiave di partizionamento orizzontale**. Inoltre, ogni partizione nel set contiene le mappe che tengono traccia dei dati delle partizioni locali (i cosiddetti **shardlet**). 

![Gestione mappe partizioni](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Per gestire le mappe partizioni, è fondamentale comprenderne il processo di creazione. A questo scopo si usa la classe ShardMapManager ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager), disponibile nella [libreria client dei database elastici](sql-database-elastic-database-client-library.md).  

## <a name="shard-maps-and-shard-mappings"></a>Mappe partizioni e mapping di partizioni
Per ogni partizione, è necessario selezionare il tipo di mappa partizioni da creare. La scelta dipende dall'architettura del database: 

1. Singolo tenant per database  
2. Più tenant per database (due tipi):
   1. Mapping di tipo elenco
   2. Mapping di tipo intervallo

Per un modello a singolo tenant, creare una mappa partizioni con **mapping di tipo elenco** . Il modello single-tenant assegna un database per tenant. Si tratta di un modello efficace per gli sviluppatori SaaS in quanto semplifica la gestione.

![Mapping di tipo elenco][1]

Il modello multi-tenant assegna diversi tenant a un database singolo ed è possibile distribuire gruppi di tenant tra più database. Usare questo modello quando si prevedono esigenze di dati ridotte per ogni tenant. In questo modello viene assegnato un intervallo di tenant a un database usando il **mapping di tipo intervallo**. 

![Mapping di tipo intervallo][2]

In alternativa è possibile implementare un modello di database multi-tenant usando il *mapping di tipo elenco* per assegnare più tenant a un database singolo. Ad esempio, DB1 viene usato per archiviare le informazioni sugli ID tenant 1 e 5 e DB2 archivia i dati per i tenant 7 e 10. 

![Tenant multipli su database singolo][3] 

### <a name="supported-types-for-sharding-keys"></a>Tipi supportati per le chiavi di partizionamento orizzontale
La scalabilità elastica supporta i tipi seguenti come chiavi di partizionamento orizzontale:

| .NET | Java |
| --- | --- |
| numero intero |numero intero |
| long |long |
| GUID |uuid |
| byte[]  |byte[] |
| Datetime | timestamp |
| Intervallo di tempo | duration|
| datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Mappe partizioni di tipo elenco e intervallo
Le mappe partizioni possono essere create usando **elenchi di singoli valori di chiavi di partizionamento orizzontale** oppure tramite **intervalli di valori di chiavi di partizionamento orizzontale**. 

### <a name="list-shard-maps"></a>Mappe partizioni di tipo elenco
Le **partizioni** includono **shardlet** e il mapping degli shardlet alle partizioni viene gestito da una mappa partizioni. Una **mappa partizioni di tipo elenco** è un'associazione tra i singoli valori di chiave che identificano gli shardlet e i database che fungono da partizioni.  I **mapping di tipo elenco** sono espliciti. È possibile eseguire il mapping di valori di chiave diversi allo stesso database. Ad esempio, la chiave 1 è mappata al database A e i valori di chiave 3 e 6 fanno entrambi riferimento al database B.

| Chiave | Percorso della partizione |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Mappa partizioni di tipo intervallo
In una **mappa partizioni di tipo intervallo** l'intervallo chiave è descritto da una coppia di tipo **[Low Value, High Value)**, dove *Low Value* indica la chiave minima dell'intervallo e *High Value* è il primo valore superiore all'intervallo. 

Ad esempio, **[0, 100)** include tutti i numeri interi superiori o uguali a 0 e inferiori a 100. Si noti che più intervalli possono fare riferimento allo stesso database e che sono supportati intervalli non contigui. Ad esempio, [100,200) e [400,600) fanno entrambi riferimento al Database C nell'esempio seguente.

| Chiave | Percorso della partizione |
| --- | --- |
| [1, 50) |Database_A |
| [50, 100) |Database_B |
| [100, 200) |Database_C |
| [400, 600) |Database_C |
| ... |... |

Ognuna delle tabelle precedenti è un esempio concettuale di un oggetto **ShardMap** . Ogni riga costituisce un esempio semplificato di un singolo oggetto **PointMapping** (per la mappa partizioni di tipo elenco) o **RangeMapping** (per la mappa partizioni di tipo intervallo).

## <a name="shard-map-manager"></a>Gestore mappe partizioni
Il gestore mappe partizioni nella libreria client è una raccolta di mappe partizioni. I dati gestiti da un'istanza di **ShardMapManager** sono conservati in tre posizioni: 

1. **Mappa globale partizioni**: si specifica un database da usare come repository per tutte le mappe partizioni e i mapping corrispondenti. Vengono create automaticamente tabelle speciali e stored procedure per la gestione delle informazioni. Si tratta in genere di un database di piccole dimensioni e a cui si accede raramente ed è consigliabile non usarlo per altre esigenze dell'applicazione. Le tabelle si trovano in uno schema speciale denominato **__ShardManagement**. 
2. **Mappa locale partizioni**: ogni database specificato per l'uso come partizione viene modificato in modo da includere varie tabelle di piccole dimensioni e stored procedure speciali, che includono e gestiscono informazioni sulle mappe partizioni specifiche per la partizione. Queste informazioni sono ridondanti rispetto alle informazioni nella mappa globale partizioni e permettono all'applicazione di convalidare le informazioni sulla mappa partizioni memorizzate nella cache, senza sovraccaricare la mappa globale partizioni. L'applicazione usa la mappa locale partizioni per determinare se un mapping memorizzato nella cache è ancora valido. Le tabelle corrispondenti alla mappa locale partizioni in ogni partizione sono disponibili anche nello schema **__ShardManagement**.
3. **Cache dell'applicazione**: ogni istanza di applicazione che accede a un oggetto **ShardMapManager** gestisce una cache in memoria locale dei rispettivi mapping, in cui vengono archiviate le informazioni di routing appena recuperate. 

## <a name="constructing-a-shardmapmanager"></a>Creazione di un oggetto ShardMapManager
Un oggetto **ShardMapManager** viene creato con un criterio factory ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)). Il metodo **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) accetta credenziali, inclusi il nome del server e il nome del database contenente la mappa globale partizioni, sotto forma di oggetto **ConnectionString**, e restituisce un'istanza di un oggetto **ShardMapManager**.  

**Nota:** è sufficiente creare una sola istanza di **ShardMapManager** per ogni dominio app, nel codice di inizializzazione dell'applicazione. La creazione di istanze aggiuntive di ShardMapManager nello stesso dominio comporta un aumento dell'utilizzo della memoria e della CPU dell'applicazione. Un oggetto **ShardMapManager** può includere un numero qualsiasi di mappe partizioni. Anche se è possibile che una singola mappa partizioni sia sufficiente per molte applicazioni, in alcune situazioni vengono usati diversi set di database per schemi diversi o per finalità specifiche. In questi casi è preferibile usare più mappe partizioni. 

Nel codice seguente un'applicazione prova ad aprire un oggetto **ShardMapManager** esistente con il metodo TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager). Se gli oggetti che rappresentano un oggetto **ShardMapManager** globale non esistono ancora nel database, vengono creati dalla libreria client con il metodo CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.createsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)).

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it. 
ShardMapManager shardMapManager; 
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString, 
                                        ShardMapManagerLoadPolicy.Lazy, 
                                        out shardMapManager); 

if (shardMapManagerExists) 
{ 
    Console.WriteLine("Shard Map Manager already exists");
} 
else
{
    // Create the Shard Map Manager. 
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager"); 

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString, 
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
} 
```

Per la versione .NET, è possibile usare PowerShell per creare un nuovo gestore delle mappe partizioni. Un esempio è disponibile [qui](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Ottenere una mappa RangeShardMap o ListShardMap
Dopo la creazione di un gestore di mappe partizioni, è possibile ottenere RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) o ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807370.aspx)) con il metodo TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)) o GetShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)).

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
} 
```

### <a name="shard-map-administration-credentials"></a>Credenziali di amministrazione delle mappe partizioni
Le applicazioni che amministrano e modificano le mappe partizioni sono diverse da quelle che usano le mappe partizioni per il routing delle connessioni. 

Per amministrare le mappe partizioni (aggiunta o modifica di partizioni, mappe partizioni, mapping di partizioni e così via), è necessario creare istanze dell'oggetto **ShardMapManager** usando **credenziali con privilegi di lettura/scrittura per il database della mappa globale partizioni e per ogni database usato come partizione**. Le credenziali devono permettere operazioni di scrittura nelle tabelle nella mappa globale partizioni e nella mappa locale partizioni durante l'immissione o la modifica delle informazioni sulle mappe partizioni, oltre che per la creazione di tabelle della mappa locale partizioni nelle nuove partizioni.  

Vedere [Credenziali usate per accedere alla libreria client dei database elastici](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Impatto solo sui metadati
I metodi usati per popolare o modificare i dati di **ShardMapManager** non influiscono sui dati utente archiviati nelle partizioni stesse. Ad esempio, i metodi come **CreateShard**, **DeleteShard**, **UpdateMapping** e così via interessano solo i metadati della mappa partizioni. Essi non rimuovono, aggiungono o modificano i dati utente contenuti nelle partizioni. Questi metodi sono stati invece progettati per l'uso insieme a operazioni separate eseguite per creare o rimuovere i database effettivi o per rimuovere righe da una partizione a un'altra, in modo da bilanciare nuovamente un ambiente partizionato  (il servizio di **suddivisione-unione** incluso negli strumenti dei database elastici usa queste API, oltre a orchestrare lo spostamento effettivo dei dati tra le partizioni). Vedere [Scalabilità tramite lo strumento di suddivisione-unione del database elastico](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Routing dipendente dei dati
Lo strumento di gestione delle mappe partizioni viene usato dalle applicazioni che richiedono connessioni a database per eseguire operazioni specifiche sui dati. Tali connessioni devono essere associate al database corretto. Questa procedura è nota come **routing dipendente dai dati**. Per queste applicazioni è necessario creare istanze di un oggetto gestore delle mappe partizioni dal factory, usando le credenziali con accesso di sola lettura per il database della mappa globale partizioni. Le singole richieste per connessioni successive forniscono le credenziali necessarie per la connessione al database partizioni appropriato.

Si noti che queste applicazioni, che usano l'oggetto **ShardMapManager** aperto con credenziali di sola lettura, non possono apportare modifiche alle mappe o ai mapping. A questo scopo è possibile creare applicazioni specifiche per l'amministrazione o script di PowerShell che forniscono credenziali con privilegi elevati, come illustrato in precedenza. Vedere [Credenziali usate per accedere alla libreria client dei database elastici](sql-database-elastic-scale-manage-credentials.md).

Per altre informazioni, vedere [Routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md). 

## <a name="modifying-a-shard-map"></a>Modifica di una mappa partizioni
È possibile modificare una mappa partizioni in molti modi diversi. Tutti i seguenti metodi modificano i metadati che descrivono le partizioni e i rispettivi mapping, ma non modificano fisicamente i dati nelle partizioni e non creano o eliminano i database effettivi.  Potrebbe essere necessario coordinare alcune operazioni sulla mappa partizioni descritte di seguito con le azioni amministrative che spostano fisicamente i dati o che aggiungono e rimuovono i database che fungono da partizioni.

Questi metodi interagiscono tra loro come i blocchi predefiniti disponibili per la modifica della distribuzione complessiva dei dati nell'ambiente di database partizionati.  

* Per aggiungere o rimuovere partizioni: usare **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.createshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)) e **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.deleteshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)) della classe Shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx)). 
  
    Per permettere l'esecuzione di queste operazioni, è necessario che il server e il database che rappresentano la partizione di destinazione esistano già. Questi metodi non hanno alcun impatto sui database stessi. Influiscono solo sui metadati nella mappa partizioni.
* Per creare o rimuovere punti o intervalli mappati alle partizioni: usare **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.createrangemapping), [.NET](https://msdn.microsoft.com/library/azure/dn841993.aspx)) e **DeleteMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.deletemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824200.aspx)) della classe RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) e **CreatePointMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map.createpointmapping), [.NET](https://msdn.microsoft.com/library/azure/dn807218.aspx)) della classe ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn842123.aspx)).
  
    È possibile mappare molti punti o intervalli diversi alla stessa partizione. Questi metodi influiscono solo sui metadati, non sui dati eventualmente già presenti nelle partizioni. Se è necessario rimuovere dati dal database per assicurare la coerenza con le operazioni di tipo **DeleteMapping**, eseguire queste operazioni separatamente, ma contemporaneamente all'uso di questi metodi.  
* Per dividere in due gli intervalli esistenti o unire intervalli adiacenti in un unico intervallo: usare **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) e **MergeMappings** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Si noti che le operazioni di suddivisione e unione **non modificano la partizione a cui sono mappati i valori di chiave**. Una suddivisione divide un intervallo esistente in due parti, ma ne mantiene il mapping alla stessa partizione. Un'unione viene applicata a due intervalli adiacenti già mappati alla stessa partizione e li unisce in un singolo intervallo.  Lo spostamento di punti o degli stessi intervalli tra le partizioni deve essere coordinato tramite l'uso di **UpdateMapping** insieme allo spostamento effettivo dei dati.  È possibile usare il servizio di **suddivisione/unione** , incluso nello strumento dei database elastici, per coordinare le modifiche della mappa partizioni con lo spostamento dei dati, nei casi in cui lo spostamento è necessario. 
* Per eseguire di nuovo il mapping di singoli punti o intervalli o spostare punti o intervalli in partizioni diverse usare **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.updatemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824207.aspx)).  
  
    Poiché potrebbe essere necessario spostare i dati da una partizione a un'altra per mantenere la coerenza con le operazioni di tipo **UpdateMapping**, occorre eseguire separatamente lo spostamento, ma contemporaneamente all'uso di questi metodi.
* Per impostare i mapping come online e offline: usare **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingoffline), [.NET](https://msdn.microsoft.com/library/azure/dn824202.aspx)) e **MarkMappingOnline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingonline), [.NET](https://msdn.microsoft.com/library/azure/dn807225.aspx)) per controllare lo stato online di un mapping. 
  
    Alcune operazioni sui mapping di partizioni, incluse le operazioni **UpdateMapping** e **DeleteMapping**, sono consentite solo se lo stato del mapping è "offline". Quando un mapping è offline, una richiesta dipendente dai dati e basata su una chiave inclusa nel mapping restituisce un errore. Quando si porta offline per la prima volta un intervallo, inoltre, tutte le connessioni alla partizione interessata verranno terminate automaticamente per evitare risultati incoerenti o incompleti per le query dirette agli intervalli sottoposti a modifica. 

I mapping sono oggetti non modificabili in .NET.  Tutti i metodi precedenti che modificano i mapping invalidano anche tutti i riferimenti ad essi nel codice. Per semplificare l’esecuzione di sequenze di operazioni che modificano lo stato di un mapping, tutti i metodi che consentono di modificare un mapping restituiscono un nuovo riferimento di mapping, in modo che le operazioni possano essere concatenate. Ad esempio, per eliminare un mapping esistente in shardmap sm che contiene la chiave 25, è possibile eseguire le operazioni seguenti: 

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Aggiunta di una partizione
Per una mappa partizioni già esistente, le applicazioni devono spesso aggiungere nuove partizioni per gestire i dati previsti dalle nuove chiavi o dai nuovi intervalli di chiavi. Ad esempio, è possibile che un'applicazione partizionata dall'ID tenant debba eseguire il provisioning di una nuova partizione per un nuovo tenant, oppure è possibile che i dati partizionati ogni mese richiedano il provisioning di una nuova partizione prima dell'inizio di ogni nuovo mese. 

Se il nuovo intervallo di valori di chiave non è ancora incluso in un mapping esistente e non è necessario alcuno spostamento di dati, l'aggiunta della nuova partizione e l'associazione della nuova chiave o del nuovo intervallo a tale partizione sono semplici. Per informazioni dettagliate sull'aggiunta di nuove partizioni, vedere [Aggiunta di una nuova partizione](sql-database-elastic-scale-add-a-shard.md).

Per gli scenari che richiedono lo spostamento di dati, tuttavia, il servizio di suddivisione-unione è necessario per l'orchestrazione dello spostamento di dati tra le partizioni insieme agli aggiornamenti necessari per la mappa partizioni. Per informazioni dettagliate sull'uso dello strumento di suddivisione-unione, vedere [Panoramica dello strumento di suddivisione-unione](sql-database-elastic-scale-overview-split-and-merge.md) 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
