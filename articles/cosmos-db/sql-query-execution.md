---
title: Esecuzione di query SQL in Azure Cosmos DB
description: Informazioni sull'esecuzione di query SQL in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: e4e26b658bd29e4589be40e4d29935059836c909
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342758"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Esecuzione di query di Azure Cosmos DB SQL

Qualsiasi linguaggio in grado di effettuare richieste HTTP/HTTPS può chiamare l'API REST di Cosmos DB. COSMOS DB offre anche librerie di programmazione per linguaggi di programmazione .NET, Node. js, JavaScript e Python. L'API REST e librerie di supportano delle query tramite SQL e .NET SDK supporta anche [l'esecuzione di query LINQ](sql-query-linq-to-sql.md).

Negli esempi seguenti viene illustrato come creare una query e inviarla a fronte di un account di database di Cosmos DB.

## <a id="REST-API"></a>API REST

Cosmos DB offre un modello di programmazione aperto RESTful su HTTP. Il modello di risorsa è costituito da un set di risorse in un account di database che effettua il provisioning una sottoscrizione di Azure. L'account del database è costituito da un set di *database*, ognuna delle quali può contenere più *contenitori*, che a sua volta contenere *elementi*, funzioni definite dall'utente e altri tipi di risorse. Ogni risorsa di Cosmos DB è indirizzabile tramite un URI logico e stabile. Un set di risorse viene chiamato un *feed*. 

È il modello di interazione di base con queste risorse Usa i verbi HTTP `GET`, `PUT`, `POST`, e `DELETE`, con le interpretazioni standard. Usare `POST` per creare una nuova risorsa, eseguire una stored procedure oppure eseguire una query di Cosmos DB. Le query sono sempre operazioni di sola lettura senza nessun effetto collaterale.

Gli esempi seguenti illustrano un `POST` per una query dell'API SQL rispetto agli elementi di esempio. La query include un semplice filtro nel codice JSON `name` proprietà. Il `x-ms-documentdb-isquery` e Content-Type: `application/query+json` intestazioni indicano che l'operazione è una query. Sostituire `mysqlapicosmosdb.documents.azure.com:443` con l'URI per l'account Cosmos DB.

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

La query successiva e più complessa restituisce più risultati da un join:

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

Se i risultati della query non possono essere contenuta in un'unica pagina, l'API REST restituisce un token di continuazione attraverso il `x-ms-continuation-token` intestazione della risposta. I client possono impaginare i risultati includendo l'intestazione nei risultati successivi. È anche possibile controllare il numero di risultati per pagina tramite il `x-ms-max-item-count` intestazione dei numeri.

Se una query contiene una funzione di aggregazione, ad esempio COUNT, la pagina di query può restituire un valore parzialmente aggregato su solo una pagina di risultati. I client devono eseguire un'aggregazione di secondo livello su questi risultati per produrre i risultati finali. Ad esempio sommare i conteggi restituiti nelle singole pagine per ottenere il conteggio totale.

Per gestire i criteri di coerenza dei dati per le query, usare il `x-ms-consistency-level` intestazione come in tutte le richieste API REST. La coerenza di sessione richiede anche la versione più recente di ripetizione `x-ms-session-token` intestazione cookie nella richiesta di query. I criteri di indicizzazione del contenitore sulla quale è stata eseguita la query possono influenzare anche la coerenza dei risultati della query. Con il valore predefinito delle impostazioni di criteri per i contenitori di indicizzazione, l'indice è sempre aggiornato con il contenuto di elemento e i risultati della query corrispondono alla coerenza scelta per i dati. Per altre informazioni, vedere [livelli di coerenza di Azure Cosmos DB] [-livelli di coerenza].

Se i criteri di indicizzazione configurati per il contenitore non possono supportare la query specificata, il server di Azure Cosmos DB restituisce 400 "richiesta non valida". Questo messaggio di errore restituisce per le query con i percorsi esplicitamente esclusi dall'indicizzazione. È possibile specificare il `x-ms-documentdb-query-enable-scan` intestazione per consentire alla query eseguire una scansione quando un indice non è disponibile.

È possibile ottenere metriche dettagliate sull'esecuzione di query impostando il `x-ms-documentdb-populatequerymetrics` intestazione `true`. Per altre informazioni, vedere [metriche di query SQL per Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

.NET SDK supporta l'esecuzione di query LINQ ed SQL. Nell'esempio seguente viene illustrato come eseguire la query del filtro precedente con .NET:

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

Nell'esempio seguente vengono confrontate due proprietà per verificarne l'uguaglianza all'interno di ogni elemento e proiezioni anonime.

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

L'esempio seguente vengono illustrati i join, esplicito tramite LINQ `SelectMany`.

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

Il client .NET esegue automaticamente l'iterazione attraverso tutte le pagine dei risultati della query nella `foreach` si blocca, come illustrato nell'esempio precedente. Le opzioni di query introdotte nel [API REST](#REST-API) sezione sono disponibili anche in .NET SDK, usando la `FeedOptions` e `FeedResponse` le classi nel `CreateDocumentQuery` (metodo). È possibile controllare il numero di pagine tramite la `MaxItemCount` impostazione.

È possibile controllare in modo esplicito il paging creando `IDocumentQueryable` usando il `IQueryable` oggetto, quindi leggendo il `ResponseContinuationToken` i valori e passarli nuovamente come `RequestContinuationToken` in `FeedOptions`. È possibile impostare `EnableScanInQuery` per abilitare l'analisi quando la query non è supportata da criteri di indicizzazione configurati. Per i contenitori partizionati, è possibile usare `PartitionKey` per eseguire la query a fronte di una partizione singola, anche se Azure Cosmos DB può estrazione automatica dal testo della query. È possibile usare `EnableCrossPartitionQuery` per eseguire query su più partizioni.

Per altri esempi di .NET con le query, vedere la [esempi di Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet) in GitHub.

## <a id="JavaScript-server-side-API"></a>API lato server JavaScript

Azure Cosmos DB offre un modello di programmazione per [l'esecuzione di JavaScript applicazione basata su](stored-procedures-triggers-udfs.md) per la logica direttamente nei contenitori, utilizzando stored procedure e trigger. La logica JavaScript registrata a livello di contenitore può quindi rilasciare operazioni di database per gli elementi del contenitore specificato, incapsulati nelle transazioni ACID ambient.

Nell'esempio seguente viene illustrato come utilizzare `queryDocuments` nel server di JavaScript API per eseguire query dall'interno di stored procedure e trigger:

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
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Livelli di coerenza di Azure Cosmos DB](consistency-levels.md)
