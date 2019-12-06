---
title: Gestire i criteri di indicizzazione in Azure Cosmos DB
description: Informazioni su come gestire i criteri di indicizzazione, includere o escludere una proprietà dall'indicizzazione, come definire l'indicizzazione con diversi Azure Cosmos DB SDK
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: 3b98975df194af4625087e1beb556efb2a347f43
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872061"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Gestire i criteri di indicizzazione in Azure Cosmos DB

In Azure Cosmos DB, i dati vengono indicizzati seguendo i [criteri di indicizzazione](index-policy.md) definiti per ogni contenitore. I criteri di indicizzazione predefiniti per i contenitori appena creati applicano indici di intervallo per qualsiasi stringa o numero. Questo criterio può essere sostituito con i criteri di indicizzazione personalizzati.

## <a name="indexing-policy-examples"></a>Esempi di criteri di indicizzazione

Di seguito sono riportati alcuni esempi di criteri di indicizzazione visualizzati nel formato JSON, ovvero come sono presentati nel portale di Azure. Gli stessi parametri possono essere impostati tramite l'interfaccia della riga di comando di Azure o qualsiasi SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Criteri di esclusione per escludere in modo selettivo alcuni percorsi delle proprietà

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

Questo criterio di indicizzazione è equivalente a quello riportato di seguito, che imposta manualmente ```kind```, ```dataType```e ```precision``` sui rispettivi valori predefiniti. Queste proprietà non sono più necessarie per impostare in modo esplicito ed è possibile ometterle completamente dal criterio di indicizzazione (come illustrato nell'esempio precedente).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Criteri di inclusione per includere in modo selettivo alcuni percorsi delle proprietà

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Questo criterio di indicizzazione è equivalente a quello riportato di seguito, che imposta manualmente ```kind```, ```dataType```e ```precision``` sui rispettivi valori predefiniti. Queste proprietà non sono più necessarie per impostare in modo esplicito ed è possibile ometterle completamente dal criterio di indicizzazione (come illustrato nell'esempio precedente).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE] 
> È in genere consigliabile usare un criterio di indicizzazione di **esclusione** per consentire ad Azure Cosmos DB di indicizzare in modo proattivo qualsiasi nuova proprietà che può essere aggiunta al modello.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Usare un indice spaziale solo in un percorso di proprietà specifico

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>Esempi di criteri di indicizzazione composti

Oltre a includere o escludere i percorsi per le singole proprietà, è anche possibile specificare un indice composto. Se si desidera eseguire una query con una clausola `ORDER BY` per più proprietà, è obbligatorio un [indice composto](index-policy.md#composite-indexes) su queste proprietà. Inoltre, gli indici compositi avranno un vantaggio in materia di prestazioni per le query che hanno un filtro e hanno una clausola ORDER BY su proprietà diverse.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Indice composto definito per (nome cres, età disc):

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

Per la query #1 e la #2 di query è necessario l'indice composto precedente per nome ed età:

Query 1:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

Query 2:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Questo indice composito trarrà vantaggio dalla #3 di query e #4 di query e ottimizza i filtri:

#3 di query:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

#4 di query:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Indice composto definito per (nome ASC, Age ASC) e (nome ASC, Age DESC):

È possibile definire più indici composti differenti entro lo stesso criterio di indicizzazione.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Indice composto definito per (nome ASC, Age ASC):

È facoltativo specificare l'ordine. Se non specificato, l'ordine è crescente.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Escludere tutti i percorsi delle proprietà mantenendo però l'indicizzazione attiva

Questo criterio può essere usato in situazioni in cui la funzionalità [Time-to-Live (TTL)](time-to-live.md) è attiva ma non è richiesto alcun indice secondario (per usare Azure Cosmos DB come puro archivio chiave-valore).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Nessuna indicizzazione

Questo criterio disattiva l'indicizzazione. Se `indexingMode` è impostato su `none`, non è possibile impostare una durata (TTL) sul contenitore.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Aggiornamento dei criteri di indicizzazione

In Azure Cosmos DB, i criteri di indicizzazione possono essere aggiornati utilizzando uno dei metodi seguenti:

- dal portale di Azure
- usando l'interfaccia della riga di comando di Azure
- uso di PowerShell
- usando uno degli SDK

Un [aggiornamento dei criteri di indicizzazione](index-policy.md#modifying-the-indexing-policy) attiva una trasformazione dell'indice. Lo stato di avanzamento della trasformazione può essere monitorato anche dagli SDK.

> [!NOTE]
> Quando si aggiornano i criteri di indicizzazione, le Scritture in Azure Cosmos DB verranno interrotte. Durante la reindicizzazione, le query possono restituire risultati parziali durante l'aggiornamento dell'indice.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

I contenitori di Azure Cosmos archiviano i criteri di indicizzazione come un documento JSON che il portale di Azure consente di modificare direttamente.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Creare un nuovo account Azure Cosmos o selezionare un account esistente.

1. Aprire il riquadro **Esplora dati** e selezionare il contenitore da usare.

1. Fare clic su **Scalabilità e impostazioni**.

1. Modificare il documento JSON dei criteri di indicizzazione (vedere gli esempi [di seguito](#indexing-policy-examples))

1. Al termine, fare clic su **Salva**.

![Gestire l'indicizzazione usando il portale di Azure](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

Per creare un contenitore con criteri di indicizzazione personalizzati, vedere [creare un contenitore con criteri di indice personalizzati usando l'interfaccia](manage-with-cli.md#create-a-container-with-a-custom-index-policy) della riga di comando

## <a name="use-powershell"></a>Usare PowerShell

Per creare un contenitore con criteri di indicizzazione personalizzati, vedere [creare un contenitore con criteri di indice personalizzati usando PowerShell](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk-v2"></a>Usare .NET SDK v2

L'oggetto `DocumentCollection` di [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) espone una proprietà `IndexingPolicy` che consente di modificare la `IndexingMode` e aggiungere o rimuovere `IncludedPaths` e `ExcludedPaths`.

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Per monitorare l'avanzamento della trasformazione dell'indice, passare un oggetto `RequestOptions` che consente di impostare la proprietà `PopulateQuotaInfo` su `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-net-sdk-v3"></a>Usare .NET SDK V3

L'oggetto `ContainerProperties` di [.NET SDK V3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (vedere [questa Guida introduttiva](create-sql-api-dotnet.md) per quanto riguarda l'utilizzo) espone una proprietà `IndexingPolicy` che consente di modificare la `IndexingMode` e aggiungere o rimuovere `IncludedPaths` e `ExcludedPaths`.

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

Per tenere traccia dello stato di avanzamento della trasformazione dell'indice, passare un oggetto `RequestOptions` che imposta la proprietà `PopulateQuotaInfo` su `true`, quindi recuperare il valore dall'intestazione della risposta `x-ms-documentdb-collection-index-transformation-progress`.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Quando si definiscono i criteri di indicizzazione personalizzati durante la creazione di un nuovo contenitore, l'SDK V3 Fluent API consente di scrivere questa definizione in modo conciso ed efficiente:

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Usare Java SDK

L'oggetto `DocumentCollection` da [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (vedere [questo Avvio rapido](create-sql-api-java.md) per quanto riguarda l'utilizzo) espone i metodi `getIndexingPolicy()` e `setIndexingPolicy()`. L'oggetto `IndexingPolicy` che essi manipolano consente di modificare la modalità di indicizzazione e di aggiungere o rimuovere percorsi inclusi ed esclusi.

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
ExcludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

Per monitorare l'avanzamento della trasformazione dell'indice in un contenitore, passare un oggetto `RequestOptions` che richiede di popolare le informazioni sulla quota, quindi recuperare il valore dall'intestazione della risposta `x-ms-documentdb-collection-index-transformation-progress`.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Usare Node.js SDK

L'interfaccia `ContainerDefinition` di [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (vedere [questo Avvio rapido](create-sql-api-nodejs.md) per quanto riguarda l'utilizzo) espone una proprietà `indexingPolicy` che consente di modificare `indexingMode` e aggiungere o rimuovere `includedPaths` e `excludedPaths`.

Recuperare i dettagli del contenitore

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Impostare la modalità di indicizzazione su coerente

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Aggiungere un percorso incluso, incluso un indice spaziale

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Aggiungi percorso escluso

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

Aggiornare il contenitore con le modifiche

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Per monitorare l'avanzamento della trasformazione dell'indice in un contenitore, passare un oggetto `RequestOptions` che imposta la proprietà `populateQuotaInfo` su `true`, quindi recuperare il valore dall'intestazione della risposta `x-ms-documentdb-collection-index-transformation-progress`.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Usare Python SDK

Quando si usa [Python SDK](https://pypi.org/project/azure-cosmos/) (vedere [questo Avvio rapido](create-sql-api-python.md) per quanto riguarda l'utilizzo), la configurazione del contenitore viene gestita come un dizionario. Da questo dizionario, è possibile accedere ai criteri di indicizzazione e a tutti i relativi attributi.

Recuperare i dettagli del contenitore

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Impostare la modalità di indicizzazione su coerente

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Definire un criterio di indicizzazione con un percorso incluso e un indice spaziale

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definire un criterio di indicizzazione con un percorso escluso

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Aggiungere un indice composito

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

Aggiornare il contenitore con le modifiche

```python
response = client.ReplaceContainer(containerPath, container)
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

- [Panoramica dell'indicizzazione](index-overview.md)
- [Criteri di indicizzazione](index-policy.md)
