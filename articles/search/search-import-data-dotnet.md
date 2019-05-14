---
title: Caricare dati in un indice di Ricerca di Azure in C# (.NET SDK) - Ricerca di Azure
description: Informazioni su come caricare i dati in un indice di ricerca full-text in Ricerca di Azure usando il codice di esempio di C# e .NET SDK.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: d2d54d1425bbb67a3f5ba1b6081a9f74ff87f4d6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286908"
---
# <a name="quickstart-2---load-data-to-an-azure-search-index-using-c"></a>Avvio rapido: 2 - Caricare dati in un indice di Ricerca di Azure con C#

Questo articolo illustra come importare dati in un [indice di Ricerca di Azure](search-what-is-an-index.md) usando C# e [.NET SDK](https://aka.ms/search-sdk). Per effettuare il push di documenti nell'indice, eseguire le attività seguenti:

> [!div class="checklist"]
> * Creare un oggetto [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) da connettere a un indice di ricerca.
> * Creare un oggetto [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) contenente i documenti da aggiungere, modificare o eliminare.
> * Chiamare il metodo `Documents.Index` in `SearchIndexClient` per caricare documenti in un indice.

## <a name="prerequisites"></a>Prerequisiti

[Creare un indice di Ricerca di Azure](search-create-index-dotnet.md) e un oggetto `SearchServiceClient`, come illustrato in ["Creare un client"](search-create-index-dotnet.md#CreateSearchServiceClient).


## <a name="create-a-client"></a>Creare un client
Per importare i dati, è necessaria un'istanza della classe `SearchIndexClient`. Per creare questa classe sono disponibili diversi approcci, tra cui l'uso dell'istanza di `SearchServiceClient` già creata. 

Come illustra l'esempio seguente, è possibile usare l'istanza di `SearchServiceClient` e chiamare il relativo metodo `Indexes.GetClient`. Questo frammento di codice ottiene un oggetto `SearchIndexClient` per l'indice denominato "hotels" da un oggetto `SearchServiceClient` denominato `serviceClient`.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

`SearchIndexClient` include una proprietà `Documents`. Questa proprietà fornisce tutti i metodi necessari per aggiungere, modificare, eliminare o eseguire query sui documenti nell'indice.

> [!NOTE]
> In una tipica applicazione di ricerca, le operazioni di query e indicizzazione vengono gestite separatamente. Anche se è conveniente usare `Indexes.GetClient` perché è possibile riutilizzare oggetti come `SearchCredentials`, un approccio più efficace implica la creazione diretta di `SearchIndexClient`, in modo da passare una chiave di query invece di una chiave di amministratore. Questo procedimento è coerente con il [principio dei privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege) e consente di rendere più sicura l'applicazione. Verrà creato un oggetto `SearchIndexClient` nell'esercizio successivo. Per altre informazioni sulle chiavi, vedere [Creare e gestire chiavi API per un servizio Ricerca di Azure](search-security-api-keys.md).
> 
> 

<a name="construct-indexbatch"></a>

## <a name="construct-indexbatch"></a>Creare IndexBatch

Per importare i dati usando .NET SDK, è necessario inserirli in un pacchetto in un oggetto `IndexBatch`. Un oggetto `IndexBatch` incapsula una raccolta di oggetti `IndexAction`, ognuno dei quali contiene un documento e una proprietà che indica a Ricerca di Azure quale azione eseguire sul documento (upload, merge, delete e mergeOrUpload). Per altre informazioni sulle azioni di indicizzazione, vedere [Azioni di indicizzazione: upload, merge, mergeOrUpload, delete](search-what-is-data-import.md#indexing-actions).

Dopo aver appreso quali azioni eseguire sui documenti, è possibile creare `IndexBatch`. L'esempio seguente illustra come creare un batch con alcune azioni. Si noti che questo esempio usa una classe personalizzata denominata `Hotel` che esegue il mapping a un documento nell'indice "hotels".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

In questo caso vengono usate `Upload`, `MergeOrUpload` e `Delete` come azioni di ricerca, come specificato dai metodi chiamati nella classe `IndexAction`.

Si supponga che l'indice "hotels" di esempio sia già popolato con alcuni documenti. Si noti che non è stato necessario specificare tutti i possibili campi dei documenti quando si usa `MergeOrUpload` e come viene specificata solo la chiave del documento (`HotelId`) quando si usa `Delete`.

Si noti anche che è possibile includere solo fino a 1000 documenti in una singola richiesta di indicizzazione.

> [!NOTE]
> In questo esempio vengono applicate azioni diverse a documenti diversi. Per eseguire le stesse azioni in tutti i documenti nel batch, invece di chiamare `IndexBatch.New` si possono usare gli altri metodi statici di `IndexBatch`. Ad esempio, è possibile creare batch chiamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` o `IndexBatch.Delete`. Questi metodi accettano una raccolta di documenti (oggetti di tipo `Hotel` in questo esempio) invece di oggetti `IndexAction`.
> 
> 

## <a name="call-documentsindex"></a>Chiamare Documents.Index
Dopo aver creato un oggetto `IndexBatch` inizializzato, è possibile inviarlo all'indice chiamando `Documents.Index` sull'oggetto `SearchIndexClient`. L'esempio seguente mostra come chiamare `Index`e illustra alcune operazioni aggiuntive che è necessario eseguire:

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Si noti la presenza di `try`/`Index` attorno alla chiamata al metodo `catch`. Il blocco catch gestisce un caso di errore importante per l'indicizzazione. Se il servizio Ricerca di Azure non riesce a indicizzare alcuni dei documenti nel batch, viene generato un `IndexBatchException` da `Documents.Index`. Questa situazione può verificarsi se l'indicizzazione dei documenti avviene mentre il servizio è sovraccarico. **Si consiglia di gestire in modo esplicito questo caso nel codice.**  È possibile ritardare e quindi ritentare l'indicizzazione di documenti, accedere e continuare come nell'esempio,  oppure eseguire altre attività a seconda dei requisiti di coerenza di dati dell'applicazione.

Infine, il codice nell'esempio precedente prevede un ritardo di due secondi. L'indicizzazione avviene in modo asincrono nel servizio Ricerca di Azure, pertanto l'applicazione di esempio deve attendere un breve periodo per garantire che i documenti siano disponibili per la ricerca. Ritardi come questi in genere sono necessari solo in applicazioni di esempio, test e demo.

Per altre informazioni sull'elaborazione di documenti, vedere ["Modalità di gestione dei documenti in .NET SDK"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).


## <a name="next-steps"></a>Passaggi successivi
Dopo aver popolato l'indice di Ricerca di Azure, il passaggio successivo consiste nell'eseguire query per la ricerca di documenti. 

> [!div class="nextstepaction"]
> [Eseguire query in un indice di Ricerca di Azure in C#](search-query-dotnet.md)
