---
title: SQL query execution in Azure Cosmos DB
description: Informazioni su come creare una query SQL ed eseguirla in Azure Cosmos DB. In questo articolo viene descritto come creare ed eseguire una query SQL utilizzando l'API REST, .Net SDK, JavaScript SDK e vari altri SDK.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871262"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Esecuzione di query SQL del database Cosmos di AzureAzure Cosmos DB SQL query execution

Qualsiasi linguaggio in grado di effettuare richieste HTTP/HTTPS può chiamare l'API REST Cosmos DB. Cosmos DB offre anche librerie di programmazione per i linguaggi di programmazione .NET, Node.js, JavaScript e Python. L'API REST e le librerie supportano tutte l'esecuzione di query tramite SQL e .NET SDK supporta anche [l'esecuzione di query LINQ](sql-query-linq-to-sql.md).

Negli esempi seguenti viene illustrato come creare una query e inviarla a un account di database Cosmos.

## <a name="rest-api"></a><a id="REST-API"></a>REST API

Cosmos DB offre un modello di programmazione aperto RESTful su HTTP. Il modello di risorsa è costituito da un set di risorse in un account di database di cui viene eseguito il provisioning di una sottoscrizione di Azure.The resource model consists of a set of resources under a database account which an Azure subscription provisions. L'account di database è costituito da un set di *database,* ognuno dei quali può contenere più *contenitori*, che a loro volta contengono *elementi,* funzioni definite dall'utente e altri tipi di risorse. Ogni risorsa Cosmos DB è indirizzabile utilizzando un URI logico e stabile. Un set di risorse è denominato *feed*. 

Il modello di interazione di base `GET`con `PUT` `POST`queste `DELETE`risorse è tramite i verbi HTTP, , e , con le relative interpretazioni standard. Consente `POST` di creare una nuova risorsa, eseguire una stored procedure o eseguire una query Cosmos DB. Le query sono sempre operazioni di sola lettura senza nessun effetto collaterale.

Gli esempi seguenti `POST` illustrano una per una query dell'API SQL sugli elementi di esempio. La query include un filtro `name` semplice per la proprietà JSON. Le `x-ms-documentdb-isquery` intestazioni e `application/query+json` Content-Type: indicano che l'operazione è una query. Sostituire `mysqlapicosmosdb.documents.azure.com:443` con l'URI dell'account Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

I risultati sono:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

La query successiva e più complessa restituisce più risultati da un join:The next, more complex query returns multiple results from a join:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

I risultati sono: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Se i risultati di una query non possono essere contenuti in una `x-ms-continuation-token` singola pagina, l'API REST restituisce un token di continuazione tramite l'intestazione della risposta. I client possono impaginare i risultati includendo l'intestazione nei risultati successivi. È inoltre possibile controllare il numero `x-ms-max-item-count` di risultati per pagina tramite l'intestazione numerica.

Se una query ha una funzione di aggregazione come COUNT, la pagina della query può restituire un valore parzialmente aggregato su una sola pagina di risultati. I client devono eseguire un'aggregazione di secondo livello su questi risultati per produrre i risultati finali. Ad esempio, sommare i conteggi restituiti nelle singole pagine per restituire il conteggio totale.

Per gestire i criteri di coerenza `x-ms-consistency-level` dei dati per le query, usare l'intestazione come in tutte le richieste dell'API REST. La coerenza della sessione `x-ms-session-token` richiede anche l'eco dell'intestazione del cookie più recente nella richiesta di query. I criteri di indicizzazione del contenitore sulla quale è stata eseguita la query possono influenzare anche la coerenza dei risultati della query. Con le impostazioni predefinite dei criteri di indicizzazione per i contenitori, l'indice è sempre aggiornato con il contenuto dell'elemento e i risultati della query corrispondono alla coerenza scelta per i dati. Per altre informazioni, vedere [Livelli di coerenza di Azure Cosmos DB][livelli di coerenza].For more information, see [Azure Cosmos DB consistency levels][consistency-levels].

Se i criteri di indicizzazione configurati nel contenitore non supportano la query specificata, il server di database Cosmos di Azure restituisce 400 "Richiesta non valida". Questo messaggio di errore restituisce per le query con percorsi esclusi in modo esplicito dall'indicizzazione. È possibile `x-ms-documentdb-query-enable-scan` specificare l'intestazione per consentire alla query di eseguire un'analisi quando un indice non è disponibile.

È possibile ottenere metriche dettagliate `x-ms-documentdb-populatequerymetrics` sull'esecuzione di query impostando l'intestazione su `true`. Per altre informazioni, vedere [metriche di query SQL per Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C'è (SDK di .NET)

.NET SDK supporta l'esecuzione di query LINQ ed SQL. Nell'esempio seguente viene illustrato come eseguire la query di filtro precedente con .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Nell'esempio seguente vengono confrontate due proprietà per l'uguaglianza all'interno di ogni elemento e vengono utilizzate proiezioni anonime.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Nell'esempio seguente vengono illustrati `SelectMany`i join, espressi tramite LINQ .

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

Il client .NET scorre automaticamente tutte le pagine `foreach` dei risultati della query nei blocchi, come illustrato nell'esempio precedente. Le opzioni di query introdotte nella sezione [API REST](#REST-API) sono `FeedOptions` `FeedResponse` disponibili anche `CreateDocumentQuery` in .NET SDK, usando le classi e nel metodo . È possibile controllare il numero `MaxItemCount` di pagine utilizzando l'impostazione.

È inoltre possibile controllare `IDocumentQueryable` in `IQueryable` modo esplicito il `ResponseContinuationToken` paging creando utilizzando `RequestContinuationToken` `FeedOptions`l'oggetto , quindi leggendo i valori e passandoli come in . È possibile `EnableScanInQuery` impostare per abilitare le analisi quando la query non è supportata dai criteri di indicizzazione configurati. Per i contenitori partizionati, è possibile usare per eseguire `PartitionKey` la query su una singola partizione, anche se Azure Cosmos DB può estrarla automaticamente dal testo della query. È possibile `EnableCrossPartitionQuery` utilizzare per eseguire query su più partizioni.

Per altri esempi di .NET con query, vedere gli [esempi di Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3) in GitHub.For more .NET samples with queries, see the Azure Cosmos DB .NET samples in GitHub.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>API lato server JavaScript

Azure Cosmos DB offre un modello di programmazione per [l'esecuzione della logica dell'applicazione basata su JavaScript](stored-procedures-triggers-udfs.md) direttamente nei contenitori, usando stored procedure e trigger. La logica JavaScript registrata a livello di contenitore può quindi eseguire operazioni di database sugli elementi del contenitore specificato, di cui è stato eseguito il wrapping nelle transazioni ACID di ambiente.

L'esempio seguente mostra `queryDocuments` come usare l'API del server JavaScript per eseguire query dall'interno di stored procedure e trigger:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Livelli di coerenza di Azure Cosmos DBAzure Cosmos DB consistency levels](consistency-levels.md)
