---
title: "Partizionamento e scalabilità in Azure DocumentDB | Documentazione Microsoft"
description: Informazioni su come funziona il partizionamento in Azure DocumentDB, su come configurare il partizionamento e le chiavi di partizione e su come scegliere la chiave di partizione corretta per l&quot;applicazione.
services: documentdb
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 702c39b4-1798-48dd-9993-4493a2f6df9e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 67d817c04672979ec8af8a540c5a63eb4df9bf6a
ms.lasthandoff: 03/15/2017


---
# <a name="partitioning-partition-keys-and-scaling-in-documentdb"></a>Partizionamento, chiavi di partizione e scalabilità in DocumentDB

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) è progettato per ottenere prestazioni rapide e prevedibili e per eseguire facilmente la scalabilità in base allo sviluppo dell'applicazione. Questo articolo offre una panoramica del funzionamento del partizionamento in DocumentDB e descrive come configurare raccolte di DocumentDB per scalare in modo efficace le applicazioni.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:   

* Come funziona il partizionamento in Azure DocumentDB?
* Come si configura il partizionamento in DocumentDB?
* Cosa sono le chiavi di partizione e come scegliere la chiave di partizione corretta per l'applicazione?

Per iniziare a usare il codice, scaricare il progetto dall'esempio relativo al [driver del test delle prestazioni di DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

Il partizionamento e le chiavi di partizione sono illustrati anche in questo video di Azure Friday con Scott Hanselman e Shireesh Thota, responsabile principale della progettazione di DocumentDB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-documentdb"></a>Partizionamento in DocumentDB
In DocumentDB è possibile archiviare ed eseguire query di documenti JSON senza schema con tempi di risposta dell'ordine di millisecondi su qualsiasi scala. DocumentDB offre contenitori per l'archiviazione di dati denominati **raccolte**. Le raccolte sono risorse logiche e possono comprendere una o più partizioni fisiche o server. Il numero di partizioni è determinato da DocumentDB in base allo spazio di archiviazione e alla velocità effettiva con provisioning della raccolta. Ogni partizione in DocumentDB dispone di una quantità fissa di archiviazione supportata da unità SSD associata a essa e viene replicata per la disponibilità elevata. Le partizioni vengono completamente gestite da Azure DocumentDB e non è necessario scrivere script di codice complessi o gestire le partizioni. Le raccolte di DocumentDB sono **praticamente illimitate** in termini di spazio di archiviazione e di velocità effettiva. 

Il partizionamento è completamente trasparente per l'applicazione. DocumentDB supporta letture e scritture veloci, query SQL e LINQ, una logica transazionale basata su JavaScript, livelli di coerenza e il controllo di accesso con granularità fine tramite chiamate API REST a una singola risorsa della raccolta. Il servizio gestisce la distribuzione dei dati tra le partizioni e il routing delle richieste di query alla partizione corretta. 

Come funziona? Quando viene creata una raccolta in DocumentDB, è possibile specificare il valore di configurazione della **proprietà chiave di partizione**. Questa è la proprietà (o il percorso) JSON all'interno dei documenti che possono essere usati da DocumentDB per distribuire i dati tra più server o più partizioni. DocumentDB eseguirà l'hashing del valore della chiave di partizione e userà il risultato con hash per determinare la partizione in cui verrà archiviato il documento JSON. Tutti i documenti con la stessa chiave di partizione verranno archiviati nella stessa partizione. 

Ad esempio, si consideri un'applicazione che archivia i dati sui dipendenti e i relativi reparti in DocumentDB. Scegliere `"department"` come proprietà chiave di partizione per scalare orizzontalmente i dati in base al reparto. Ogni documento in DocumentDB deve contenere una proprietà `"id"` obbligatoria che deve essere univoca per ogni documento con lo stesso valore della chiave di partizione, ad esempio `"Marketing`". Ogni documento archiviato in una raccolta deve avere una combinazione univoca di chiave di partizione e ID, ad esempio `{ "Department": "Marketing", "id": "0001" }`, `{ "Department": "Marketing", "id": "0002" }` e `{ "Department": "Sales", "id": "0001" }`. In altre parole, la proprietà composta (chiave di partizione, id) è la chiave primaria per la raccolta.

