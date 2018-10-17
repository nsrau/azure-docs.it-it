---
title: "Esempio: Chiamare l'API Emozioni per un video"
titlesuffix: Azure Cognitive Services
description: Informazioni su come chiamare l'API Emozioni per i video in Servizi cognitivi.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: sample
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 2687145a89c11efb4a3bcb1494a39806e9aae551
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238608"
---
# <a name="example-call-emotion-api-for-video"></a>Esempio: Chiamare l'API Emozioni per un video

> [!IMPORTANT]
> L'API Emozioni verrà deprecata il 15 febbraio 2019. La funzionalità di riconoscimento delle emozioni è ora generalmente disponibile nell'ambito dell'[API Viso](https://docs.microsoft.com/azure/cognitive-services/face/). 

Questa guida descrive come chiamare l'API Emozioni per i video. Gli esempi sono scritti in C# e usano la libreria client dell'API Emozioni per i video.

### <a name="Prep">Operazioni preliminari</a>
Per usare l'API Emozioni per i video, occorre un video che includa persone, preferibilmente con il viso rivolto verso la telecamera.

### <a name="Step1">Passaggio 1: Autorizzare la chiamata all'API</a>
Ogni chiamata all'API Emozioni per i video richiede una chiave di sottoscrizione. Questa chiave deve essere passata tramite un parametro di stringa di query o essere specificata nell'intestazione della richiesta. Per passare la chiave di sottoscrizione tramite una stringa di query, fare riferimento all'URL della richiesta seguente per l'API Emozioni per i video, ad esempio:

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

La chiave di sottoscrizione può essere in alternativa specificata nell'intestazione della richiesta HTTP:

```
ocp-apim-subscription-key: <Your subscription key>
```

Quando si usa una libreria client, la chiave di sottoscrizione viene passata tramite il costruttore della classe VideoServiceClient. Ad esempio: 

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
Per ottenere una chiave di sottoscrizione, vedere [sottoscrizioni] (https://azure.microsoft.com/try/cognitive-services/)).

### <a name="Step2">Passaggio 2: Caricare un video nel servizi e controllarne lo stato</a>
Il modo più semplice per eseguire una qualsiasi chiamata all'API Emozioni per i video consiste nel caricare il video direttamente. Si invia a tal fine una richiesta "POST" con il tipo di contenuto del flusso applicazione/flusso di ottetti insieme ai dati letti da un file video. La dimensione massima del video è di 100 MB.

Quando di usa la libreria client, la stabilizzazione tramite caricamento viene ottenuta passando un oggetto flusso. Vedere l'esempio seguente:

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

Il metodo CreateOperationAsync di VideoServiceClient è asincrono. Anche il metodo chiamante deve essere contrassegnato come asincrono in modo che possa usare la clausola await.
Se il video è già caricato sul Web in un URL pubblico, è possibile accedere all'API Emozioni per i video specificando l'URL. In questo esempio il corpo della richiesta sarà una stringa JSON che contiene l'URL.

Se si usa la libreria client, è possibile ottenere la stabilizzazione tramite un URL usando un altro overload del metodo CreateOperationAsync.


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

Questo metodo di caricamento sarà lo stesso per tutte le chiamate all'API Emozioni per i video.

Dopo aver caricato un video, l'operazione successiva consiste nel verificarne lo stato. Poiché i file video sono in genere più grandi e più diversi rispetto agli altri file, gli utenti devono prevedere un tempo di elaborazione lungo in questo passaggio. Il tempo dipende dalla dimensione e dalla durata del file.

Se si usa la libreria client, è possibile recuperare lo stato e il risultato dell'operazione usando il metodo GetOperationResultAsync.


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
Il lato client recupera in genere periodicamente lo stato dell'operazione fino a quando lo stato non diventa "Succeeded" o "Failed".

```
OperationResult operationResult;
while (true)
{
      operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);
      if (operationResult.Status == OperationStatus.Succeeded || operationResult.Status == OperationStatus.Failed)
      {
           break;
      }

      Task.Delay(30000).Wait();
}

```

Quando lo stato di VideoOperationResult è "Succeeded", il risultato può essere recuperato eseguendo il cast di VideoOperationResult a VideoOperationInfoResult<VideoAggregateRecognitionResult> e accedendo al campo ProcessingResult.

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">Passaggio 3: Recuperare e riconoscere la funzionalità di riconoscimento delle emozioni e tenere traccia dell'output JSON</a>

Il risultato dell'output contiene i metadati dei visi all'interno del file specifico nel formato JSON.

Come illustrato nel passaggio 2, l'output JSON è disponibile nel campo ProcessingResult di OperationResult quando il relativo stato viene visualizzato come "Succeeded".

Il file JSON sul rilevamento e monitoraggio dei volti include gli attributi seguenti:

Attributo | DESCRIZIONE
-------------|-------------
Version | Si riferisce alla versione dell'API Emozioni per i video JSON.
Scala cronologica | "Tick" al secondo del video.
Offset  |Differenza di orario dei timestamp. Nella versione 1.0 dell'API Emozioni per i video, questo valore sarà sempre 0. Negli scenari futuri supportati questo valore potrebbe cambiare.
Frequenza fotogrammi | Fotogrammi al secondo del video.
Frammenti   | I metadati sono suddivisi in segmenti più piccoli diversi, detti frammenti. Ogni frammento contiene un inizio, una durata, un numero di intervallo e uno o più eventi.
Inizia   | Ora di inizio del primo evento in "tick".
Duration |  Lunghezza del frammento in "tick".
Interval |  Lunghezza di ogni evento all'interno del frammento in "tick".
Eventi  | Matrice di eventi. La matrice esterna rappresenta un intervallo di tempo. La matrice interna è costituita da 0 o più eventi che si sono verificati in un determinato momento.
windowFaceDistribution |    Percentuale di visi che hanno un'emozione specifica durante l'evento.
windowMeanScores |  Punteggi medi per ogni emozione dei visi nell'immagine.

Il file JSON viene formattato in questo modo per impostare le API per scenari futuri, dove sarà importante recuperare i metadati rapidamente e gestire un flusso di risultati di grandi dimensioni. Questa formattazione usa sia la tecnica di frammentazione, che consente di suddividere i metadati nelle parti basate sul tempo, in cui è possibile scaricare solo il necessario, che la tecnica di segmentazione, che consente di suddividere gli eventi se sono di dimensioni troppo grandi. Alcuni semplici calcoli consentono di trasformare i dati. Se ad esempio un evento è iniziato in corrispondenza di 6300 (tick), con una scala cronologica di 2997 (tick al secondo) e una frequenza di fotogrammi di 29,97 (fotogrammi al secondo):

*   Inizio/Scala cronologica = 2,1 secondi
*   Secondi x (frequenza fotogrammi/scala cronologica) = 63 fotogrammi

Di seguito è riportato un esempio semplice di estrazione del file JSON in formato per fotogramma per il rilevamento e il monitoraggio dei volti:

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
Poiché le emozioni vengono smussate nel corso del tempo, se si compila una visualizzazione per sovrapporre i propri risultati al video originale, sottrarre 250 millisecondi dal timestamp specificato.

### <a name="Summary">Summary</a>
In questa guida sono state fornite informazioni sulle funzionalità dell'API Emozioni per i video: come caricare un video, verificarne lo stato, recuperare i metadati di riconoscimento delle emozioni.

Per altre informazioni dettagliate sull'API, vedere la guida di riferimento sull'API "[Informazioni di riferimento per l'API Emozioni per i video](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e)".
