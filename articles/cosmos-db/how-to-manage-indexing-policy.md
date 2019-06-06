---
title: Gestire i criteri di indicizzazione in Azure Cosmos DB
description: Informazioni su come gestire i criteri di indicizzazione in Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 05fd369cfebba03c814507f82755fa6cb6a89400
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386791"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Gestire i criteri di indicizzazione in Azure Cosmos DB

In Azure Cosmos DB, i dati vengono indicizzati seguendo i [criteri di indicizzazione](index-policy.md) definiti per ogni contenitore. I criteri di indicizzazione predefiniti per i contenitori appena creati applicano indici di intervallo per qualsiasi stringa o numero e indici spaziali per qualsiasi oggetto GeoJSON di tipo Point. Questi criteri possono essere sottoposti a override:

- dal portale di Azure
- usando l'interfaccia della riga di comando di Azure
- usando uno degli SDK

Un [aggiornamento dei criteri di indicizzazione](index-policy.md#modifying-the-indexing-policy) attiva una trasformazione dell'indice. Lo stato di avanzamento della trasformazione può essere monitorato anche dagli SDK.

> [!NOTE]
> Come parte dell'aggiornamento dell'SDK e del portale, stiamo cambiando i criteri di indicizzazione per allinearli con un nuovo layout di indicizzazione implementato nei nuovi contenitori. Con questo nuovo layout, tutti i tipi di dati primitivi sono indicizzati come Intervallo con la massima precisione (-1). Di conseguenza, le tipologie e la precisione dell'indice non sono più esposti all'utente. In futuro, gli utenti dovranno semplicemente aggiungere percorsi alla sezione includedPaths e ignorare indexKinds e la precisione. Questa modifica non ha alcun impatto sulle prestazioni ed è possibile continuare ad aggiornare i criteri di indicizzazione con la stessa sintassi. È possibile continuare a usare tutti gli esempi nella documentazione esistente per aggiornare i criteri di indicizzazione.

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

Il comando [az cosmosdb collection update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) dell'interfaccia della riga di comando di Azure consente di sostituire la definizione JSON dei criteri di indicizzazione di un contenitore:

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>Usare .NET SDK v2

L'oggetto `DocumentCollection` di [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (vedere [questo Avvio rapido](create-sql-api-dotnet.md) per quanto riguarda l'utilizzo) espone una proprietà `IndexingPolicy` che consente di modificare `IndexingMode` e aggiungere o rimuovere `IncludedPaths` e `ExcludedPaths`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// set the indexing mode to Consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/headquarters/employees/?" });
// update the container with our changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Per monitorare l'avanzamento della trasformazione dell'indice, passare un oggetto `RequestOptions` che consente di impostare la proprietà `PopulateQuotaInfo` su `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Usare Java SDK

L'oggetto `DocumentCollection` da [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (vedere [questo Avvio rapido](create-sql-api-java.md) per quanto riguarda l'utilizzo) espone i metodi `getIndexingPolicy()` e `setIndexingPolicy()`. L'oggetto `IndexingPolicy` che essi manipolano consente di modificare la modalità di indicizzazione e di aggiungere o rimuovere percorsi inclusi ed esclusi.

```java
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
    DocumentCollection container = result.getResource();
    IndexingPolicy indexingPolicy = container.getIndexingPolicy();
    // set the indexing mode to Consistent
    indexingPolicy.setIndexingMode(IndexingMode.Consistent);
    Collection<ExcludedPath> excludedPaths = new ArrayList<>();
    ExcludedPath excludedPath = new ExcludedPath();
    excludedPath.setPath("/*");
    // add an excluded path
    excludedPaths.add(excludedPath);
    indexingPolicy.setExcludedPaths(excludedPaths);
    // update the container with our changes
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

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read();
// set the indexing mode to Consistent
containerResponse.body.indexingPolicy.indexingMode = "consistent";
// add an excluded path
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/headquarters/employees/?' });
// update the container with our changes
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

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [{"path" : "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>Esempi di criteri di indicizzazione

Di seguito sono riportati alcuni esempi di criteri di indicizzazione visualizzati nel formato JSON, ovvero come sono presentati nel portale di Azure. Gli stessi parametri possono essere impostati tramite l'interfaccia della riga di comando di Azure o qualsiasi SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Criteri di esclusione per escludere in modo selettivo alcuni percorsi delle proprietà
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
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
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
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

Note: È in genere consigliabile usare un criterio di indicizzazione di **esclusione** per consentire ad Azure Cosmos DB di indicizzare in modo proattivo qualsiasi nuova proprietà che può essere aggiunta al modello.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Usare un indice spaziale solo in un percorso di proprietà specifico
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
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
                "path": "/path/to/geojson/property/?",
                "indexes": [
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Escludere tutti i percorsi delle proprietà mantenendo però l'indicizzazione attiva

Questo criterio può essere usato in situazioni in cui la funzionalità [Time-to-Live (TTL)](time-to-live.md) è attiva ma non è richiesto alcun indice secondario (per usare Azure Cosmos DB come puro archivio chiave-valore).
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Nessuna indicizzazione
```
    {
        "indexingMode": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Esempi di criteri di indicizzazione composti

Oltre a includere o escludere i percorsi per le singole proprietà, è anche possibile specificare un indice composto. Se si desidera eseguire una query con una clausola `ORDER BY` per più proprietà, è obbligatorio un [indice composto](index-policy.md#composite-indexes) su queste proprietà.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Indice composto definito per (nome cres, età disc):
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

Questo indice composto sarà in grado di supportare le due query seguenti:

Query 1:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

Query 2:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Indice composto definito per (nome cres, età disc) e (nome cres, età disc):

È possibile definire più indici composti differenti entro lo stesso criterio di indicizzazione. 
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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
            ]
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

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Indice composto definito per (nome cres, età cres):

È facoltativo specificare l'ordine. Se non specificato, l'ordine è crescente.
```
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

- [Panoramica dell'indicizzazione](index-overview.md)
- [Criterio di indicizzazione](index-policy.md)
