---
title: Uso delle date in Azure Cosmos DB | Microsoft Docs
description: Informazioni sull'uso delle date in Azure Cosmos DB.
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: e587772f-ce9f-498c-a017-a51e7265bb23
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: arramac
ms.openlocfilehash: 1a54884196e5b4ff5b16425e902abeb8d82aa8f1
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Uso delle date in Azure Cosmos DB
Azure Cosmos DB offre flessibilità dello schema e un'indicizzazione avanzata tramite un modello di dati [JSON](http://www.json.org) nativo. Tutte le risorse di Azure Cosmos DB, inclusi database, raccolte, documenti e procedure archiviate, sono modellate e archiviate come documenti JSON. Come requisito per essere portatile, JSON, insieme a Azure Cosmos DB, supporta solo un piccolo set di tipi di base: String, Number, Boolean, Array, Object e Null. Tuttavia, JSON è flessibile e consente a sviluppatori e strutture di rappresentare tipi più complessi tramite l'uso di queste primitive, componendole come oggetti o matrici. 

Oltre ai tipi di base, molte applicazioni hanno bisogno del tipo [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) per rappresentare date e timestamp. Questo articolo descrive come gli sviluppatori possono archiviare, recuperare ed eseguire query di date in Azure Cosmos DB usando .NET SDK.

## <a name="storing-datetimes"></a>Archiviazione di valori DateTime
Per impostazione predefinita, [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) serializza i valori DateTime come stringhe [ISO 8601](http://www.iso.org/iso/catalogue_detail?csnumber=40874). La maggior parte delle applicazioni può usare la rappresentazione predefinita della stringa per il valore DateTime, per i motivi seguenti:

* Le stringhe possono essere confrontate e l'ordinamento relativo dei valori DateTime viene conservato quando questi vengono trasformati in stringhe. 
* Questo approccio non richiede codici o attributi personalizzati per la conversione JSON.
* Le date così come archiviate in JSON sono leggibili dall'utente.
* Questo approccio può sfruttare l'indicizzazione di Azure Cosmos DB per query più veloci.

Ad esempio, il frammento seguente memorizza come documento un oggetto `Order` che contiene due proprietà DateTime, `ShipDate` e `OrderDate`, tramite .NET SDK:

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Il documento viene archiviato in Azure Cosmos DB come illustrato di seguito:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

In alternativa, è possibile archiviare valori DateTime come timestamp Unix, ovvero come un numero che rappresenti i secondi trascorsi a partire dal 1° gennaio 1970. La proprietà Timestamp interna di Azure Cosmos DB (`_ts`) segue questo approccio. È possibile usare la classe [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) per serializzare come numeri i valori DateTime. 

## <a name="indexing-datetimes-for-range-queries"></a>Indicizzazione dei valori DateTime per le query di intervallo
Le query di intervallo sono comuni con i valori DateTime. Ad esempio, sono necessarie per trovare tutti gli ordini creati dal giorno precedente o tutti gli ordini spediti negli ultimi cinque minuti. Per eseguire queste query in modo efficiente, è necessario configurare la raccolta per l'indicizzazione di intervallo nelle stringhe.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Per altre informazioni su come configurare i criteri di indicizzazione, vedere i [criteri di indicizzazione di Azure Cosmos DB](indexing-policies.md).

## <a name="querying-datetimes-in-linq"></a>Esecuzione di query per i valori DateTime in LINQ
il SDK di .NET SQL supporta automaticamente le query sui dati archiviati nel database di Azure Cosmos tramite LINQ. Ad esempio, il frammento seguente mostra una query LINQ che filtra gli ordini inviati negli ultimi tre giorni.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Per altre informazioni sul linguaggio di query SQL di Azure Cosmos DB e sul provider LINQ, vedere l'[esecuzione di query in Cosmos DB](sql-api-sql-query.md).

In questo articolo è stato descritto come archiviare, indicizzare ed eseguire query per valori DateTime in Azure Cosmos DB.

## <a name="next-steps"></a>Fasi successive
* Scaricare ed eseguire gli [Esempi di codice su GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Altre informazioni, vedere [query SQL](sql-api-sql-query.md)
* Altre informazioni sui [criteri di indicizzazione di Azure Cosmos DB](indexing-policies.md)
