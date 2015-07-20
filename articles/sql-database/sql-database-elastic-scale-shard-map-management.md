<properties 
	pageTitle="Gestione mappe partizioni" 
	description="Come utilizzare ShardMapManager, libreria client dei database elastici" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="sidneyh"/>

# Gestione mappe partizioni
Una **mappa partizioni** in un ambiente con database partizionati gestisce le informazioni permettendo a un'applicazione di connettersi al database corretto in base al valore della **chiave di partizionamento orizzontale**. La comprensione della modalità di creazione di queste mappe è essenziale per la gestione delle partizioni con la libreria client dei database elastici.

## Mappe partizioni e mapping di partizioni
 
### Tipi .NET supportati per le chiavi di partizionamento orizzontale

Scalabilità elastica supporta i seguenti tipi di .NET Framework come chiavi di partizionamento orizzontale:

* numero intero
* long
* guid
* byte  
* datetime
* timespan
* datetimeoffset

### Mappe partizioni di tipo elenco e intervallo
Le mappe partizioni possono essere create usando **elenchi di singoli valori di chiavi di partizionamento orizzontale** oppure tramite **intervalli di valori di chiavi di partizionamento orizzontale**.

###Mappe partizioni di tipo elenco
Le **partizioni** includono **shardlet** e il mapping degli shardlet alle partizioni viene gestito da una mappa partizioni. Una **mappa partizioni di tipo elenco** è un'associazione tra i singoli valori di chiave che identificano gli shardlet e i database che fungono da partizioni. I **mapping di tipo elenco** sono espliciti (ad esempio, la chiave 1 è mappata al Database A) ed è possibile mappare valori di chiave diversi allo stesso database (i valori di chiave 3 e 6 fanno riferimento entrambi al Database B). <table> <tr> <td>Chiave</td> <td>Percorso partizione</td> </tr> <tr> <td>1</td> <td>Database_A</td> </tr> <tr> <td>3</td> <td>Database_B</td> </tr> <tr> <td>4</td> <td>Database_C</td> </tr> <tr> <td>6</td> <td>Database_B</td> </tr> <tr> <td>...</td> <td>...</td> </tr> </table>

### Mappa partizioni di tipo intervallo 
In una **mappa partizioni di tipo intervallo** l'intervallo chiave è descritto da una coppia di tipo **[Low Value, High Value)**, dove *Low Value* indica la chiave minima dell'intervallo e *High Value* è il primo valore superiore all'intervallo.

Ad esempio, **[0, 100)** include tutti i numeri interi superiori o uguali a 0 e inferiori a 100. Si noti che più intervalli possono fare riferimento allo stesso database e che sono supportati intervalli non contigui. Ad esempio, [100,200) e [400,600) fanno entrambi riferimento al Database C nel seguente esempio. <table> <tr> <td><b>Intervallo di chiavi</b></td> <td><b>Percorso partizione</b></td> </tr> <tr> <td>[1, 50)</td> <td>Database_A</td> </tr> <tr> <td>[50, 100)</td> <td>Database_B</td> </tr> <tr> <td>[100, 200)</td> <td>Database_C</td> </tr> <tr> <td>[400, 600)</td> <td>Database_C</td> </tr> <tr> <td>...</td> <td>...</td> </tr> </table>

Ognuna delle tabelle precedenti è un esempio concettuale di un oggetto **ShardMap**. Ogni riga costituisce un esempio semplificato di un singolo oggetto **PointMapping** (per la mappa partizioni di tipo elenco) o **RangeMapping** (per la mappa partizioni di tipo intervallo).

## Gestore mappe partizioni 

Il gestore mappe partizioni nella libreria client è una raccolta di mappe partizioni. I dati gestiti da un oggetto .NET **ShardMapManager** sono archiviati in tre posizioni:

