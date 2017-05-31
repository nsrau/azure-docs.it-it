---
title: "Partizionamento e scalabilità in Azure Cosmos DB | Microsoft Docs"
description: Informazioni sul funzionamento del partizionamento in Azure Cosmos DB, sulla configurazione del partizionamento e delle chiavi di partizione e su come scegliere la chiave di partizione corretta per l&quot;applicazione.
services: cosmosdb
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 702c39b4-1798-48dd-9993-4493a2f6df9e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 55793a8b8abd9d6c441019c0319e24561c392987
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="partitioning-in-azure-cosmos-db-using-the-documentdb-api"></a>Partizionamento in Azure Cosmos DB con l'API DocumentDB

[Microsoft Azure Cosmos DB](../cosmos-db/introduction.md) è un servizio di database multimodello con distribuzione globale progettato per ottenere prestazioni rapide e prevedibili e per eseguire facilmente il ridimensionamento in base alla crescita dell'applicazione. 

Questo articolo offre una panoramica dell'uso del partizionamento dei contenitori Cosmos DB con l'API DocumentDB. Per una panoramica dei concetti e delle procedure consigliate per il partizionamento con qualsiasi API di Azure Cosmos DB, vedere l'articolo relativo a [partizionamento e scalabilità orizzontale](../cosmos-db/partition-data.md). 

