---
title: Come specificare un modello di rilevamento - API viso
titleSuffix: Azure Cognitive Services
description: Questo articolo illustrerà come scegliere quale modello di rilevamento viso da usare con l'applicazione API viso di Azure.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 26ab3cb247309aa21791ca5a984f39ef40ce9a78
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249622"
---
# <a name="specify-a-face-detection-model"></a>Specificare un modello di rilevamento volto

Questa guida illustra come specificare un modello di rilevamento viso per l'API viso di Azure.

L'API viso Usa modelli di machine learning per eseguire operazioni sui visi umani nelle immagini. Continuiamo a migliorare la precisione dei nostri modelli basati su commenti e suggerimenti dei clienti e i progressi nelle ricerche e offrire tali funzionalità e miglioramenti come modellare gli aggiornamenti. Gli sviluppatori hanno la possibilità di specificare quale versione del modello di rilevamento viso desiderano utilizzare; è possibile scegliere il modello che meglio si adatta rispettivi casi d'uso.

Continuare a leggere per informazioni su come specificare il modello di rilevamento viso in determinate operazioni di visi. L'API viso Usa il rilevamento viso ogni volta che converte un'immagine di un viso in un'altra forma di dati.

Se non si è certi se sia necessario utilizzare il modello più recente, passare al [valutare modelli diversi](#evaluate-different-models) sezione per valutare il nuovo modello e confrontare i risultati usando il set di dati corrente.

## <a name="prerequisites"></a>Prerequisiti

È necessario avere familiarità con il concetto di rilevamento dei visi per intelligenza artificiale. Se non si, consultare la Guida concettuale di rilevamento viso o sulle procedure:

* [Concetti di rilevamento viso](../concepts/face-detection.md)
* [Come rilevare i visi in un'immagine](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Rileva i visi con il modello specificato

Rilevamento volti rileva i percorsi di riquadro dei volti umani e identifica i punti di riferimento visual. Estrae le funzionalità del viso e archiviarli per usarli in seguito nel [riconoscimento](../concepts/face-recognition.md) operazioni.

Quando si usa la [Viso: rilevare] API, è possibile assegnare la versione del modello con il `detectionModel` parametro. I valori disponibili sono:

* `detection_01`
* `detection_02`

URL della richiesta per il [Viso: rilevare] API REST avrà un aspetto simile al seguente:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Se si usa la libreria client, è possibile assegnare il valore per `detectionModel` passando una stringa appropriata. Se si lascia non assegnati, l'API userà la versione del modello predefinito (`detection_01`). Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Aggiungere volto a persona con il modello specificato

Può estrarre i dati di volti da un'immagine e associalo con l'API viso una **Person** dell'oggetto tramite il [gruppo di persone persona - aggiungere viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API. In questa chiamata API, è possibile specificare il modello di rilevamento nello stesso modo come in [Viso: rilevare].

Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Questo codice crea un **gruppo di persone** con ID `mypersongroupid` e aggiunge un **persona** ad esso. Quindi aggiunge un volto a questo **Person** usando il `detection_02` modello. Se non si specifica la *detectionModel* parametro, l'API verrà utilizzato il modello predefinito, `detection_01`.

> [!NOTE]
> Non è necessario usare lo stesso modello di rilevamento per tutti i volti in una **Person** oggetto ed è non dover usare lo stesso modello di rilevamento quando rileva nuove facce da confrontare con un **persona** oggetto (nel [Face - Identify] API, ad esempio).

## <a name="add-face-to-facelist-with-specified-model"></a>Aggiungere volto a FaceList con il modello specificato

È inoltre possibile specificare un modello di rilevamento quando si aggiunge un volto a un oggetto esistente **FaceList** oggetto. Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Questo codice crea un **FaceList** chiamato `My face collection` e aggiunge un volto a esso con il `detection_02` modello. Se non si specifica la *detectionModel* parametro, l'API verrà utilizzato il modello predefinito, `detection_01`.

> [!NOTE]
> Non è necessario usare lo stesso modello di rilevamento per tutti i volti in una **FaceList** oggetto ed è non dover usare lo stesso modello di rilevamento quando rileva nuove facce da confrontare con un **FaceList** oggetto.

## <a name="evaluate-different-models"></a>Valutare i modelli diversi

I modelli di rilevamento viso diverse sono ottimizzati per attività diverse. Vedere la tabella seguente per una panoramica delle differenze.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Scelta predefinita per tutte le operazioni di rilevamento viso. | Rilasciato in maggio 2019 ed è disponibile facoltativamente in tutte le operazioni di rilevamento viso.
|Non è ottimizzato per piccole, visualizzazione lato o facce sfocate.  | Precisione migliorata in piccole, visualizzazione lato e volti sfocati. |
|Restituisce affrontano gli attributi (head posa, età, emozione e così via) quando sono specificate nella chiamata di rilevamento. |  Non restituisce attributi facciali.     |
|Restituisce affrontano punti di riferimento, se sono specificate nella chiamata di rilevamento.   | Non restituisce riquadri.  |

Il modo migliore per confrontare le prestazioni dei `detection_01` e `detection_02` modelli è possibile usarli in un set di dati di esempio. È consigliabile chiamare il [Viso: rilevare] API in un'ampia gamma di immagini, in particolare le immagini di mille aspetti o di volti che sono difficili da vedere, con ogni modello di rilevamento. Prestare attenzione al numero di volti che restituisce ogni modello.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato descritto come specificare il modello di rilevamento da usare con diverse API viso. Successivamente, eseguire un avvio rapido per iniziare a usare il rilevamento viso.

* [Rileva i visi in un'immagine (.NET SDK)](../quickstarts/csharp-detect-sdk.md)

[Viso: rilevare]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