1. **Mappa globale partizioni**: quando si crea un oggetto **ShardMapManager**, si specifica un database da usare come repository per tutte le mappe partizioni e i mapping corrispondenti. Vengono create automaticamente tabelle speciali e stored procedure per la gestione delle informazioni. Si tratta in genere di un database di piccole dimensioni e a cui si accede raramente, ma è consigliabile non usarlo per altre esigenze dell'applicazione. Le tabelle si trovano in uno schema speciale denominato **__ShardManagement**. 
2. **Mappa locale partizioni**: ogni database specificato per l'uso come partizione in una mappa partizioni verrà modificato in modo da includere alcune tabelle di piccole dimensioni e stored procedure speciali, che includono e gestiscono informazioni sulle mappe partizioni specifiche per la partizione indicata. Queste informazioni sono ridondanti rispetto alle informazioni nella mappa globale partizioni, ma permettono all'applicazione di convalidare le informazioni sulla mappa partizioni memorizzate nella cache, senza sovraccaricare la mappa globale partizioni. L'applicazione usa la mappa locale partizioni per determinare se un mapping memorizzato nella cache è ancora valido. Le tabelle corrispondenti alla mappa locale partizioni in ogni partizione sono disponibili nello schema **__ShardManagement**.

3. **Cache dell'applicazione**: ogni istanza di applicazione che accede a un oggetto **ShardMapManager** gestisce una cache in memoria locale dei rispettivi mapping, in cui vengono archiviate le informazioni di routing appena recuperate.

## Creazione di un oggetto ShardMapManager
La creazione di istanze di un oggetto **ShardMapManager** nell'applicazione viene eseguita tramite un modello factory. Il metodo **ShardMapManagerFactory.GetSqlShardMapManager** accetta credenziali, inclusi il nome del server e il nome del database che include la mappa globale partizioni, sotto forma di oggetto **ConnectionString** e restituisce un'istanza di **ShardMapManager**.

È necessario creare istanze di **ShardMapManager** solo una volta per ogni dominio dell'applicazione, nel codice di inizializzazione per un'applicazione. Un oggetto **ShardMapManager** può includere un numero qualsiasi di mappe partizioni. Anche se è possibile che una singola mappa partizioni sia sufficiente per molte applicazioni, in alcune situazioni vengono usati diversi set di database per schemi diversi o per finalità specifiche. In questi casi è preferibile usare più mappe partizioni.

Nel seguente codice un'applicazione tenta di aprire un oggetto **ShardMapManager** esistente. Se gli oggetti che rappresentano un oggetto **ShardMapManager** globale non esistono ancora nel database, verranno creati dalla libreria client.

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

        // The connectionString contains server name, database name, and admin credentials 
        // for privileges on both the GSM and the shards themselves.
    } 
 

### Credenziali di amministrazione delle mappe partizioni

Le applicazioni che amministrano e modificano le mappe partizioni sono in genere diverse dalle applicazioni che usano le mappe partizioni per il routing delle connessioni.

Per le applicazioni che amministrano le mappe partizioni (aggiunta o modifica di partizioni, mappe partizioni, mapping di partizioni e così via), è necessario creare istanze dell'oggetto **ShardMapManager** usando **credenziali con privilegi di lettura/scrittura nel database della mappa globale partizioni e in ogni database usato come partizione**. Le credenziali devono permettere operazioni di scrittura nelle tabelle nella mappa globale partizioni e nella mappa locale partizioni durante l'immissione o la modifica delle informazioni sulle mappe partizioni, oltre che per la creazione di tabelle della mappa locale partizioni nelle nuove partizioni.

### Impatto solo sui metadati 

I metodi usati per popolare o modificare i dati di **ShardMapManager** non influiscono sui dati utente archiviati nelle partizioni stesse. Ad esempio, i metodi come **CreateShard**, **DeleteShard**, **UpdateMapping**, e così via interessano solo i metadati della mappa di partizionamento. Essi non rimuovono, aggiungono o modificano i dati utente contenuti nelle partizioni. Questi metodi sono stati invece progettati per l'uso insieme a operazioni separate eseguite per creare o rimuovere i database effettivi o per rimuovere righe da una partizione a un'altra, in modo da bilanciare nuovamente un ambiente partizionato. (Il servizio di **suddivisione-unione** incluso negli strumenti dei database elastici usa queste API, oltre a orchestrare lo spostamento effettivo dei dati tra le partizioni).

## Popolamento di una mappa partizioni: esempio
 
Di seguito è disponibile una sequenza di esempio delle operazioni necessarie per popolare una mappa partizioni specifica. Il codice esegue i seguenti passaggi:

1. Creazione di una nuova mappa partizioni in un gestore delle mappe partizioni. 
2. Aggiunta di metadati per due partizioni diverse a una mappa partizioni. 
3. Aggiunta di diversi mapping di intervalli di chiavi e visualizzazione dei contenuti complessivi della mappa partizioni. 