DocumentDB creare un numero ridotto di partizioni fisiche dietro a ogni raccolta in base alle dimensioni di archiviazione e alla velocità effettiva con provisioning. La proprietà definita come chiave di partizione è una partizione logica. Più valori di chiave di partizione condividono in genere una singola partizione fisica, ma un singolo valore non può espandersi a una partizione. È bene disporre di una chiave di partizione con molti valori, perché ciò consente a DocumentDB di eseguire un miglior bilanciamento del carico in caso di aumento dei dati o della velocità effettiva con provisioning.

Si supponga, ad esempio, di creare una raccolta con 25.000 richieste per secondo di velocità effettiva e che DocumentDB supporta 10.000 richieste al secondo per ogni singola partizione fisica. Per la raccolta, DocumentDB crea le 3 partizioni fisiche P1, P2 e P3. Durante l'inserimento o la lettura di un documento, il servizio DocumentDB genera il valore hash corrispondente `Department` per il mapping dei dati delle tre partizioni P1, P2 e P3. Pertanto, se l'hash di "Marketing" e "Sales" corrisponde a 1, entrambi vengono memorizzati in P1. Quando la partizione P1 è piena, DocumentDB la suddivide nelle due nuove partizioni P4 e P5. Dopo la divisione, il servizio potrebbe quindi spostare "Marketing" in P4 e "Sales" in P5, quindi eliminare P1. Questi spostamenti delle chiavi di partizione tra partizioni sono trasparenti all'applicazione e non hanno alcun impatto sulla disponibilità della raccolta.

