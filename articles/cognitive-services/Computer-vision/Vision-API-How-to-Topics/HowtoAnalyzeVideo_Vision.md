---
title: Analizzare i video quasi in tempo reale - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire l'analisi quasi in tempo reale di fotogrammi acquisiti da un video in diretta streaming usando l'API Visione artificiale.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: f4410d9cab5677327d2950dfdc1a093140f31708
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102272"
---
# <a name="analyze-videos-in-near-real-time"></a>Analizzare i video quasi in tempo reale

Questo articolo illustra come eseguire l'analisi quasi in tempo reale di fotogrammi acquisiti da un video in diretta streaming usando l'API Visione artificiale. Gli elementi di base di tale analisi sono:

- Acquisizione di fotogrammi da un'origine video.
- Selezione dei fotogrammi da analizzare.
- Invio di tali fotogrammi all'API.
- Utilizzo di ogni risultato dell'analisi restituito dalla chiamata all'API.

Gli esempi di questo articolo sono scritti in C#. Per accedere al codice, passare alla pagina dell'[esempio di analisi di fotogrammi video](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) in GitHub.

## <a name="approaches-to-running-near-real-time-analysis"></a>Approcci all'esecuzione dell'analisi quasi in tempo reale

È possibile adottare vari approcci per risolvere il problema dell'esecuzione di analisi quasi in tempo reale di flussi video. Questo articolo ne descrive tre, con un livello di complessità crescente.

### <a name="design-an-infinite-loop"></a>Progettare un ciclo infinito

L'approccio più semplice all'analisi quasi in tempo reale è quello del ciclo infinito. In ogni iterazione di questo ciclo si acquisisce un fotogramma, lo si analizza e quindi si utilizza il risultato:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Se l'analisi deve consistere in un algoritmo leggero lato client, questo approccio è adeguato. Tuttavia, quando l'analisi viene eseguita nel cloud, la latenza risultante implica che una chiamata all'API potrebbe richiedere diversi secondi. In questo periodo, non vengono acquisite immagini e il thread essenzialmente non esegue alcuna operazione. La frequenza massima dei fotogrammi è limitata dalla latenza delle chiamate all'API.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Consentire l'esecuzione in parallelo delle chiamate all'API

Se l'uso di un ciclo con thread singolo semplice ha senso per un algoritmo leggero lato client, non è invece adatto in presenza della latenza insita nelle chiamate all'API cloud. Per risolvere questo problema, è possibile effettuare le chiamate all'API a esecuzione prolungata in parallelo con l'acquisizione dei fotogrammi. A questo scopo, in C# si usa il parallelismo basato su attività. È ad esempio possibile eseguire il codice seguente:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Con questo approccio, ogni analisi viene avviata in un'attività distinta. L'attività può essere eseguita in background mentre si continuano ad acquisire nuovi fotogrammi. Questo approccio evita di bloccare il thread principale mentre si aspetta che la chiamata all'API restituisca il risultato. Tuttavia, può presentare alcuni svantaggi:
* Alcune garanzie fornite dalla versione semplice vanno perse. Ossia, è possibile che vengano eseguite più chiamate all'API in parallelo e che i risultati vengano restituiti nell'ordine non corretto. 
* Inoltre, più thread potrebbero accedere alla funzione ConsumeResult() simultaneamente, il che potrebbe rivelarsi pericoloso se la funzione non è thread-safe. 
* Infine, questo codice semplice non tiene traccia delle attività che vengono create, quindi le eccezioni scompaiono in modo invisibile all'utente. Di conseguenza, è necessario aggiungere un thread "consumer" che tenga traccia delle attività di analisi, generi eccezioni, interrompa le attività a esecuzione prolungata e assicuri che i risultati vengano utilizzati nell'ordine corretto, uno alla volta.

### <a name="design-a-producer-consumer-system"></a>Progettare un sistema producer-consumer

L'approccio finale prevede la progettazione di un sistema "producer-consumer", per cui viene creato un thread producer simile al ciclo infinito menzionato in precedenza. Tuttavia, invece di utilizzare i risultati dell'analisi non appena diventano disponibili, il producer inserisce semplicemente le attività in una coda per tenerne traccia.

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

