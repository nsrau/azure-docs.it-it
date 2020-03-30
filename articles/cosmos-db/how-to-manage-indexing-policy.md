---
title: Gestire i criteri di indicizzazione in Azure Cosmos DB
description: Informazioni su come gestire i criteri di indicizzazione, includere o escludere una proprietà dall'indicizzazione, come definire l'indicizzazione usando SDK diversi di Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: 58a1ee13afa76b152723cb71d4037f9c31cc8d4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252077"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Gestire i criteri di indicizzazione in Azure Cosmos DB

In Azure Cosmos DB, i dati vengono indicizzati seguendo i [criteri di indicizzazione](index-policy.md) definiti per ogni contenitore. I criteri di indicizzazione predefiniti per i contenitori appena creati applicano indici di intervallo per qualsiasi stringa o numero. Questo criterio può essere sostituito con i criteri di indicizzazione personalizzati.

## <a name="indexing-policy-examples"></a>Esempi di criteri di indicizzazione

Ecco alcuni esempi di criteri di indicizzazione illustrati nel formato JSON, ovvero il modo in cui vengono esposti nel portale di Azure.Here are some examples of indexing policies shown in [their JSON format,](index-policy.md#include-exclude-paths)which is how they are exposed on the Azure portal. Gli stessi parametri possono essere impostati tramite l'interfaccia della riga di comando di Azure o qualsiasi SDK.

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

Questo criterio di indicizzazione è equivalente a ```kind``` ```dataType```quello ```precision``` riportato di seguito che imposta manualmente , e sui relativi valori predefiniti. Queste proprietà non sono più necessarie per impostare in modo esplicito ed è possibile ometterle completamente dai criteri di indicizzazione (come illustrato nell'esempio precedente).

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

Questo criterio di indicizzazione è equivalente a ```kind``` ```dataType```quello ```precision``` riportato di seguito che imposta manualmente , e sui relativi valori predefiniti. Queste proprietà non sono più necessarie per impostare in modo esplicito ed è possibile ometterle completamente dai criteri di indicizzazione (come illustrato nell'esempio precedente).

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
            "path": "/_etag/?"
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

Oltre a includere o escludere i percorsi per le singole proprietà, è anche possibile specificare un indice composto. Se si desidera eseguire una query con una clausola `ORDER BY` per più proprietà, è obbligatorio un [indice composto](index-policy.md#composite-indexes) su queste proprietà. Inoltre, gli indici compositi diranno un vantaggio in termini di prestazioni per le query che dispongono di un filtro e hanno una clausola ORDER BY su proprietà diverse.

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

L'indice composito precedente in base al nome e all'età è necessario per #1 di query e #2 di query:The above composite index on name and age is required for Query #1 and Query #2:

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

Questo indice composito trarrà vantaggio da Query #3 ed Query #4 e ottimizzerà i filtri:This composite index will benefit Query #3 and Query #4 and optimize the filters:

#3 query:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

#4 query:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Indice composito definito per (nome ASC, età ASC) e (nome ASC, età DESC):

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

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Indice composito definito per (nome ASC, età ASC):

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

Questo criterio disattivirà l'indicizzazione. Se `indexingMode` è `none`impostato su , non è possibile impostare un valore TTL sul contenitore.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Aggiornamento dei criteri di indicizzazione

In Azure Cosmos DB, i criteri di indicizzazione possono essere aggiornati usando uno dei metodi seguenti:In Azure Cosmos DB, the indexing policy can be updated using any of the below methods:

- dal portale di Azure
- usando l'interfaccia della riga di comando di Azure
- utilizzo di PowerShell
- usando uno degli SDK

Un [aggiornamento dei criteri di indicizzazione](index-policy.md#modifying-the-indexing-policy) attiva una trasformazione dell'indice. Lo stato di avanzamento della trasformazione può essere monitorato anche dagli SDK.

> [!NOTE]
> Quando si aggiornano i criteri di indicizzazione, le scritture in Azure Cosmos DB saranno ininterrotte. Durante la reindicizzazione, le query possono restituire risultati parziali durante l'aggiornamento dell'indice.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

I contenitori di Azure Cosmos archiviano i criteri di indicizzazione come un documento JSON che il portale di Azure consente di modificare direttamente.

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Creare un nuovo account Azure Cosmos o selezionare un account esistente.

1. Aprire il riquadro **Esplora dati** e selezionare il contenitore da usare.

1. Fare clic su **Scalabilità e impostazioni**.

1. Modificare il documento JSON dei criteri di indicizzazione (vedere gli esempi [di seguito](#indexing-policy-examples))

1. Al termine, fare clic su **Salva**.

![Gestire l'indicizzazione usando il portale di Azure](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

Per creare un contenitore con un criterio di indicizzazione personalizzato, vedere Creare un contenitore con criteri di [indice personalizzati tramite l'interfaccia della riga di comandoTo](manage-with-cli.md#create-a-container-with-a-custom-index-policy) create a container with a custom indexing policy see, Create a container with a custom index policy using CLI

## <a name="use-powershell"></a>Usare PowerShell

Per creare un contenitore con un criterio di indicizzazione personalizzato, vedere Creare un contenitore con un criterio di [indice personalizzato tramite PowershellTo](manage-with-powershell.md#create-container-custom-index) create a container with a custom indexing policy see, Create a container with a custom index policy using Powershell

## <a name="use-the-net-sdk-v2"></a>Usare .NET SDK v2

L'oggetto `DocumentCollection` di [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) espone `IndexingPolicy` una `IndexingMode` proprietà che `IncludedPaths` consente `ExcludedPaths`di modificare e aggiungere o rimuovere e .

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

## <a name="use-the-net-sdk-v3"></a>Utilizzare .NET SDK V3

`ContainerProperties` L'oggetto di [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (vedere [questa Guida introduttiva](create-sql-api-dotnet.md) relativa al relativo utilizzo) espone una `IndexingPolicy` proprietà che consente di modificare e `IndexingMode` aggiungere o rimuovere `IncludedPaths` e `ExcludedPaths`.

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

Per tenere traccia dello stato `RequestOptions` di avanzamento della trasformazione dell'indice, passare un oggetto che imposta la `PopulateQuotaInfo` proprietà su `true`, quindi recuperare il valore dall'intestazione della `x-ms-documentdb-collection-index-transformation-progress` risposta.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Quando si definisce un criterio di indicizzazione personalizzato durante la creazione di un nuovo contenitore, l'API fluente dell'SDK V3 consente di scrivere questa definizione in modo conciso ed efficiente:When defining a custom indexing policy while creating a new container, the SDK V3's fluent API lets you write this definition in acise and efficient way:

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
IncludedPath includedPath = new IncludedPath();
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

Recuperare i dettagli del contenitoreRetrieve the container's details

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Impostare la modalità di indicizzazione su una coerenza

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Aggiungere il percorso incluso, incluso un indice spazialeAdd included path including a spatial index

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

Aggiungi tracciato escluso

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

## <a name="use-the-python-sdk-v3"></a>Usare Python SDK V3

Quando si usa [Python SDK V3](https://pypi.org/project/azure-cosmos/) (vedere [questa Guida introduttiva](create-sql-api-python.md) relativa al relativo utilizzo), la configurazione del contenitore viene gestita come dizionario. Da questo dizionario, è possibile accedere ai criteri di indicizzazione e a tutti i relativi attributi.

Recuperare i dettagli del contenitoreRetrieve the container's details

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Impostare la modalità di indicizzazione su una coerenza

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Definire un criterio di indicizzazione con un percorso incluso e un indice spazialeDefine an indexing policy with an included path and a spatial index

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

Definire un criterio di indicizzazione con un percorso esclusoDefine an indexing policy with an excluded path

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Aggiungere un indice compositoAdd a composite index

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

## <a name="use-the-python-sdk-v4"></a>Utilizzare Python SDK V4

Quando si utilizza [Python SDK V4](https://pypi.org/project/azure-cosmos/), la configurazione del contenitore viene gestita come dizionario. Da questo dizionario, è possibile accedere ai criteri di indicizzazione e a tutti i relativi attributi.

Recuperare i dettagli del contenitoreRetrieve the container's details

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

Impostare la modalità di indicizzazione su una coerenza

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

Definire un criterio di indicizzazione con un percorso incluso e un indice spazialeDefine an indexing policy with an included path and a spatial index

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definire un criterio di indicizzazione con un percorso esclusoDefine an indexing policy with an excluded path

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Aggiungere un indice compositoAdd a composite index

```python
indexingPolicy['compositeIndexes'] = [
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
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

- [Panoramica dell'indicizzazione](index-overview.md)
- [Criterio di indicizzazione](index-policy.md)