Il codice è scritto in modo che sia possibile eseguire di nuovo l'intero metodo in caso di errore imprevisto. Ogni richiesta verifica se esiste già una partizione o un mapping, prima di tentarne la creazione. Il seguente codice presuppone che i database denominati **sample_shard_0**, **sample_shard_1** e **sample_shard_2** siano già stati creati nel server a cui fa riferimento la stringa **shardServer**.

    public void CreatePopulatedRangeMap(ShardMapManager smm, string mapName) 
        {            
            RangeShardMap<long> sm = null; 

            // check if shardmap exists and if not, create it 
            if (!smm.TryGetRangeShardMap(mapName, out sm)) 
            { 
                sm = smm.CreateRangeShardMap<long>(mapName); 
            } 

            Shard shard0 = null, shard1=null; 
            // check if shard exists and if not, 
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_0"), out shard0)) 
            { 
                Shard0 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_0")); 
            } 

            if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_1"), out shard1)) 
            { 
                Shard1 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_1"));  
            } 

            RangeMapping<long> rmpg=null; 

            // Check if mapping exists and if not,
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetMappingForKey(0, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(0, 50), shard0, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(50, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(50, 100), shard1, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(100, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(100, 150), shard0, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(150, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(150, 200), shard1, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(200, out rmpg)) 
            { 
               sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                   (new Range<long>(200, 300), shard0, MappingStatus.Online)); 
            } 

            // List the shards and mappings 
            foreach (Shard s in sm.GetShards()
                                    .OrderBy(s => s.Location.DataSource)
                                    .ThenBy(s => s.Location.Database))
            { 
               Console.WriteLine("shard: "+ s.Location); 
            } 

            foreach (RangeMapping<long> rm in sm.GetMappings()) 
            { 
                Console.WriteLine("range: [" + rm.Value.Low.ToString() + ":" 
                        + rm.Value.High.ToString()+ ")  ==>" +rm.Shard.Location); 
            } 
        } 
 
In alternativa, è possibile usare script di PowerShell per ottenere lo stesso risultato.

Dopo il popolamento delle mappe partizioni, sarà possibile creare o modificare le applicazioni di accesso ai dati in modo che usino le mappe. Sarà necessario popolare o modificare di nuovo le **mappe solo se occorrerà modificare il layout delle mappe**.

## Routing dipendente dei dati 

Il gestore delle mappe partizioni viene usato principalmente dalle applicazioni che necessitano di connessioni a database per eseguire operazioni sui dati specifiche per le app. In un'applicazione partizionata è necessario associare queste connessioni al database di destinazione corretto. Questa procedura è nota come **routing dipendente dai dati**. Per queste applicazioni è necessario creare istanze di un oggetto gestore delle mappe partizioni dal factory, usando le credenziali con accesso di sola lettura per il database della mappa globale partizioni. Le singole richieste di connessione forniranno successivamente le credenziali necessarie per la connessione al database partizioni appropriato.

Si noti che queste applicazioni, che usano l'oggetto **ShardMapManager** aperto con credenziali di sola lettura, non potranno apportare modifiche alle mappe o ai mapping. A questo scopo è possibile creare applicazioni specifiche per l'amministrazione o script di PowerShell che forniscono credenziali con privilegi elevati, come illustrato in precedenza.

Per altri dettagli, vedere [Routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md).

## Modifica di una mappa partizioni 

È possibile modificare una mappa partizioni in molti modi diversi. Tutti i seguenti metodi modificano i metadati che descrivono le partizioni e i rispettivi mapping, ma non modificano fisicamente i dati nelle partizioni e non creano o eliminano i database effettivi. Potrebbe essere necessario coordinare alcune operazioni sulla mappa partizioni descritte di seguito con le azioni amministrative che spostano fisicamente i dati o che aggiungono e rimuovono i database che fungono da partizioni.

Questi metodi interagiscono tra loro come i blocchi predefiniti disponibili per la modifica della distribuzione complessiva dei dati nell'ambiente di database partizionati.

* Per aggiungere o rimuovere partizioni, usare **CreateShard** e **DeleteShard**. 
    
    Per permettere l'esecuzione di queste operazioni, è necessario che il server e il database che rappresentano la partizione di destinazione esistano già. Questi metodi non hanno alcun impatto sui database stessi. Influiscono solo sui metadati nella mappa partizioni.

* Per creare o rimuovere punti o intervalli mappati alle partizioni, usare **CreateRangeMapping**, **DeleteMapping** e **CreatePointMapping**.
    
    È possibile mappare molti punti o intervalli diversi alla stessa partizione. Questi metodi influiscono solo sui metadati, non su eventuali dati già presenti nelle partizioni. Se è necessario rimuovere dati dal database per assicurare la coerenza con le operazioni di tipo **DeleteMapping**, occorrerà eseguire queste operazioni separatamente, ma insieme all'uso di questi metodi.

* Per suddividere in due gli intervalli esistenti o unire intervalli adiacenti in un unico intervallo, usare **SplitMapping** e **MergeMappings**.

    Si noti che le operazioni di suddivisione e unione **non modificano la partizione a cui sono mappati i valori di chiave**. Una suddivisione divide un intervallo esistente in due parti, ma ne mantiene il mapping alla stessa partizione. Un'unione viene applicata a due intervalli adiacenti già mappati alla stessa partizione e li unisce in un singolo intervallo. Lo spostamento di punti o degli stessi intervalli tra le partizioni deve essere coordinato tramite l'uso di **UpdateMapping** insieme allo spostamento effettivo dei dati. È possibile usare il servizio di **suddivisione/unione**, incluso nello strumento dei database elastici, per coordinare le modifiche della mappa partizioni con lo spostamento dei dati, nei casi in cui lo spostamento è necessario.

* Per eseguire di nuovo il mapping o spostare singoli punti o intervalli in partizioni diverse, usare **UpdateMapping**.

    Poiché potrebbe essere necessario spostare i dati da una partizione a un'altra per mantenerne la coerenza con le operazioni di tipo **UpdateMapping**, occorrerà eseguire separatamente lo spostamento, ma insieme all'uso di questi metodi.

* Per impostare i mapping come online e offline, usare **MarkMappingOffline** e **MarkMappingOnline** per controllare lo stato online di un mapping.

    Alcune operazioni sui mapping di partizioni, incluse le operazioni **UpdateMapping** e **DeleteMapping**, sono consentite solo se lo stato del mapping è "offline". Quando un mapping è offline, una richiesta dipendente dai dati e basata su una chiave inclusa nel mapping restituirà un errore. Quando si porta offline per la prima volta un intervallo, inoltre, tutte le connessioni alla partizione interessata verranno terminate automaticamente per evitare risultati incoerenti o incompleti per le query dirette agli intervalli sottoposti a modifica.

I mapping sono oggetti non modificabili in .NET. Tutti i metodi precedenti che modificano i mapping invalidano anche tutti i riferimenti ad essi nel codice. Per semplificare l’esecuzione di sequenze di operazioni che modificano lo stato di un mapping, tutti i metodi che consentono di modificare un mapping restituiscono un nuovo riferimento di mapping, in modo che le operazioni possano essere concatenate. Ad esempio, per eliminare un mapping esistente in shardmap sm che contiene la chiave 25, è possibile eseguire le operazioni seguenti:

        sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));