Per iniziare a usare il codice, scaricare il progetto da [Github](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:   

* Come funziona il partizionamento in Azure Cosmos DB?
* Come si configura il partizionamento in Azure Cosmos DB?
* Cosa sono le chiavi di partizione e come scegliere la chiave di partizione corretta per l'applicazione?

Per iniziare a usare il codice, scaricare il progetto dell'[esempio di driver di test delle prestazioni di Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

<!-- placeholder until we have a permanent solution-->
<a name="partition-keys"></a>
<a name="single-partition-and-partitioned-collections"></a>
<a name="migrating-from-single-partition"></a>
## Chiavi di partizione Nell'API DocumentDB, la definizione delle chiavi di partizione viene specificata sotto forma di percorso JSON. La tabella seguente mostra esempi di definizioni di chiavi di partizione e i valori corrispondenti a ognuna. La chiave di partizione viene specificata come percorso, ad esempio `/department` rappresenta la proprietà department. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Chiave di partizione</strong></p></td>
            <td valign="top"><p><strong>Descrizione</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>Corrisponde al valore doc.department, in cui doc è l'elemento.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>Corrisponde al valore doc.properties.name, in cui doc è l'elemento (proprietà annidata).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>Corrisponde al valore doc.id (ID e chiave di partizione sono la stessa proprietà).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"nome reparto"</p></td>
            <td valign="top"><p>Corrisponde al valore doc["nome reparto"], in cui doc è l'elemento.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> La sintassi della chiave di partizione è simile alla specifica dei percorsi dei criteri di indicizzazione con la differenza fondamentale che il percorso corrisponde alla proprietà anziché al valore, ossia non sono presenti caratteri jolly alla fine. Ad esempio, per indicizzare i valori nel reparto si specifica /department/?, mentre per la definizione della chiave di partizione si usa /department. La chiave di partizione viene indicizzata in modo implicito e non può essere esclusa dall'indicizzazione usando override dei criteri di indicizzazione.
> 
> 

Di seguito viene esaminato come la scelta della chiave di partizione influisce sulle prestazioni dell'applicazione.

## <a name="working-with-the-documentdb-sdks"></a>Uso di DocumentDB SDK
Azure Cosmos DB ha aggiunto il supporto per il partizionamento automatico con la [versione 2015-12-16 dell'API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx). Per creare contenitori partizionati, è necessario scaricare la versione 1.6.0 o una successiva dell'SDK in una delle piattaforme di SDK supportate (.NET, Node.js, Java, Python e MongoDB). 

### <a name="creating-containers"></a>Creazione di contenitori
L'esempio seguente illustra un frammento .NET per la creazione di un contenitore per archiviare i dati di telemetria dei dispositivi a una velocità effettiva di 20.000 unità richiesta al secondo. L'SDK imposta il valore OfferThroughput, che a sua volta imposta l'intestazione di richiesta `x-ms-offer-throughput` nell'API REST. Qui viene impostato `/deviceId` come chiave di partizione. La scelta della chiave di partizione viene salvata con il resto dei metadati del contenitore, come il nome e i criteri di indicizzazione.

Per questo esempio è stato scelto `deviceId` per due motivi: (a) considerato il numero elevato di dispositivi, le scritture possono essere distribuite in modo uniforme tra le partizioni consentendo di scalare il database per l'inserimento di volumi elevati di dati e (b) molte richieste, ad esempio il recupero della lettura più recente per un dispositivo, sono limitate a un singolo deviceId e possono essere recuperate da una partizione singola.

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Container for device telemetry. Here the property deviceId will be used as the partition key to 
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

Questo metodo effettua una chiamata API REST a Cosmos DB e il servizio eseguirà il provisioning di una serie di partizioni in base alla velocità effettiva richiesta. È possibile modificare la velocità effettiva di un contenitore in base all'evoluzione delle esigenze in termini di prestazioni. 

### <a name="reading-and-writing-items"></a>Lettura e scrittura di elementi
A questo punto si inseriscono i dati in Cosmos DB. Di seguito è riportata una classe di esempio contenente la lettura di un dispositivo e una chiamata a CreateDocumentAsync per inserire una nuova lettura del dispositivo in un contenitore. Questo esempio sfrutta l'API DocumentDB:

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

L'elemento viene letto in base alla chiave di partizione e all'ID, viene aggiornato e infine viene eliminato in base alla chiave di partizione e all'ID. Si noti che le letture includono un valore PartitionKey, corrispondente all'intestazione di richiesta `x-ms-documentdb-partitionkey` nell'API REST.

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

### <a name="querying-partitioned-containers"></a>Esecuzione di query sui contenitori partizionati
Quando si eseguono query sui dati dei contenitori partizionati, Azure Cosmos DB instrada automaticamente la query alle partizioni corrispondenti ai valori di chiave di partizione specificati nel filtro (se presenti). Ad esempio, questa query viene instradata solo alla partizione contenente la chiave di partizione "XMS-0001".

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

Cosmos DB supporta le [funzioni di aggregazione](documentdb-sql-query.md#Aggregates) `COUNT`, `MIN`, `MAX`, `SUM` e `AVG` su contenitori partizionati con SQL a partire da SDK 1.12.0 e versioni successive. Le query devono includere un unico operatore di aggregazione e un singolo valore nella proiezione.

### <a name="parallel-query-execution"></a>Esecuzione di query in parallelo
Gli SDK di Cosmos DB 1.9.0 e versioni successive supportano opzioni per l'esecuzione di query in parallelo che consentono di eseguire query a bassa latenza sulle raccolte partizionate, anche quando è coinvolto un numero elevato di partizioni. Ad esempio, la query seguente è configurata in modo da essere eseguita in parallelo tra le partizioni.

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
È possibile gestire l'esecuzione di query in parallelo, ottimizzando i parametri seguenti:

* Impostando `MaxDegreeOfParallelism` è possibile controllare il grado di parallelismo, ossia il numero massimo di connessioni di rete simultanee alle partizioni del contenitore. Se si imposta questo valore su -1, il grado di parallelismo viene gestito dall'SDK. Se `MaxDegreeOfParallelism` non è specificato o è impostato su 0, ovvero il valore predefinito, esisterà una sola connessione di rete alle partizioni del contenitore.
* Impostando `MaxBufferedItemCount` è possibile raggiungere un compromesso tra latenza della query e uso della memoria dal lato client. Se si omette questo parametro o si imposta su -1, il numero di elementi memorizzati nel buffer durante l'esecuzione di query in parallelo viene gestito dall'SDK.

Considerato lo stato della raccolta, una query in parallelo restituirà i risultati nello stesso ordine dell'esecuzione seriale. Quando si esegue una query tra partizioni che include l'ordinamento (ORDER BY e/o TOP), l'SDK di DocumentDB esegue la query in parallelo tra le partizioni e unisce i risultati ordinati parzialmente sul lato client per produrre risultati ordinati a livello globale.

### <a name="executing-stored-procedures"></a>Esecuzione di stored procedure
È anche possibile eseguire transazioni atomiche su documenti con lo stesso ID dispositivo, ad esempio se si gestiscono le aggregazioni o lo stato più recente di un dispositivo in un singolo elemento. 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
   
Nella sezione successiva verrà illustrato come passare a contenitori partizionati da contenitori a partizione singola.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata offerta una panoramica dell'uso del partizionamento dei contenitori Cosmos DB con l'API DocumentDB. Per una panoramica dei concetti e delle procedure consigliate per il partizionamento con qualsiasi API di Azure Cosmos DB, vedere anche l'articolo relativo a [partizionamento e scalabilità orizzontale](../cosmos-db/partition-data.md). 

* Eseguire il test delle prestazioni e della scalabilità con Cosmos DB. Per un esempio, vedere [Test delle prestazioni e della scalabilità con Azure Cosmos DB](documentdb-performance-testing.md).
* Introduzione alla programmazione con gli [SDK](documentdb-sdk-dotnet.md) o l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)
* Informazioni sulla [velocità effettiva di provisioning in Azure Cosmos DB](documentdb-request-units.md)


