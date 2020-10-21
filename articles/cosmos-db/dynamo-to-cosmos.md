---
title: Eseguire la migrazione dell'applicazione da Amazon DynamoDB ad Azure Cosmos DB
description: Informazioni su come eseguire la migrazione dell'applicazione .NET da Amazon DynamoDB ad Azure Cosmos DB
author: manishmsfte
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: 167d1f21a2eb7ea4c685b5bbbb5d8d64fcc1367e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278696"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>Eseguire la migrazione dell'applicazione da Amazon DynamoDB ad Azure Cosmos DB

Azure Cosmos DB è un database scalabile, distribuito a livello globale e completamente gestito. Offre un accesso ai dati con bassa latenza garantita. Per altre informazioni su Azure Cosmos DB, vedere l'articolo [Panoramica](introduction.md). Questo articolo descrive come eseguire la migrazione dell'applicazione .NET da DynamoDB ad Azure Cosmos DB con modifiche minime al codice.

## <a name="conceptual-differences"></a>Differenze concettuali

Di seguito sono riportate le principali differenze concettuali tra Azure Cosmos DB e DynamoDB:

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|Non applicabile|  Database |
|Tabella      |  Raccolta |
|  Elemento |  Document |
|Attributo|Campo|
|Indice secondario|Indice secondario|
|Chiave primaria - Chiave di partizione|Chiave di partizione|
|Chiave primaria - Chiave di ordinamento| Non obbligatorio |
|STREAM|Feed di modifiche|
|Unità di calcolo di scrittura|Unità richiesta (flessibile, può essere usata per operazioni di lettura o scrittura)|
|Unità di calcolo di lettura    |Unità richiesta (flessibile, può essere usata per operazioni di lettura o scrittura)|
|Tabelle globali| Non obbligatorio. È possibile selezionare direttamente l'area durante il provisioning dell'account Azure Cosmos (è possibile modificare l'area in un secondo momento)|

## <a name="structural-differences"></a>Differenze strutturali

Azure Cosmos DB ha una struttura JSON più semplice rispetto a quella di DynamoDB. L'esempio seguente illustra le differenze

**DynamoDB**:

L'oggetto JSON seguente rappresenta il formato dei dati in DynamoDB

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB**:

L'oggetto JSON seguente rappresenta il formato dei dati in Cosmos DB

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>Migrare i dati

Sono disponibili diverse opzioni per eseguire la migrazione dei dati ad Azure Cosmos DB. Per altre informazioni, vedere l'articolo [Opzioni per la migrazione dei dati locali o cloud ad Azure Cosmos DB](cosmosdb-migrationchoices.md).

## <a name="migrate-your-code"></a>Eseguire la migrazione del codice

Questo articolo riguarda la migrazione del codice di un'applicazione ad Azure Cosmos DB, che è l'aspetto fondamentale della migrazione del database. Per favorire la riduzione della curva di apprendimento, le sezioni seguenti includono un confronto con codice affiancato tra Amazon DynamoDB e il frammento di codice di Azure Cosmos DB equivalente.

Per scaricare il codice sorgente, clonare il repository seguente:

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>Prerequisiti

- .NET Framework 4.7.2
- Visual Studio 2019
- Accesso a un account API SQL di Azure Cosmos DB
- Installazione locale di Amazon DynamoDB
- Java 8
- Eseguire la versione scaricabile di Amazon DynamoDB sulla porta 8000 (è possibile modificare e configurare il codice)

### <a name="set-up-your-code"></a>Configurare il codice

Aggiungere il pacchetto NuGet seguente al progetto:

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>Stabilire la connessione

**DynamoDB**:

In Amazon DynamoDB viene usato il codice seguente per la connessione:

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB**:

Per la connessione ad Azure Cosmos DB, aggiornare il codice come segue:

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**Ottimizzare la connessione in Azure Cosmos DB**

Con Azure Cosmos DB è possibile usare le opzioni seguenti per ottimizzare la connessione:

* **ConnectionMode**: usare la modalità di connessione diretta per connettersi ai nodi dati nel servizio Azure Cosmos DB. Usare la modalità gateway solo per inizializzare e memorizzare nella cache gli indirizzi logici ed eseguire gli aggiornamenti. Per altri dettagli, vedere l'articolo relativo alle [modalità di connettività](sql-sdk-connection-modes.md).

* **ApplicationRegion**: questa opzione viene usata per impostare l'area di replica geografica preferita per l'interazione con Azure Cosmos DB. Per altre informazioni, vedere l'articolo relativo alla [distribuzione globale](distribute-data-globally.md).

* **ConsistencyLevel**: questa opzione viene usata per eseguire l'override del livello di coerenza predefinito. Per altre informazioni, vedere l'articolo relativo ai [livelli di coerenza](consistency-levels.md).

* **BulkExecutionMode**: questa opzione viene usata per eseguire operazioni bulk impostando la proprietà *AllowBulkExecution* su true. Per altre informazioni, vedere l'articolo relativo all'[importazione bulk](tutorial-sql-api-dotnet-bulk-import.md).

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>Eseguire il provisioning del contenitore

**DynamoDB**:

Prima di archiviare i dati in Amazon DynamoDB è necessario creare la tabella. In questo processo si definiscono lo schema, il tipo di chiave e gli attributi, come illustrato nel codice seguente:

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB**:

In Amazon DynamoDB è necessario e il provisioning delle unità di calcolo di lettura e di scrittura. Invece in Azure Cosmos DB si specifica la velocità effettiva come [unità richieste (UR/sec)](request-units.md), che possono essere usate dinamicamente per qualsiasi operazione. I dati sono organizzati nella sequenza database --> contenitore --> elemento. È possibile specificare la velocità effettiva a livello di database o a livello di raccolta o a entrambi i livelli.

Per creare un database:

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

Per creare il contenitore:

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>Caricare i dati

**DynamoDB**:

Il codice seguente illustra come caricare i dati in Amazon DynamoDB. moviesArray è costituito da un elenco di documenti JSON, quindi è necessario scorrere e caricare il documento JSON in Amazon DynamoDB:

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB**:

In Azure Cosmos DB è possibile optare per lo streaming e la scrittura con `moviesContainer.CreateItemStreamAsync()`. Tuttavia in questo esempio il codice JSON verrà deserializzato nel tipo *MovieModel* per illustrare la funzionalità di cast dei tipi. Il codice è multithread, e questo consente di usare l'architettura distribuita di Azure Cosmos DB e di accelerare il caricamento:

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>Creare un documento

**DynamoDB**:

La scrittura di un nuovo documento in Amazon DynamoDB non è indipendente dai tipi. Nell'esempio seguente viene usato newItem come tipo di documento:

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB**:

Azure Cosmos DB offre l'indipendenza dai tipi tramite il modello di dati. Viene usato il modello di dati denominato "MovieModel":

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

In Azure Cosmos DB newItem sarà MovieModel:

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>Leggere un documento

**DynamoDB**:

Per la lettura in Amazon DynamoDB è necessario definire le primitive:

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB**:

Invece con Azure Cosmos DB la query è naturale (LINQ):

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

La raccolta di documenti nell'esempio precedente sarà:

- Indipendente dai tipi
- In grado di specificare un'opzione di query naturale.

### <a name="update-an-item"></a>Aggiornare un elemento

**DynamoDB**: Per aggiornare l'elemento in Amazon DynamoDB:

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB**:

In Azure Cosmos DB l'aggiornamento verrà considerato come operazione Upsert, ovvero inserimento del documento se questo non esiste:

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>Eliminare un documento

**DynamoDB**:

Per eliminare un elemento in Amazon DynamoDB è necessario tornare a usare le primitive:

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB**:

In Azure Cosmos DB è possibile ottenere il documento ed eliminarlo in modo asincrono:

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>Eseguire query su documenti

**DynamoDB**:

In Amazon DynamoDB, per eseguire query sui dati sono necessarie le funzioni API:

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB**:

In Azure Cosmos DB è possibile eseguire la proiezione e applicare il filtro in una semplice query SQL:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

Per le operazioni con intervalli, ad esempio "between", è necessario eseguire un'analisi in Amazon DynamoDB:

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

In Azure Cosmos DB è possibile usare query SQL e un'istruzione a riga singola:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>Eliminare un contenitore

**DynamoDB**:

Per eliminare la tabella in Amazon DynamoDB, è possibile specificare:

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB**:

Per eliminare la raccolta in Azure Cosmos DB, è possibile specificare:

```csharp
await moviesContainer.DeleteContainerAsync();
```
Quindi, se necessario, è anche possibile eliminare il database:

```csharp
await cosmosDatabase.DeleteAsync();
```

Come si vede, Azure Cosmos DB supporta le query naturali (SQL) e le operazioni sono asincrone e molto più semplici. È possibile eseguire facilmente la migrazione del codice complesso ad Azure Cosmos DB e il codice diventerà più semplice dopo la migrazione.

### <a name="next-steps"></a>Passaggi successivi

- Informazioni sull'[ottimizzazione delle prestazioni](performance-tips.md)
- Informazioni sull'[ottimizzazione di letture e scritture](key-value-store-cost.md)
- Informazioni sul [monitoraggio in Cosmos DB](monitor-cosmos-db.md)

