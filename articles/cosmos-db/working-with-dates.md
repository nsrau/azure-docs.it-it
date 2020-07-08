---
title: Uso delle date in Azure Cosmos DB
description: Informazioni su come archiviare, indicizzare ed eseguire query sugli oggetti DataTime in Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 2f31ee7f7d60a3bf0ab56b9ed8aa7fd25774e06c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85412550"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Uso delle date in Azure Cosmos DB

Azure Cosmos DB offre flessibilità dello schema e un'indicizzazione avanzata tramite un modello di dati [JSON](https://www.json.org) nativo. Tutte le risorse di Azure Cosmos DB, inclusi database, contenitori, documenti e procedure archiviate, sono modellate e archiviate come documenti JSON. Come requisito per essere portatile, JSON, insieme a Azure Cosmos DB, supporta solo un piccolo set di tipi di base: String, Number, Boolean, Array, Object e Null. Tuttavia, JSON è flessibile e consente a sviluppatori e strutture di rappresentare tipi più complessi tramite l'uso di queste primitive, componendole come oggetti o matrici.

Oltre ai tipi di base, molte applicazioni hanno bisogno del tipo DateTime per rappresentare date e timestamp. Questo articolo descrive come gli sviluppatori possono archiviare, recuperare ed eseguire query di date in Azure Cosmos DB usando .NET SDK.

## <a name="storing-datetimes"></a>Archiviazione di valori DateTime

Azure Cosmos DB supporta i tipi JSON, ad esempio-String, Number, Boolean, null, array, Object. Non supporta direttamente un tipo DateTime. Attualmente, Azure Cosmos DB non supporta la localizzazione delle date. Quindi, è necessario archiviare i valori DateTime come stringhe. Il formato consigliato per le stringhe DateTime in Azure Cosmos DB è `yyyy-MM-ddTHH:mm:ss.fffffffZ` che segue lo standard ISO 8601 UTC. È consigliabile archiviare tutte le date nel Azure Cosmos DB come ora UTC. La conversione delle stringhe di data in questo formato consentirà di ordinare le date lessicografico. Se vengono archiviate date non UTC, la logica deve essere gestita sul lato client. Per convertire un valore DateTime locale nell'ora UTC, l'offset deve essere noto/archiviato come proprietà in JSON e il client può usare l'offset per calcolare il valore DateTime UTC.

Le query di intervallo con stringhe DateTime come filtri sono supportate solo se le stringhe DateTime sono tutte in formato UTC e la stessa lunghezza. In Azure Cosmos DB la funzione di sistema [GetCurrentDateTime](sql-query-getcurrentdatetime.md) restituirà il valore della stringa ISO 8601 per la data e l'ora UTC corrente nel formato: `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

La maggior parte delle applicazioni può usare la rappresentazione predefinita della stringa per il valore DateTime, per i motivi seguenti:

* Le stringhe possono essere confrontate e l'ordinamento relativo dei valori DateTime viene conservato quando questi vengono trasformati in stringhe.
* Questo approccio non richiede codici o attributi personalizzati per la conversione JSON.
* Le date così come archiviate in JSON sono leggibili dall'utente.
* Questo approccio può sfruttare l'indicizzazione di Azure Cosmos DB per query più veloci.

Ad esempio, il frammento seguente memorizza come documento un oggetto `Order` che contiene due proprietà DateTime, `ShipDate` e `OrderDate`, tramite .NET SDK:

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14),
            Total = 113.39
        });
```

Il documento viene archiviato in Azure Cosmos DB come illustrato di seguito:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

In alternativa, è possibile archiviare valori DateTime come timestamp Unix, ovvero come un numero che rappresenti i secondi trascorsi a partire dal 1° gennaio 1970. La proprietà Timestamp interna di Azure Cosmos DB (`_ts`) segue questo approccio. È possibile usare la classe [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) per serializzare come numeri i valori DateTime.

## <a name="querying-datetimes-in-linq"></a>Esecuzione di query per i valori DateTime in LINQ

SQL .NET SDK supporta automaticamente le query sui dati archiviati in Azure Cosmos DB tramite LINQ. Ad esempio, il frammento di codice seguente mostra una query LINQ che filtra gli ordini spediti negli ultimi tre giorni:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Convertito nell'istruzione SQL seguente ed eseguito in Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

Per ulteriori informazioni sul linguaggio di query SQL di Azure Cosmos DB e sul provider LINQ, vedere [esecuzione di query Cosmos DB in LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Indicizzazione dei valori DateTime per le query di intervallo

Le query sono comuni con i valori DateTime. Per eseguire queste query in modo efficiente, è necessario disporre di un indice definito su qualsiasi proprietà nel filtro della query.

Per altre informazioni su come configurare i criteri di indicizzazione, vedere i [criteri di indicizzazione di Azure Cosmos DB](index-policy.md). 

## <a name="next-steps"></a>Passaggi successivi

* Scaricare ed eseguire gli [Esempi di codice su GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Altre informazioni sulle [Query SQL](sql-query-getting-started.md)
* Altre informazioni sui [criteri di indicizzazione di Azure Cosmos DB](index-policy.md)
