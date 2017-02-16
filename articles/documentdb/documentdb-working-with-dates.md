---
title: Uso delle date in Azure DocumentDB| Microsoft Docs
description: Informazioni sull&quot;uso delle date in Azure DocumentDB.
services: documentdb
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: e587772f-ce9f-498c-a017-a51e7265bb23
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 3c07980e09362eabdfc80483213b62912e64ecf6
ms.openlocfilehash: b57dc0b699d7f7e6d539d6622b3d2769adf4257d


---
# <a name="working-with-dates-in-azure-documentdb"></a>Uso delle date in Azure DocumentDB
DocumentDB offre flessibilità dello schema e un'indicizzazione avanzata tramite un modello di dati [JSON](http://www.json.org) nativo. Tutte le risorse di DocumentDB, inclusi database, raccolte, documenti e procedure archiviate sono modellate e archiviate come documenti JSON. Come requisito per essere portatile, JSON, insieme a DocumentDB, supporta solo un piccolo set di tipi di base: String, Number, Boolean, Array, Object e Null. Tuttavia, JSON è flessibile e consente a sviluppatori e strutture di rappresentare tipi più complessi tramite l'uso di queste primitive, componendole come oggetti o matrici. 

Oltre ai tipi di base, molte applicazioni hanno bisogno del tipo [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) per rappresentare date e timestamp. In questo articolo viene descritto come gli sviluppatori possono archiviare, recuperare ed eseguire query di date in DocumentDB con l in DocumentDB con .NET SDK.

## <a name="storing-datetimes"></a>Archiviazione di valori DateTime
Per impostazione predefinita, [DocumentDB SDK](documentdb-sdk-dotnet.md) serializza i valori DateTime come stringhe [ISO 8601](http://www.iso.org/iso/catalogue_detail?csnumber=40874). La maggior parte delle applicazioni può usare la rappresentazione predefinita della stringa per il valore DateTime, per i motivi seguenti:

* Le stringhe possono essere confrontate e l'ordinamento relativo dei valori DateTime viene conservato quando questi vengono trasformati in stringhe. 
* Questo approccio non richiede codici o attributi personalizzati per la conversione JSON.
* Le date così come archiviate in JSON sono leggibili dall'utente.
* Questo approccio può sfruttare l'indicizzazione di DocumentDB per avere query più veloci.

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

Il documento viene archiviato in DocumentDB come illustrato di seguito:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

In alternativa, è possibile archiviare valori DateTime come timestamp Unix, ovvero come un numero che rappresenti i secondi trascorsi a partire dal 1° gennaio 1970. La proprietà Timestamp interna a DocumentDB (`_ts`) segue questo approccio. È possibile usare la classe [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) per serializzare come numeri i valori DateTime. 

## <a name="indexing-datetimes-for-range-queries"></a>Indicizzazione dei valori DateTime per le query di intervallo
Le query di intervallo sono comuni con i valori DateTime. Ad esempio, sono necessarie per trovare tutti gli ordini creati dal giorno precedente o tutti gli ordini spediti negli ultimi cinque minuti. Per eseguire queste query in modo efficiente, è necessario configurare la raccolta per l'indicizzazione di intervallo nelle stringhe.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

È possibile trovare ulteriori informazioni su come configurare i criteri di indicizzazione nella sezione [Criteri di indicizzazione di DocumentDB](documentdb-indexing-policies.md).

## <a name="querying-datetimes-in-linq"></a>Esecuzione di query per i valori DateTime in LINQ
.NET SDK di DocumentDB supporta automaticamente le query per i dati archiviati in DocumentDB tramite LINQ. Ad esempio, il frammento seguente mostra una query LINQ che filtra gli ordini inviati negli ultimi tre giorni.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on DocumentDB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

È possibile trovare ulteriori informazioni sul linguaggio di query SQL di DocumentDB e sul provider LINQ nella sezione [Esecuzione di query in DocumentDB](documentdb-sql-query.md).

In questo articolo è stato descritto come archiviare, indicizzare ed eseguire query per valori DateTime in DocumentDB.

## <a name="next-steps"></a>Passaggi successivi
* Scaricare ed eseguire gli [Esempi di codice su GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Altre informazioni sulle [query di DocumentDB](documentdb-sql-query.md)
* Ulteriori informazioni sui [criteri di indicizzazione di DocumentDB](documentdb-indexing-policies.md)



<!--HONumber=Dec16_HO4-->


