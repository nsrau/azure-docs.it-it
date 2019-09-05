---
title: Come specificare un modello di rilevamento-API Viso
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come scegliere il modello di rilevamento viso da usare con l'applicazione Azure API Viso.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 4306a918d56240bfe038100124b3c2b94964cebc
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306676"
---
# <a name="specify-a-face-detection-model"></a>Specificare un modello di rilevamento volto

Questa guida illustra come specificare un modello di rilevamento viso per la API Viso di Azure.

Il API Viso usa i modelli di apprendimento automatico per eseguire operazioni sui visi umani nelle immagini. Continuiamo a migliorare l'accuratezza dei nostri modelli in base ai commenti e ai suggerimenti dei clienti e ai miglioramenti apportati alla ricerca e offriamo questi miglioramenti come aggiornamenti del modello. Gli sviluppatori hanno la possibilità di specificare la versione del modello di rilevamento viso che si vuole usare. possono scegliere il modello più adatto al caso d'uso.

Vedere per informazioni su come specificare il modello di rilevamento viso in determinate operazioni del viso. Il API Viso usa il rilevamento viso ogni volta che converte un'immagine di una faccia in un altro tipo di dati.

Se non si è certi che sia necessario utilizzare il modello più recente, passare alla sezione [valuta modelli diversi](#evaluate-different-models) per valutare il nuovo modello e confrontare i risultati utilizzando il set di dati corrente.

## <a name="prerequisites"></a>Prerequisiti

È necessario avere familiarità con il concetto di rilevamento delle facce di intelligenza artificiale. Se non lo è, vedere la Guida concettuale per il rilevamento viso o la Guida alle procedure:

* [Concetti relativi al rilevamento viso](../concepts/face-detection.md)
* [Come rilevare i visi in un'immagine](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Rileva visi con il modello specificato

Il rilevamento viso trova le posizioni del riquadro dei visi umani e identifica i relativi punti di interesse visivi. Estrae le funzionalità del viso e le archivia per un uso successivo nelle operazioni di [riconoscimento](../concepts/face-recognition.md) .

Quando si usa l'API [Rilevamento viso] , è possibile assegnare la versione del modello con il `detectionModel` parametro. I valori disponibili sono:

* `detection_01`
* `detection_02`

Un URL della richiesta per l'API REST di [rilevamento viso] sarà simile al seguente:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Se si usa la libreria client, è possibile assegnare il valore per `detectionModel` passando una stringa appropriata. Se la si lascia non assegnata, l'API utilizzerà la versione del modello`detection_01`predefinita (). Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Aggiungi faccia a persona con il modello specificato

Il API Viso è in grado di estrarre i dati facciali da un'immagine e di associarli a un oggetto **Person** tramite l'API [gruppo person-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) . In questa chiamata API è possibile specificare il modello di rilevamento nello stesso modo in cui si trova nel [Rilevamento viso].

Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Questo codice crea un **gruppo** con ID `mypersongroupid` e vi aggiunge una **persona** . Quindi aggiunge un volto a questa **persona** usando il `detection_02` modello. Se non si specifica il parametro *detectionModel* , l'API utilizzerà il modello `detection_01`predefinito.

> [!NOTE]
> Non è necessario usare lo stesso modello di rilevamento per tutti i visi in un oggetto **Person** e non è necessario usare lo stesso modello di rilevamento quando si rilevano nuovi visi da confrontare con un oggetto **Person** (ad esempio, nell'API di [Face - Identify] ).

## <a name="add-face-to-facelist-with-specified-model"></a>Aggiungi facet a facet con il modello specificato

È inoltre possibile specificare un modello di rilevamento quando si aggiunge una faccia a un oggetto **facet** esistente. Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Questo codice crea un **facet** denominato `My face collection` e ne aggiunge una faccia con il `detection_02` modello. Se non si specifica il parametro *detectionModel* , l'API utilizzerà il modello `detection_01`predefinito.

> [!NOTE]
> Non è necessario utilizzare lo stesso modello di rilevamento per tutti i visi in un oggetto **facet** e non è necessario utilizzare lo stesso modello di rilevamento quando si rilevano nuovi visi da confrontare con un oggetto **facet** .

## <a name="evaluate-different-models"></a>Valutare modelli diversi

I diversi modelli di rilevamento viso sono ottimizzati per diverse attività. Per una panoramica delle differenze, vedere la tabella seguente.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Scelta predefinita per tutte le operazioni di rilevamento del viso. | Rilasciato nel maggio 2019 e disponibile facoltativamente in tutte le operazioni di rilevamento del viso.
|Non ottimizzato per visi piccoli, a visualizzazione laterale o sfocata.  | Accuratezza migliorata per i visi piccoli, di visualizzazione laterale e sfocata. |
|Restituisce gli attributi del viso (posto, età, emozione e così via) se sono specificati nella chiamata di rilevamento. |  Non restituisce attributi della faccia.     |
|Restituisce i punti di riferimento della faccia se specificati nella chiamata di rilevamento.   | Non restituisce riferimenti ai visi.  |

Il modo migliore per confrontare le prestazioni dei modelli `detection_01` e `detection_02` è usarli in un set di dati di esempio. È consigliabile chiamare l'API di [rilevamento viso] su un'ampia gamma di immagini, in particolare immagini di molti visi o di visi difficili da visualizzare, usando ogni modello di rilevamento. Prestare attenzione al numero di visi restituiti da ogni modello.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come specificare il modello di rilevamento da usare con diverse API viso. Seguire quindi una guida introduttiva per iniziare a usare il rilevamento delle facce.

* [.NET SDK facet](../Quickstarts/csharp-sdk.md)
* [Python SDK per visi](../Quickstarts/python-sdk.md)

[Rilevamento viso]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
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
