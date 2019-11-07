---
title: Elaborare il feed delle modifiche nell'archiviazione BLOB di Azure (anteprima) | Microsoft Docs
description: Informazioni su come elaborare i log dei feed di modifiche in un'applicazione client .NET
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 90b7d79cd2a0e215af17856796bcdda2fbabb43f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693631"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Elaborare il feed delle modifiche nell'archiviazione BLOB di Azure (anteprima)

Il feed di modifiche fornisce i log delle transazioni di tutte le modifiche apportate ai BLOB e ai metadati del BLOB nell'account di archiviazione. Questo articolo illustra come leggere i record del feed di modifiche usando la libreria del processore dei feed delle modifiche BLOB.

Per altre informazioni sul feed delle modifiche, vedere [feed delle modifiche nell'archivio BLOB di Azure (anteprima)](storage-blob-change-feed.md).

> [!NOTE]
> Il feed delle modifiche è in anteprima pubblica ed è disponibile nelle aree **westcentralus** e **westus2** . Per altre informazioni su questa funzionalità insieme ai problemi noti e alle limitazioni, vedere [supporto del feed di modifiche nell'archivio BLOB di Azure](storage-blob-change-feed.md). La libreria del processore dei feed delle modifiche è soggetta a modifiche tra ora e quando la libreria diventa disponibile a livello generale.

## <a name="get-the-blob-change-feed-processor-library"></a>Ottenere la libreria del processore dei feed delle modifiche BLOB

1. In Visual Studio aggiungere l'URL `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` alle origini dei pacchetti NuGet. 

   Per informazioni, vedere [origini di pacchetti](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. In Gestione pacchetti NuGet trovare il pacchetto **Microsoft. Azure. storage. offre** e installarlo nel progetto. 

   Per informazioni, vedere [trovare e installare un pacchetto](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

## <a name="connect-to-the-storage-account"></a>Effettuare la connessione all'account di archiviazione

Analizzare la stringa di connessione chiamando il metodo [CloudStorageAccount. TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse) . 

Quindi, creare un oggetto che rappresenti l'archiviazione BLOB nell'account di archiviazione chiamando il metodo [CloudStorageAccount. CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet) .

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

## <a name="initialize-the-change-feed"></a>Inizializzare il feed delle modifiche

Aggiungere le istruzioni using seguenti all'inizio del file di codice. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Quindi, creare un'istanza della classe **offre** chiamando il metodo **GetContainerReference** . Passare il nome del contenitore del feed di modifiche.

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

## <a name="reading-records"></a>Lettura di record

> [!NOTE]
> Il feed delle modifiche è un'entità non modificabile e di sola lettura nell'account di archiviazione. Un numero qualsiasi di applicazioni può leggere ed elaborare il feed di modifiche simultaneamente e in modo indipendente con la loro convenienza. I record non vengono rimossi dal feed di modifiche quando vengono letti da un'applicazione. Lo stato di lettura o di iterazione di ogni lettore che lo utilizza è indipendente e gestito dall'applicazione.

Il modo più semplice per leggere i record consiste nel creare un'istanza della classe **ChangeFeedReader** . 

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

È possibile scegliere di salvare la posizione di lettura nel feed di modifiche e riprendere l'iterazione dei record in un momento successivo. È possibile salvare lo stato dell'iterazione del feed di modifiche in qualsiasi momento usando il metodo **ChangeFeedReader. SerializeState ()** . Lo stato è una **stringa** e l'applicazione può salvare lo stato in base alla progettazione dell'applicazione (ad esempio, in un database o in un file).

```csharp
    string currentReadState = processor.SerializeState();
```

È possibile continuare a scorrere i record dall'ultimo stato creando il **ChangeFeedReader** usando il metodo **CreateChangeFeedReaderFromPointerAsync** .

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

## <a name="stream-processing-of-records"></a>Elaborazione di flussi di record

È possibile scegliere di elaborare i record del feed di modifiche man mano che arrivano. Vedere le [specifiche](storage-blob-change-feed.md#specifications).

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

Il feed delle modifiche è organizzato in segmenti orari in base all'ora dell'evento di modifica. Vedere le [specifiche](storage-blob-change-feed.md#specifications). È possibile leggere i record dei segmenti del feed di modifiche che rientrano in un intervallo di tempo specifico.

Questo esempio Mostra come ottenere l'ora di inizio di tutti i segmenti. Quindi scorre l'elenco fino a quando l'ora di inizio non è successiva all'ora dell'ultimo segmento utilizzabile o oltre l'ora di fine dell'intervallo desiderato. 

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

## <a name="read-records-starting-from-a-time"></a>Lettura dei record a partire da un'ora

È possibile leggere i record del feed di modifiche da un segmento iniziale fino alla fine. Analogamente alla lettura di record in un intervallo di tempo, è possibile elencare i segmenti e scegliere un segmento da cui iniziare l'iterazione.

Questo esempio Mostra come ottenere l'oggetto [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) del primo segmento da elaborare.

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

Questo esempio elabora i record del feed di modifiche a partire da [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) di un segmento iniziale.

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
> Un segmento di può avere log del feed delle modifiche in uno o più *chunkFilePath*. Nel caso di più *chunkFilePath* , il sistema ha partizionato internamente i record in più partizioni per gestire la velocità effettiva di pubblicazione. Si garantisce che ogni partizione del segmento conterrà modifiche per i BLOB che si escludono a vicenda e possa essere elaborata in modo indipendente senza violare l'ordinamento. È possibile usare la classe **ChangeFeedSegmentShardReader** per scorrere i record a livello di partizione, se questa è la più efficiente per lo scenario.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui log del feed di modifiche. Vedere il [feed delle modifiche nell'archivio BLOB di Azure (anteprima)](storage-blob-change-feed.md)
