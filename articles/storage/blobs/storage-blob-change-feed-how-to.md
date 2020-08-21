---
title: Elaborare il feed delle modifiche nell'archiviazione BLOB di Azure (anteprima) | Microsoft Docs
description: Informazioni su come elaborare i log dei feed di modifiche in un'applicazione client .NET
author: normesta
ms.author: normesta
ms.date: 06/18/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: dedf1174e00f5bb75822fb720a592af86121ec2d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691429"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Elaborare il feed delle modifiche nell'archiviazione BLOB di Azure (anteprima)

Il feed di modifiche fornisce i log delle transazioni di tutte le modifiche apportate ai BLOB e ai metadati del BLOB nell'account di archiviazione. Questo articolo illustra come leggere i record del feed di modifiche usando la libreria del processore dei feed delle modifiche BLOB.

Per altre informazioni sul feed delle modifiche, vedere [feed delle modifiche nell'archivio BLOB di Azure (anteprima)](storage-blob-change-feed.md).

> [!NOTE]
> Il feed delle modifiche è in anteprima pubblica ed è disponibile nelle aree **westcentralus** e **westus2** . Per altre informazioni su questa funzionalità insieme ai problemi noti e alle limitazioni, vedere [supporto del feed di modifiche nell'archivio BLOB di Azure](storage-blob-change-feed.md). La libreria del processore dei feed delle modifiche è soggetta a modifiche tra ora e quando la libreria diventa disponibile a livello generale.

## <a name="get-the-blob-change-feed-processor-library"></a>Ottenere la libreria del processore dei feed delle modifiche BLOB

1. Aprire una finestra di comando (ad esempio: Windows PowerShell).
2. Dalla directory del progetto, installare il pacchetto NuGet **Azure. storage. Blobs. offre** .

```console
dotnet add package Azure.Storage.Blobs.ChangeFeed --source https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json --version 12.0.0-dev.20200604.2
```
## <a name="read-records"></a>Leggere record

> [!NOTE]
> Il feed delle modifiche è un'entità non modificabile e di sola lettura nell'account di archiviazione. Un numero qualsiasi di applicazioni può leggere ed elaborare il feed di modifiche simultaneamente e in modo indipendente con la loro convenienza. I record non vengono rimossi dal feed di modifiche quando vengono letti da un'applicazione. Lo stato di lettura o di iterazione di ogni lettore che lo utilizza è indipendente e gestito dall'applicazione.

Questo esempio scorre tutti i record nel feed delle modifiche, li aggiunge a un elenco e quindi restituisce tale elenco al chiamante.
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

In questo esempio vengono stampati nella console alcuni valori di ogni record dell'elenco. 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>Riprendere la lettura dei record da una posizione salvata

È possibile scegliere di salvare la posizione di lettura nel feed delle modifiche e quindi riprendere l'iterazione nei record in un momento successivo. È possibile salvare la posizione di lettura ottenendo il cursore del feed delle modifiche. Il cursore è una **stringa** e l'applicazione può salvare tale stringa in qualsiasi modo appropriato per la progettazione dell'applicazione, ad esempio in un file o un database.

Questo esempio scorre tutti i record nel feed di modifiche, li aggiunge a un elenco e salva il cursore. L'elenco e il cursore vengono restituiti al chiamante. 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>Elaborazione di flussi di record

È possibile scegliere di elaborare i record del feed di modifiche man mano che arrivano. Vedere le [specifiche](storage-blob-change-feed.md#specifications). Si consiglia di eseguire il polling delle modifiche ogni ora.

In questo esempio viene periodicamente eseguito il polling delle modifiche.  Se sono presenti record delle modifiche, questo codice elabora i record e salva il cursore del feed delle modifiche. In questo modo, se il processo viene arrestato e quindi riavviato, l'applicazione può utilizzare il cursore per riprendere l'elaborazione dei record in cui è stato interrotto. In questo esempio il cursore viene salvato in un file di configurazione dell'applicazione locale, ma l'applicazione può salvarla in qualsiasi forma che risulti più sensata per lo scenario in uso. 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>Lettura di record in un intervallo di tempo

È possibile leggere i record che rientrano in un intervallo di tempo specifico. Questo esempio Mostra come eseguire l'iterazione di tutti i record del feed di modifiche che rientrino tra il 3:00 e il 2 2017 marzo e le 2:00 del 7 2019 ottobre, li aggiunge a un elenco e quindi restituisce tale elenco al chiamante.

### <a name="selecting-segments-for-a-time-range"></a>Selezione dei segmenti per un intervallo di tempo

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2017, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 10, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

L'ora di inizio fornita viene arrotondata per difetto all'ora più vicina e l'ora di fine viene arrotondata per eccesso all'ora più vicina. È possibile che gli utenti visualizzino gli eventi che si sono verificati prima dell'ora di inizio e dopo l'ora di fine. È anche possibile che vengano visualizzati alcuni eventi che si verificano tra l'ora di inizio e di fine. Questo perché gli eventi potrebbero essere registrati durante l'ora precedente all'ora di inizio o nell'ora successiva all'ora di fine.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui log del feed di modifiche. Vedere il [feed delle modifiche nell'archivio BLOB di Azure (anteprima)](storage-blob-change-feed.md)
