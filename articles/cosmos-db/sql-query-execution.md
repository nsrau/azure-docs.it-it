---
title: Esecuzione di query SQL in Azure Cosmos DB
description: Informazioni sull'esecuzione di query SQL in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: 0eca458c344e5c44ad62121db14e6b286dc19a86
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614433"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Esecuzione di query SQL Azure Cosmos DB

Qualsiasi linguaggio in grado di effettuare richieste HTTP/HTTPS può chiamare l'API REST di Cosmos DB. Cosmos DB offre anche librerie di programmazione per i linguaggi di programmazione .NET, node. js, JavaScript e Python. L'API REST e le librerie supportano tutte l'esecuzione di query tramite SQL e .NET SDK supporta anche l' [esecuzione di query LINQ](sql-query-linq-to-sql.md).

Gli esempi seguenti illustrano come creare una query e inviarla a un account del database Cosmos.

## <a id="REST-API"></a>API REST

Cosmos DB offre un modello di programmazione aperto RESTful su HTTP. Il modello di risorse è costituito da un set di risorse in un account di database, che una sottoscrizione di Azure effettua il provisioning. L'account di database è costituito da un set di *database*, ognuno dei quali può contenere più *contenitori*, che a loro volta contengono *elementi*, funzioni definite dall'utente e altri tipi di risorse. Ogni risorsa Cosmos DB è indirizzabile usando un URI logico e stabile. Un set di risorse viene definito *feed*. 

Il modello di interazione di base con queste risorse è tramite i verbi `GET`http `PUT` `POST`,, e `DELETE`, con le proprie interpretazioni standard. Usare `POST` per creare una nuova risorsa, eseguire una stored procedure o emettere una query Cosmos DB. Le query sono sempre operazioni di sola lettura senza nessun effetto collaterale.

Negli esempi seguenti viene illustrato `POST` un oggetto per una query dell'API SQL sugli elementi di esempio. La query ha un semplice filtro sulla proprietà JSON `name` . Le `x-ms-documentdb-isquery` intestazioni and Content-Type `application/query+json` : indicano che l'operazione è una query. Sostituire `mysqlapicosmosdb.documents.azure.com:443` con l'URI per l'account Cosmos DB.

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

La query successiva più complessa restituisce più risultati da un join:

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

Se i risultati di una query non possono essere inseriti in una singola pagina, l'API REST restituisce un token `x-ms-continuation-token` di continuazione tramite l'intestazione della risposta. I client possono impaginare i risultati includendo l'intestazione nei risultati successivi. È anche possibile controllare il numero di risultati per pagina tramite l' `x-ms-max-item-count` intestazione Number.

Se una query include una funzione di aggregazione come COUNT, la pagina di query può restituire un valore parzialmente aggregato su una sola pagina di risultati. I client devono eseguire un'aggregazione di secondo livello su questi risultati per produrre i risultati finali. Ad esempio, sommare i conteggi restituiti nelle singole pagine per restituire il conteggio totale.

Per gestire i criteri di coerenza dei dati per le query `x-ms-consistency-level` , usare l'intestazione come in tutte le richieste API REST. La coerenza di sessione richiede anche l'eco `x-ms-session-token` dell'intestazione del cookie più recente nella richiesta di query. I criteri di indicizzazione del contenitore sulla quale è stata eseguita la query possono influenzare anche la coerenza dei risultati della query. Con le impostazioni predefinite dei criteri di indicizzazione per i contenitori, l'indice è sempre aggiornato con il contenuto dell'elemento e i risultati della query corrispondono alla coerenza scelta per i dati. Per ulteriori informazioni, vedere [Azure Cosmos DB livelli di coerenza] [livelli di coerenza].

Se i criteri di indicizzazione configurati nel contenitore non supportano la query specificata, il server Azure Cosmos DB restituisce 400 "richiesta non valida". Questo messaggio di errore restituisce per le query con percorsi esclusi in modo esplicito dall'indicizzazione. È possibile specificare l' `x-ms-documentdb-query-enable-scan` intestazione per consentire alla query di eseguire un'analisi quando un indice non è disponibile.

È possibile ottenere metriche dettagliate sull'esecuzione di query impostando l' `x-ms-documentdb-populatequerymetrics` intestazione su `true`. Per altre informazioni, vedere [metriche di query SQL per Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

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

Nell'esempio seguente vengono confrontate due proprietà per verificarne l'uguaglianza all'interno di ogni elemento e vengono utilizzate proiezioni anonime.

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

Nell'esempio seguente vengono illustrati i join, espressi `SelectMany`tramite LINQ.

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

Il client .NET scorre automaticamente tutte le pagine dei risultati delle query nei `foreach` blocchi, come illustrato nell'esempio precedente. Le opzioni di query introdotte nella sezione [API REST](#REST-API) sono disponibili anche in .NET SDK, usando le `FeedOptions` classi `CreateDocumentQuery` e `FeedResponse` nel metodo. È possibile controllare il numero di pagine usando l' `MaxItemCount` impostazione.

È anche possibile controllare in modo esplicito il `IDocumentQueryable` paging creando `IQueryable` usando l'oggetto, quindi leggendo `ResponseContinuationToken` i valori e passandoli come `RequestContinuationToken` in `FeedOptions`. È possibile impostare `EnableScanInQuery` per abilitare le analisi quando la query non è supportata dai criteri di indicizzazione configurati. Per i contenitori partizionati, è possibile `PartitionKey` usare per eseguire la query su una singola partizione, anche se Azure Cosmos DB possibile estrarla automaticamente dal testo della query. È possibile utilizzare `EnableCrossPartitionQuery` per eseguire query su più partizioni.

Per altri esempi di .NET con query, vedere gli [esempi di Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet) in GitHub.

## <a id="JavaScript-server-side-API"></a>API lato server JavaScript

Azure Cosmos DB fornisce un modello di programmazione per l'esecuzione di logica [dell'applicazione basata su JavaScript](stored-procedures-triggers-udfs.md) direttamente nei contenitori, usando stored procedure e trigger. La logica JavaScript registrata a livello di contenitore può quindi eseguire operazioni di database sugli elementi del contenitore specificato, racchiusi tra le transazioni ACID di ambiente.

Nell'esempio seguente viene illustrato come utilizzare `queryDocuments` nell'API del server JavaScript per eseguire query dall'interno di stored procedure e trigger:

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

- [Introduzione a Azure Cosmos DB](introduction.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Livelli di coerenza Azure Cosmos DB](consistency-levels.md)
