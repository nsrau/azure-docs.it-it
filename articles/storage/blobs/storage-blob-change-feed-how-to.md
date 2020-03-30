---
title: Feed di modifiche del processo in Archiviazione BLOB di Azure (anteprima)Process change feed in Azure Blob Storage (Preview) Documenti Microsoft
description: Informazioni su come elaborare i log dei feed di modifiche in un'applicazione client .NET
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74111864"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Feed di modifiche del processo in Archiviazione BLOB di Azure (anteprima)Process change feed in Azure Blob Storage (Preview)

Il feed modifiche fornisce i log delle transazioni di tutte le modifiche apportate ai BLOB e ai metadati BLOB nell'account di archiviazione. Questo articolo illustra come leggere i record del feed di modifiche usando la libreria del processore del feed di modifiche BLOB.

Per altre informazioni sul feed di modifiche, vedere [Change feed in Azure Blob Storage (Preview)](storage-blob-change-feed.md).

> [!NOTE]
> Il feed di modifiche è disponibile nell'anteprima pubblica ed è disponibile nelle regioni **westcentralus** e **westus2.** Per altre informazioni su questa funzionalità insieme a problemi noti e limitazioni, vedere [Modificare il supporto dei feed in Archiviazione BLOB](storage-blob-change-feed.md)di Azure.To learn more about this feature along with known issues and limitations, see Change feed support in Azure Blob Storage . La libreria del processore del feed di modifiche è soggetta a modifiche da ora a quando questa libreria diventa generalmente disponibile.

## <a name="get-the-blob-change-feed-processor-library"></a>Ottenere la libreria del processore del feed di modifiche BLOBGet the blob change change feed processor library

1. In Visual Studio aggiungere `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` l'URL alle origini del pacchetto NuGet.In Visual Studio, add the URL to your NuGet package sources. 

   Per informazioni, vedere [origini dei pacchetti](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. In Gestione pacchetti NuGet individuare il pacchetto **Microsoft.Azure.Storage.Changefeed** e installarlo nel progetto. 

   Per ulteriori informazioni, consultate [Trovare e installare un pacchetto.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)

## <a name="connect-to-the-storage-account"></a>Effettuare la connessione all'account di archiviazione

Analizzare la stringa di connessione chiamando il [metodo CloudStorageAccount.TryParse.Parse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) the connection string by calling the CloudStorageAccount.TryParse method. 

Creare quindi un oggetto che rappresenta l'archiviazione BLOB nell'account di archiviazione chiamando il metodo CloudStorageAccount.CreateCloudBlobClient.Then, create an object that represents Blob Storage in your storage account by calling the [CloudStorageAccount.CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient) method.

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>Inizializzare il feed di modifiche

Aggiungere le istruzioni using seguenti all'inizio del file di codice. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Quindi, creare un'istanza della classe **ChangeFeed** chiamando il **metodo GetContainerReference.** Passare il nome del contenitore del feed di modifiche.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>Lettura dei record

> [!NOTE]
> Il feed di modifiche è un'entità non modificabile e di sola lettura nell'account di archiviazione. Qualsiasi numero di applicazioni può leggere ed elaborare il feed di modifiche contemporaneamente e in modo indipendente a proprio piacimento. I record non vengono rimossi dal feed di modifiche quando un'applicazione li legge. Lo stato di lettura o iterazione di ogni lettore consumer è indipendente e gestito solo dall'applicazione.

Il modo più semplice per leggere i record consiste nel creare un'istanza della classe **ChangeFeedReader.** 

Questo esempio scorre tutti i record nel feed di modifiche e quindi stampa nella console alcuni valori di ogni record. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>Ripresa della lettura dei record da una posizione salvata

Puoi scegliere di salvare la posizione di lettura nel feed delle modifiche e riprendere l'iterare i record in un momento futuro. È possibile salvare lo stato dell'iterazione del feed di modifiche in qualsiasi momento utilizzando il metodo **ChangeFeedReader.SerializeState().** Lo stato è una **stringa** e l'applicazione può salvare tale stato in base alla progettazione dell'applicazione (ad esempio: in un database o un file).

```csharp
    string currentReadState = processor.SerializeState();
```

È possibile continuare a scorrere i record dall'ultimo stato creando **ChangeFeedReader** usando il **createChangeFeedReaderFromPointerAsync** metodo.

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>Elaborazione del flusso dei record

È possibile scegliere di elaborare i record del feed di modifiche non appena arrivano. Vedere [Specifiche](storage-blob-change-feed.md#specifications).

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>Lettura di record in un intervallo di tempo

Il feed di modifiche è organizzato in segmenti orari in base all'ora dell'evento di modifica. Vedere [Specifiche](storage-blob-change-feed.md#specifications). È possibile leggere i record dai segmenti di feed di modifiche che rientrano in un intervallo di tempo specifico.

In questo esempio vengono ottenuti gli orari di inizio di tutti i segmenti. Quindi, scorre l'elenco fino a quando l'ora di inizio è oltre l'ora dell'ultimo segmento consumabile o oltre l'ora finale dell'intervallo desiderato. 

### <a name="selecting-segments-for-a-time-range"></a>Selezione dei segmenti per un intervallo di tempo

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>Lettura di record in un segmento

È possibile leggere i record da singoli segmenti o intervalli di segmenti.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>Leggere i record a partire da un orario

È possibile leggere i record del feed di modifiche da un segmento iniziale fino alla fine. Analogamente alla lettura dei record all'interno di un intervallo di tempo, è possibile elencare i segmenti e scegliere un segmento da cui iniziare l'iterazione.

In questo esempio viene ottenuto il [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) del primo segmento da elaborare.

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

In questo esempio vengono processi i record del feed di modifica a partire da [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) di un segmento iniziale.

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> Un segmento di può avere registri del feed di modifiche in uno o più *chunkFilePath*. In caso di più *chunkFilePath* il sistema ha partizionato internamente i record in più partizioni per gestire la velocità effettiva di pubblicazione. È garantito che ogni partizione del segmento conterrà modifiche per BLOB che si escludono a vicenda e può essere elaborata in modo indipendente senza violare l'ordine. È possibile utilizzare la classe **ChangeFeedSegmentShardReader** per scorrere i record a livello di partizione, se questo è più efficiente per lo scenario.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sui log dei feed di modifiche. Vedere Modificare il feed in Archiviazione BLOB di [Azure (anteprima)See Change feed in Azure Blob Storage (Preview)](storage-blob-change-feed.md)
