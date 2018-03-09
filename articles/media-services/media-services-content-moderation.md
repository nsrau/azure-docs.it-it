---
title: Usare Azure Media Content Moderator per rilevare possibili contenuti pornografici e per adulti | Microsoft Docs
description: La moderazione video consente di rilevare nei video contenuti potenzialmente pornografici e per adulti.
services: media-services
documentationcenter: 
author: sanjeev3
manager: mikemcca
editor: 
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/06/2018
ms.author: sajagtap
ms.openlocfilehash: 1b473a6aef87e5f4c75be2becbf814ecaaab6f3a
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Usare Azure Media Content Moderator per rilevare eventuali contenuti pornografici e per adulti

## <a name="overview"></a>Panoramica
Il processore di contenuti multimediali **Azure Content Moderator** consente di usare funzionalità di moderazione automatica per i video. Ad esempio, è possibile rilevare nei video contenuti pornografici e per adulti ed eseguire la revisione dei contenuti contrassegnati in modalità manuale.

Il processore di contenuti multimediali **Azure Media Content Moderator** è attualmente disponibile in anteprima.

Questo articolo illustra nel dettaglio **Azure Media Content Moderator** e spiega come usare questa funzionalità con Media Services SDK per .NET.

## <a name="content-moderator-input-files"></a>File di input di Content Moderator
File video. Attualmente sono supportati i formati seguenti: MP4, MOV e WMV.

## <a name="content-moderator-output-files"></a>File di output di Content Moderator
L'output moderato in formato JSON include scatti e fotogrammi chiave rilevati automaticamente. I fotogrammi chiave vengono restituiti con punteggi di attendibilità per eventuali contenuti pornografici o per adulti. Includono anche un flag booleano che indica se è consigliabile una revisione. Il flag di revisione consigliata viene assegnato in base ai valori delle soglie interne relative ai punteggi di contenuti pornografici o per adulti.

## <a name="elements-of-the-output-json-file"></a>Elementi del file di output JSON

Il processo genera un file di output JSON contenente i metadati relativi agli scatti e ai fotogrammi chiave rilevati e indica se contengono contenuti pornografici o per adulti.

L'output JSON include gli elementi seguenti:

### <a name="root-json-elements"></a>Elementi JSON radice

| Elemento | DESCRIZIONE |
| --- | --- |
| version |Versione di Content Moderator. |
| timescale |"Scatti" al secondo del video. |
| offset |Differenza di orario dei timestamp. Nella versione 1.0 delle API Video, questo valore è sempre 0, ma può cambiare in futuro. |
| framerate |Fotogrammi al secondo del video. |
| width |Larghezza del fotogramma video di output, espressa in pixel.|
| height |Altezza del fotogramma video di output, espressa in pixel.|
| totalDuration |Durata del video di input, espressa in "tick". |
| [fragments](#fragments-json-elements) |I metadati sono suddivisi in segmenti diversi, detti frammenti. Ogni frammento è uno scatto rilevato automaticamente con un inizio, una durata, un numero di intervallo e uno o più eventi. |

### <a name="fragments-json-elements"></a>Elementi JSON dei frammenti

|Elemento|DESCRIZIONE|
|---|---|
| start |Ora di inizio del primo evento in "tick". |
| duration |Lunghezza del frammento in "tick". |
| interval |Intervallo di ogni voce di evento all'interno del frammento in "tick". |
| [eventi](#events-json-elements) |Ogni evento rappresenta una clip e ogni clip contiene fotogrammi chiave rilevati e monitorati nel periodo di tempo specificato. È una matrice di eventi. La matrice esterna rappresenta un intervallo di tempo. La matrice interna è costituita da 0 o più eventi che si sono verificati in un determinato momento.|

### <a name="events-json-elements"></a>Elementi JSON degli eventi

|Elemento|DESCRIZIONE|
|---|---|
| reviewRecommended | `true` o `false` a seconda che le soglie interne vengano superate da **adultScore** o **racyScore**. |
| adultScore | Punteggio di attendibilità per eventuali contenuti per adulti, su una scala compresa tra 0,00 e 0,99. |
| racyScore | Punteggio di attendibilità per eventuali contenuti pornografici, su una scala compresa tra 0,00 e 0,99. |
| index | Indice del fotogramma su una scala dal primo all'ultimo indice di fotogramma. |
| timestamp | Percorso del fotogramma in "tick". |
| shotIndex | Indice dello scatto padre. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Guida introduttiva a Content Moderation e output di esempio

### <a name="task-configuration-preset"></a>Configurazione delle attività (set di impostazioni)
Quando si crea un'attività con **Azure Media Content Moderator**, è necessario specificare un set di impostazioni di configurazione. Il set di impostazioni di configurazione seguente può essere usato soltanto per la moderazione dei contenuti.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Esempio di codice .NET

L'esempio di codice .NET seguente usa Media Services .NET SDK per eseguire un processo di Content Moderator. Accetta come input un asset di Servizi multimediali contenente il video da moderare.
Vedere [Content Moderator Video Quickstart](../cognitive-services/Content-Moderator/video-moderation-api.md) (Guida introduttiva ai video di Content Moderator) per il codice sorgente completo e il progetto di Visual Studio.


```csharp
    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }
```

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Collegamenti correlati
[Panoramica di Analisi servizi multimediali di Azure](media-services-analytics-overview.md)

[Demo di Analisi servizi multimediali di Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [soluzione di moderazione e revisione di video](../cognitive-services/Content-Moderator/video-moderation-human-review.md) Content Moderator.

Per ottenere il codice sorgente completo e il progetto di Visual Studio, vedere la [guida introduttiva alla moderazione di video](../cognitive-services/Content-Moderator/video-moderation-api.md). 

Informazioni su come generare [revisioni di video](../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) dall'output moderato e [trascrizioni moderate](../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) in .NET.

Guardare il [tutorial dettagliato sulla moderazione e la revisione di video](../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md) in .NET. 