## Aggiunta di una partizione 

Le applicazioni devono spesso aggiungere semplicemente nuove partizioni per gestire i dati previsti dalle nuove chiavi o dai nuovi intervalli di chiavi, per una mappa partizioni già esistente. Ad esempio, è possibile che un'applicazione partizionata dall'ID tenant debba eseguire il provisioning di una nuova partizione per un nuovo tenant, oppure è possibile che i dati partizionati ogni mese richiedano il provisioning di una nuova partizione prima dell'inizio di ogni nuovo mese.

Se il nuovo intervallo di valori di chiave non è già incluso in un mapping esistente e non è necessario alcuno spostamento di dati, l'aggiunta della nuova partizione e l'associazione della nuova chiave o dell'intervallo a quella partizione risulteranno molto semplici. Per informazioni dettagliate sull'aggiunta di nuove partizioni, vedere [Aggiunta di una nuova partizione](sql-database-elastic-scale-add-a-shard.md).

Per gli scenari che richiedono lo spostamento di dati, tuttavia, il servizio di suddivisione-unione è necessario per l'orchestrazione dello spostamento di dati tra le partizioni insieme agli aggiornamenti necessari per la mappa partizioni. Per informazioni dettagliate sull'uso del servizio di suddivisione-unione, vedere la [Panoramica del servizio di suddivisione-unione](sql-database-elastic-scale-overview-split-and-merge.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=July15_HO2-->