Viene anche creato un thread consumer, che preleva le attività dalla coda, attende che vengano completate e visualizza il risultato oppure solleva l'eccezione che è stata generata. L'uso della coda garantisce che i risultati vengano utilizzati uno alla volta, nell'ordine corretto, senza limitare la frequenza dei fotogrammi massima del sistema.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implement-the-solution"></a>Implementare la soluzione

### <a name="get-started-quickly"></a>Operatività immediata

Per rendere l'app operativa il più rapidamente possibile, è stato implementato il sistema descritto nella sezione precedente. Secondo le intenzioni, questo sistema è sufficientemente flessibile da supportare molti scenari, pur essendo facile da usare. Per accedere al codice, passare alla pagina dell'[esempio di analisi di fotogrammi video](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) in GitHub.

La libreria contiene la classe `FrameGrabber`, che implementa il sistema producer-consumer descritto in precedenza per elaborare i fotogrammi video ripresi da una webcam. Gli utenti possono specificare il formato esatto della chiamata all'API e la classe usa gli eventi per indicare al codice chiamante quando viene acquisito un nuovo fotogramma o quando è disponibile un nuovo risultato dell'analisi.

Per illustrare alcune delle possibilità, sono disponibili due app di esempio che usano la libreria. 

La prima app di esempio è una semplice app console che acquisisce i fotogrammi dalla webcam predefinita e li invia all'API Viso per il rilevamento del viso. Una versione semplificata dell'app è riprodotta nel codice seguente:

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static void Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face API Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face API.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

La seconda app di esempio è leggermente più interessante. Consente di scegliere quale API chiamare nei fotogrammi video. A sinistra l'app mostra l'anteprima del video in diretta. A destra sovrappone il risultato dell'API più recente al fotogramma corrispondente.

Nella maggior parte delle modalità, sarà riscontrabile un ritardo visibile tra il video in diretta a sinistra e l'analisi visualizzata a destra. Questo ritardo è il tempo necessario per effettuare la chiamata all'API. La modalità "EmotionsWithClientFaceDetect" fa eccezione, perché esegue il rilevamento del viso in locale nel computer client usando OpenCV prima di inviare qualsiasi immagine a Servizi cognitivi di Azure. 

Con questo approccio, è possibile visualizzare immediatamente il viso rilevato. È possibile aggiornare le emozioni in seguito, dopo che la chiamata all'API avrà restituito il risultato. Questo dimostra la fattibilità di un approccio "ibrido". Ossia, è possibile eseguire una semplice elaborazione nel client e quindi usare le API di Servizi cognitivi per incrementarla con un'analisi più avanzata quando è necessario.

![L'app LiveCameraSample che visualizza un'immagine con tag](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>Integrare gli esempi nella codebase

Per iniziare a usare questo esempio, procedere come segue:

1. Ottenere le chiavi API per le API Visione da [Sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/). Per l'analisi dei fotogrammi video, le API applicabili sono:
    - [L'API Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [L'API Viso](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Clonare il repository di GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

3. Aprire l'esempio in Visual Studio 2015 o versione successiva, quindi compilare ed eseguire le applicazioni di esempio:
    - Per BasicConsoleSample, la chiave dell'API Viso è hardcoded direttamente in [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Per LiveCameraSample, immettere le chiavi nel riquadro **Impostazioni** dell'app. Queste chiavi saranno persistenti tra le sessioni come dati utente.

Quando si è pronti per l'integrazione degli esempi, fare riferimento alla libreria VideoFrameAnalyzer dai propri progetti.

Le funzionalità di riconoscimento delle immagini, della voce, dei video o del testo di VideoFrameAnalyzer usano Servizi cognitivi di Azure. Microsoft riceve le immagini, l'audio, i video e altri dati che vengono caricati (tramite questa app) e potrebbe usarli per migliorare i servizi. Gli sviluppatori devono contribuire a proteggere i dati degli utenti inviati dall'app a Servizi cognitivi di Azure.

## <a name="summary"></a>Summary

In questo articolo si è appreso come eseguire analisi quasi in tempo reale di video in diretta streaming usando l'API Viso e l'API Visione artificiale. Si è anche appreso come usare il codice di esempio per iniziare. Per iniziare a creare la propria app con chiavi API gratuite, passare alla [pagina di iscrizione a Servizi cognitivi di Azure](https://azure.microsoft.com/try/cognitive-services/).

Inviare feedback e suggerimenti nel [repository di GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Per fornire un feedback più ampio sulle API, visitare il [sito UserVoice](https://cognitive.uservoice.com/).