## <a name="sharding-in-api-for-mongodb"></a>Partizionamento orizzontale nell'API per MongoDB
Le raccolte con partizionamento orizzontale nell'API per MongoDB usano la stessa infrastruttura delle raccolte partizionate di DocumentDB. Analogamente alle raccolte partizionate, le raccolte con partizionamento orizzontale possono includere un numero qualsiasi di partizioni e a ogni partizione è associata una quantità fissa di risorse di archiviazione basate su SSD. Le raccolte con partizionamento orizzontale sono praticamente illimitate in termini di risorse di archiviazione e di velocità effettiva. La chiave di partizione dell'API per MongoDB equivale alla chiave di partizione di DocumentDB. Quando si definisce una chiave di partizione, assicurarsi di leggere le sezioni [Chiavi di partizione](#partition-keys) e [Progettazione per il partizionamento](#designing-for-partitioning).

<a name="partition-keys"></a>
## <a name="partition-keys"></a>Chiavi di partizione
La scelta della chiave di partizione è una decisione importante da prendere in fase di progettazione. È necessario scegliere un nome della proprietà JSON che contiene un'ampia gamma di valori e probabilmente modelli di accesso distribuiti in modo uniforme. 

> [!NOTE]
> È bene disporre di una chiave di partizione con un numero elevato di valori distinti (100-1000 come minimo). Molti clienti usano DocumentDB come archivio di valori di chiavi, dove l'"id" univoco è la chiave di partizione per un numero elevato di chiavi di partizioni, da milioni sino a miliardi.
>

La tabella seguente mostra esempi di definizioni della chiave di partizione e i valori JSON corrispondenti a ciascuna definizione. La chiave di partizione viene specificata come percorso JSON, ad esempio `/department` rappresenta la proprietà reparto. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Percorso della chiave di partizione</strong></p></td>
            <td valign="top"><p><strong>Descrizione</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>Corrisponde al valore JSON doc.department in cui doc è il documento.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>Corrisponde al valore JSON doc.properties.name in cui doc è il documento (proprietà annidata).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>Corrisponde al valore JSON doc.id (ID e la chiave di partizione corrispondono alla stessa proprietà).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"nome reparto"</p></td>
            <td valign="top"><p>Corrisponde al valore JSON doc["nome reparto"] in cui doc è il documento.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> La sintassi del percorso della chiave di partizione è simile alla specifica dei percorsi dei criteri di indicizzazione con la differenza fondamentale che il percorso corrisponde alla proprietà anziché al valore, ovvero non ci sono caratteri jolly alla fine. Ad esempio, per indicizzare i valori nel reparto si specifica /department/?, mentre per la definizione della chiave di partizione si usa /department. Il percorso della chiave di partizione viene indicizzato in modo implicito e non può essere escluso dall'indicizzazione usando gli override dei criteri di indicizzazione.
> 
> 

Di seguito viene esaminato come la scelta della chiave di partizione influisce sulle prestazioni dell'applicazione.

## <a name="partitioning-and-provisioned-throughput"></a>Partizionamento e velocità effettiva con provisioning
DocumentDB è progettato per prestazioni prevedibili. Quando si crea una raccolta, la velocità effettiva viene riservata in termini di **[unità richiesta](documentdb-request-units.md) (UR) al secondo**. A ogni richiesta viene assegnato un addebito delle unità richiesta proporzionato alla quantità di risorse di sistema, come CPU e I/O usati dall'operazione. La lettura di un documento di 1 KB con coerenza di sessione usa 1 unità richiesta. Un'operazione di lettura corrisponde a 1 RU indipendentemente dal numero di elementi archiviati o dal numero di richieste simultanee in esecuzione contemporaneamente. Documenti di dimensioni maggiori richiedono più unità richiesta a seconda delle dimensioni. Se si conoscono le dimensioni delle entità e il numero di letture che è necessario supportare per l'applicazione, è possibile eseguire il provisioning della quantità esatta di velocità effettiva necessaria per le esigenze di lettura dell'applicazione. 

Quando DocumentDB archivia i documenti, li distribuisce in modo uniforme tra le partizioni in base al valore della chiave di partizione. Anche la velocità effettiva viene distribuita in modo uniforme tra le partizioni disponibili, ad esempio la velocità effettiva per ogni partizione = (velocità effettiva totale per ogni raccolta)/(numero di partizioni). 

> [!NOTE]
> Per ottenere la velocità effettiva totale della raccolta, è necessario scegliere una chiave di partizione che consenta di distribuire in modo uniforme le richieste tra una serie di valori distinti della chiave di partizione.
> 
> 

## <a name="single-partition-and-partitioned-collections"></a>Raccolte a partizione singola e raccolte partizionate
DocumentDB supporta la creazione di raccolte a partizione singola e raccolte partizionate. 

* Le **raccolte partizionate** possono comprendere più partizioni e supportare risorse di archiviazione e velocità effettiva senza limiti. È necessario specificare una chiave di partizione per la raccolta. 
* Le **raccolte a partizione singola** offrono opzioni a prezzo più basso, ma sono limitate a livello di risorse di archiviazione e velocità effettiva massime e minime. Non è necessario specificare una chiave di partizione per queste raccolte. È consigliabile usare le raccolte partizionate rispetto alle raccolte a partizione singola per tutti gli scenari, ad eccezione delle situazioni in cui è prevista solo una quantità ridotta di risorse di archiviazione di dati e di richieste.

![Raccolte partizionate in DocumentDB][2] 

Le raccolte partizionate possono supportare risorse di archiviazione e velocità effettiva senza limiti.

La tabella seguente elenca le differenze nell'uso di raccolte a partizione singola e raccolte partizionate:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Raccolta a partizione singola</strong></p></td>
            <td valign="top"><p><strong>Raccolta partizionata</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chiave di partizione</p></td>
            <td valign="top"><p>None</p></td>
            <td valign="top"><p>Obbligatorio</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chiave primaria per documento</p></td>
            <td valign="top"><p>"id"</p></td>
            <td valign="top"><p>chiave composta &lt;chiave di partizione&gt; e "id"</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Archiviazione minima</p></td>
            <td valign="top"><p>0 GB</p></td>
            <td valign="top"><p>0 GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Archiviazione massima</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>Senza limiti</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Velocità effettiva minima</p></td>
            <td valign="top"><p>400 unità richiesta al secondo</p></td>
            <td valign="top"><p>2.500 unità richiesta al secondo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Velocità effettiva massima</p></td>
            <td valign="top"><p>10.000 unità richiesta al secondo</p></td>
            <td valign="top"><p>Senza limiti</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Versioni dell'API</p></td>
            <td valign="top"><p>Tutti</p></td>
            <td valign="top"><p>API 2015-12-16 e versioni successive</p></td>
        </tr>
    </tbody>
</table>

## <a name="working-with-the-documentdb-sdks"></a>Uso di DocumentDB SDK
Azure DocumentDB ha aggiunto il supporto per il partizionamento automatico con la [versione 2015-12-16 dell'API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx). Per creare raccolte partizionate, è necessario scaricare la versione 1.6.0 dell'SDK o la versione successiva da una delle piattaforme SDK supportate (.NET, Node.js, Java, Python). 

### <a name="creating-partitioned-collections"></a>Creazione di raccolte partizionate
L'esempio seguente illustra un frammento di codice .NET per creare una raccolta per l'archiviazione di dati di telemetria del dispositivo a una velocità effettiva di 20.000 unità richiesta al secondo. L'SDK imposta il valore OfferThroughput, che a sua volta imposta l'intestazione di richiesta `x-ms-offer-throughput` nell'API REST. Qui viene impostato `/deviceId` come chiave di partizione. La scelta della chiave di partizione viene salvata insieme al resto dei metadati della raccolta, come il nome e il criterio di indicizzazione.

Per questo esempio è stato scelto `deviceId` per due motivi: (a) considerato il numero elevato di dispositivi, le scritture possono essere distribuite in modo uniforme tra le partizioni consentendo di scalare il database per l'inserimento di volumi elevati di dati e (b) molte richieste, ad esempio il recupero della lettura più recente per un dispositivo, sono limitate a un singolo deviceId e possono essere recuperate da una partizione singola.

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
// spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
// sorting against any number or string property.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

> [!NOTE]
> Per creare raccolte partizionate utilizzando l'SDK, è necessario specificare un valore per la velocità effettiva maggiore o uguale a 10.100 UR/s. Per impostare un valore di velocità effettiva tra 2.500 e 10.000 per le raccolte partizionate, è necessario utilizzare temporaneamente il portale di Azure, poiché i nuovi valori inferiori non sono ancora disponibili nell'SDK.
> 
> 

Questo metodo effettua una chiamata API REST a DocumentDB e il servizio eseguirà il provisioning di una serie di partizioni in base alla velocità effettiva richiesta. È possibile modificare la velocità effettiva di una raccolta se le esigenze in termini di prestazioni cambiano. 

### <a name="reading-and-writing-documents"></a>Lettura e scrittura di documenti
A questo punto si inseriscono i dati in DocumentDB. Di seguito è riportata una classe di esempio contenente un dispositivo di lettura e una chiamata a CreateDocumentAsync per inserire un nuovo dispositivo di lettura in una raccolta.

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```

Si procede alla lettura del documento, all'aggiornamento e infine all'eliminazione in base alla chiave di partizione e all'id. Si noti che le letture includono un valore PartitionKey, corrispondente all'intestazione di richiesta `x-ms-documentdb-partitionkey` nell'API REST.

```csharp
// Read document. Needs the partition key and the ID to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;

// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);

// Delete document. Needs partition key
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="querying-partitioned-collections"></a>Esecuzione di query sulle raccolte partizionate
Quando viene eseguita una query sui dati delle raccolte partizionate, DocumentDB instrada automaticamente la query alle partizioni corrispondenti ai valori della chiave di partizione specificati nel filtro (se presenti). Ad esempio, questa query viene instradata solo alla partizione contenente la chiave di partizione "XMS-0001".

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
La query seguente non dispone di un filtro per la chiave di partizione (DeviceId) e viene effettuato il fan-out a tutte le partizioni in cui viene eseguita a fronte dell'indice della partizione. Si noti che è necessario specificare EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` nell'API REST) affinché l'SDK esegua una query tra le partizioni.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

DocumentDB supporta le [funzioni di aggregazione](documentdb-sql-query.md#Aggregates) `COUNT`, `MIN`, `MAX`, `SUM` e `AVG` su raccolte partizionate usando SQL a partire da SDK 1.12.0 e versioni successive. Le query devono includere un unico operatore di aggregazione e un singolo valore nella proiezione.

### <a name="parallel-query-execution"></a>Esecuzione di query in parallelo
Gli SDK di DocumentDB 1.9.0 e versioni successive supportano le opzioni di esecuzione di query in parallelo, che consentono di eseguire query a bassa latenza sulle raccolte partizionate, anche quando è necessario toccare un numero elevato di partizioni. Ad esempio, la query seguente è configurata in modo da essere eseguita in parallelo tra le partizioni.

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
È possibile gestire l'esecuzione di query in parallelo, ottimizzando i parametri seguenti:

* Impostando `MaxDegreeOfParallelism`è possibile controllare il grado di parallelismo, ovvero il numero massimo di connessioni di rete simultanee alle partizioni della raccolta. Se si imposta questo valore su -1, il grado di parallelismo viene gestito dall'SDK. Se `MaxDegreeOfParallelism` non è specificato o è impostato su 0, ovvero il valore predefinito, esisterà una sola connessione di rete per le partizioni della raccolta.
* Impostando `MaxBufferedItemCount` è possibile raggiungere un compromesso tra latenza della query e uso della memoria dal lato client. Se si omette questo parametro o si imposta su -1, il numero di elementi memorizzati nel buffer durante l'esecuzione di query in parallelo viene gestito dall'SDK.

Considerato lo stato della raccolta, una query in parallelo restituirà i risultati nello stesso ordine dell'esecuzione seriale. Quando si esegue una query tra partizioni che include l'ordinamento (ORDER BY e/o TOP), l'SDK di DocumentDB esegue la query in parallelo tra le partizioni e unisce i risultati ordinati parzialmente sul lato client per produrre risultati ordinati a livello globale.

### <a name="executing-stored-procedures"></a>Esecuzione di stored procedure
È anche possibile eseguire transazioni atomiche rispetto a documenti con lo stesso ID dispositivo, ad esempio se si gestiscono aggregazioni o lo stato più recente di un dispositivo in un unico documento. 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
    
Nella sezione successiva verrà illustrato come passare alle raccolte partizionate da raccolte a partizione singola.

## <a name="creating-an-api-for-mongodb-sharded-collection"></a>Creazione di una raccolta con partizionamento orizzontale per l'API per MongoDB
Il modo più semplice per creare una raccolta con partizionamento orizzontale per l'API per MongoDB consiste nell'usare il proprio strumento, driver o SDK preferito. In questo esempio verrà usato Mongo Shell per la creazione della raccolta.

In Mongo Shell:

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Risultati:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

<a name="migrating-from-single-partition"></a>

## <a name="migrating-from-single-partition-to-partitioned-collections-in-documentdb"></a>Migrazione da raccolte a partizione singola a raccolte partizionate in DocumentDB

> [!IMPORTANT]
> Se si importano dati nell'API per MongoDB, seguire queste [istruzioni](documentdb-mongodb-migrate.md).
> 
> 

Quando un'applicazione che usa una raccolta a partizione singola necessita di una velocità effettiva più alta (>&10;.000 UR/secondo) o di uno spazio di archiviazione dati maggiore (>&10; GB), è possibile usare lo [strumento di migrazione dati di DocumentDB](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) per eseguire la migrazione dei dati dalla raccolta a partizione singola a una raccolta partizionata. 

Per eseguire la migrazione da una raccolta a partizione singola a una raccolta partizionata

1. Esportare i dati da una raccolta a partizione singola a JSON. Vedere [Esportare in file JSON](documentdb-import-data.md#export-to-json-file) per altre informazioni.
2. Importare i dati in una raccolta partizionata creata con una definizione della chiave di partizione e con più di 2.500 unità richiesta al secondo, come illustrato nell'esempio seguente. Vedere [Importare in DocumentDB](documentdb-import-data.md#DocumentDBSeqTarget) per altre informazioni.

![Migrazione dei dati a una raccolta partizionata in DocumentDB][3]  

> [!TIP]
> Per accelerare l'importazione, provare ad aumentare il numero di richieste parallele a 100 o a un valore superiore per sfruttare la maggiore velocità effettiva disponibile per le raccolte partizionate. 
> 
> 

Dopo aver acquisito le nozioni di base, si esamineranno alcune importanti considerazioni di progettazione relative all'uso delle chiavi di partizione in DocumentDB.

<a name="designing-for-partitioning"></a>
## <a name="designing-for-partitioning"></a>Progettazione per il partizionamento
La scelta della chiave di partizione è una decisione importante da prendere in fase di progettazione. Questa sezione descrive alcuni dei compromessi da applicare quando si seleziona una chiave di partizione per la raccolta.

### <a name="partition-key-as-the-transaction-boundary"></a>Chiave di partizione come limite delle transazioni
La scelta della chiave di partizione deve bilanciare la necessità di consentire l'uso di transazioni rispetto al requisito di distribuire le entità tra più chiavi di partizione per garantire una soluzione scalabile. Da una parte, è possibile impostare la stessa chiave di partizione per tutti i documenti. Tuttavia, questa scelta potrebbe limitare la scalabilità della soluzione. Dall'altra parte, è possibile assegnare a ogni documento una chiave di partizione univoca. In questo modo, la soluzione risulterebbe altamente scalabile, ma impedirebbe di usare transazioni tra documenti diversi mediante stored procedure e trigger. Una chiave di partizione ideale consente di usare query efficienti e dispone di una quantità sufficiente di cardinalità per garantire la scalabilità della soluzione. 

### <a name="avoiding-storage-and-performance-bottlenecks"></a>Come evitare colli di bottiglia per l'archiviazione e le prestazioni
Un altro elemento importante è scegliere una proprietà che consenta di distribuire le scritture su una serie di valori distinti. Le richieste per la stessa chiave di partizione non possono superare la velocità effettiva di una partizione singola e saranno limitate. È quindi importante scegliere una chiave di partizione che non generi **"aree sensibili"** all'interno dell'applicazione. Poiché tutti i dati per una singola chiave di partizione devono essere archiviati in una partizione, è anche consigliabile evitare le chiavi di partizione con volumi elevati di dati per lo stesso valore. 

### <a name="examples-of-good-partition-keys"></a>Esempi di chiavi di partizione efficaci
Di seguito sono riportati alcuni esempi che illustrano come scegliere la chiave di partizione per l'applicazione:

* Se si implementa un back-end del profilo utente, l'ID utente rappresenta la scelta ideale per la chiave di partizione.
* Se si archiviano dati IoT, ad esempio lo stato del dispositivo, l'ID dispositivo rappresenta la scelta ideale per la chiave di partizione.
* Se si usa DocumentDB per la registrazione di dati di serie temporali, il nome host o l'ID processo rappresenta la scelta ottimale per la chiave di partizione.
* Se si dispone di un'architettura multi-tenant, l'ID tenant rappresenta la scelta ideale per la chiave di partizione.

Si noti che in alcuni casi d'uso (come l'IoT e i profili utente descritti in precedenza), la chiave di partizione potrebbe corrispondere all'id (chiave del documento). In altri casi, come ad esempio i dati di serie temporali, la chiave di partizione potrebbe essere diversa rispetto all'id.

### <a name="partitioning-and-loggingtime-series-data"></a>Partizionamento e registrazione di dati di serie temporali
Uno dei casi di utilizzo più comuni di DocumentDB è la registrazione e la telemetria. È importante scegliere una chiave di partizione efficace perché potrebbe essere necessario scrivere/leggere grandi volumi di dati. La scelta dipende dalla frequenza di lettura e scrittura e dai tipi di query che si prevede di eseguire. Di seguito sono riportati alcuni suggerimenti su come scegliere una chiave di partizione efficace.

* Se il caso di utilizzo prevede una frequenza ridotta di scritture eseguite in un lungo intervallo di tempo e la necessità di eseguire query in base agli intervalli di timestamp e ad altri filtri, si consiglia l'uso di un rollup del timestamp, ad esempio l'uso della data come chiave di partizione. Ciò consente di eseguire query su tutti i dati per una data da una singola partizione. 
* Se il carico di lavoro prevede molte scritture, che in generale sono più comuni, è opportuno usare una chiave di partizione non basata su timestamp in modo che DocumentDB possa distribuire in modo uniforme le scritture in più partizioni. In questo caso, un nome host, un ID processo, un ID attività o un'altra proprietà con una cardinalità elevata è una scelta efficace. 
* Il terzo è un approccio ibrido in cui si hanno più raccolte, una per ogni giorno/mese e la chiave di partizione è una proprietà granulare, ad esempio un nome host. Il vantaggio di questo approccio riguarda la possibilità di impostare diverse velocità effettive in base alla finestra temporale, ad esempio il provisioning della raccolta per il mese corrente viene eseguito con una velocità effettiva maggiore perché viene usata per letture e scritture, mentre i per i mesi precedenti è possibile ridurre la velocità effettiva perché vengono usati solo per le letture.

### <a name="partitioning-and-multi-tenancy"></a>Partizionamento e multi-tenancy
Se si implementa un'applicazione multi-tenant usando DocumentDB, sono disponibili due modelli principali per implementare tenancy con DocumentDB: una chiave di partizione per ogni tenant e una raccolta per ogni tenant. Di seguito sono riportati vantaggi e svantaggi di ogni modello:

* Una chiave di partizione per ogni tenant: in questo modello, i tenant vengono collocati all'interno di una raccolta singola. Tuttavia, le query e gli inserimenti di documenti all'interno di un tenant possono essere eseguiti a fronte di una partizione singola. È anche possibile implementare la logica transazionale su tutti i documenti all'interno di un tenant. Poiché più tenant condividono una raccolta, è possibile risparmiare i costi di archiviazione e velocità effettiva raggruppando le risorse per i tenant all'interno di una raccolta singola anziché eseguendo il provisioning di maggiore capacità aggiuntiva per ogni tenant. Lo svantaggio è che non si ottiene l'isolamento delle prestazioni per ogni tenant. Gli aumenti di prestazioni/velocità effettiva si applicano all'intera raccolta rispetto agli aumenti previsti per i tenant.
* Una raccolta per ogni tenant: ogni tenant dispone di una raccolta propria. In questo modello è possibile riservare le prestazioni per ogni tenant. Grazie al modello di determinazione prezzi basato sull'uso di DocumentDB, questo modello è più conveniente per applicazioni multi-tenant con un numero ridotto di tenant.

È anche possibile usare un approccio combinato/a livelli che colloca tenant di dimensioni ridotte ed esegue la migrazione di tenant di dimensioni maggiori alla relativa raccolta.

## <a name="next-steps"></a>Passaggi successivi
Questo articolo descrive il funzionamento del partizionamento in Azure DocumentDB, come creare raccolte partizionate e come scegliere una chiave di partizione efficace per l'applicazione. 

* Eseguire il test delle prestazioni e della scalabilità con DocumentDB. Per un esempio, vedere [Test delle prestazioni e della scalabilità con Azure DocumentDB](documentdb-performance-testing.md) .
* Introduzione alla programmazione con gli [SDK](documentdb-sdk-dotnet.md) o l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)
* Informazioni sulla [velocità effettiva con provisioning in DocumentDB](documentdb-performance-levels.md)

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png  



