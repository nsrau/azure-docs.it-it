---
title: Come specificare un modello di rilevamento - Faccia
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come scegliere il modello di rilevamento volti da usare con l'applicazione Azure Face.This article show you how to choose which face detection model to use with your Azure Face application.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 40ca1dbf981c5a9025cf5a0bac6b007709d69a77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76934570"
---
# <a name="specify-a-face-detection-model"></a>Specificare un modello di rilevamento volto

Questa guida illustra come specificare un modello di rilevamento volti per il servizio Azure Face.This guide shows you how to specify a face detection model for the Azure Face service.

Il servizio Face utilizza modelli di apprendimento automatico per eseguire operazioni sui volti umani nelle immagini. Continuiamo a migliorare l'accuratezza dei nostri modelli in base al feedback dei clienti e ai progressi nella ricerca, e forniamo questi miglioramenti come aggiornamenti del modello. Gli sviluppatori hanno la possibilità di specificare quale versione del modello di rilevamento volti desiderano utilizzare; possono scegliere il modello che meglio si adatta al loro caso d'uso.

Continuate a leggere per scoprire come specificare il modello di rilevamento facciale in determinate operazioni facciali. Il servizio Face utilizza il rilevamento volti ogni volta che converte un'immagine di un volto in un'altra forma di dati.

Se non si è certi di dover utilizzare il modello più recente, passare alla sezione [Valutare modelli diversi](#evaluate-different-models) per valutare il nuovo modello e confrontare i risultati utilizzando il set di dati corrente.

## <a name="prerequisites"></a>Prerequisiti

Si dovrebbe avere familiarità con il concetto di AI face detection. In caso contrario, consulta la guida concettuale per il rilevamento dei volti o la guida alle procedure:

* [Concetti relativi al rilevamento dei volti](../concepts/face-detection.md)
* [Come rilevare i volti in un'immagine](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Rilevare le facce con il modello specificato

Il rilevamento volti trova le posizioni del riquadro di delimitazione dei volti umani e ne identifica i punti di riferimento visivi. Estrae le caratteristiche del volto e le memorizza per un uso successivo nelle operazioni di [riconoscimento.](../concepts/face-recognition.md)

Quando si utilizza l'API [Face - Detect,] `detectionModel` è possibile assegnare la versione del modello con il parametro . I valori disponibili sono:

* `detection_01`
* `detection_02`

Un URL di richiesta per l'API [REST Face - Detect] sarà simile al seguente:A request URL for the Face - Detect REST API will look like this:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Se si utilizza la libreria client, è `detectionModel` possibile assegnare il valore per passando una stringa appropriata. Se lo si lascia non assegnato, l'API`detection_01`utilizzerà la versione predefinita del modello ( ). Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Aggiungi volto alla persona con il modello specificato

Il servizio Face può estrarre i dati del volto da un'immagine e associarli a un oggetto **Person** tramite [personGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API. In questa chiamata API, è possibile specificare il modello di rilevamento nello stesso modo in [Face - Detect].

Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Questo codice crea un `mypersongroupid` **PersonGroup** con ID e aggiunge un **Person** ad esso. Poi aggiunge un Volto **Person** a `detection_02` questa Persona usando il modello. Se non si specifica il parametro *detectionModel,* l'API utilizzerà il modello predefinito, `detection_01`.

> [!NOTE]
> Non è necessario usare lo stesso modello di rilevamento per tutte le facce in un oggetto **Person** e non è necessario usare lo stesso modello di rilevamento quando si rilevano nuovi volti da confrontare con un oggetto **Person** (ad esempio nell'API [Face - Identify).]

## <a name="add-face-to-facelist-with-specified-model"></a>Aggiungi faccia a FaceList con il modello specificato

È inoltre possibile specificare un modello di rilevamento quando si aggiunge una faccia a un oggetto **FaceList** esistente. Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Questo codice crea un `My face collection` **FaceList** chiamato e `detection_02` aggiunge un Face ad esso con il modello. Se non si specifica il parametro *detectionModel,* l'API utilizzerà il modello predefinito, `detection_01`.

> [!NOTE]
> Non è necessario utilizzare lo stesso modello di rilevamento per tutte le facce in un **oggetto FaceList** e non è necessario utilizzare lo stesso modello di rilevamento quando si rilevano nuove facce da confrontare con un oggetto **FaceList.**

## <a name="evaluate-different-models"></a>Valutare diversi modelli

I diversi modelli di rilevamento volti sono ottimizzati per diverse attività. Vedere la tabella seguente per una panoramica delle differenze.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Scelta predefinita per tutte le operazioni di rilevamento facciali. | Rilasciato a maggio 2019 e disponibile facoltativamente in tutte le operazioni di rilevamento facciale.
|Non ottimizzato per facce piccole, affiancate o sfocate.  | Maggiore precisione sulle facce piccole, a lato e sfocate. |
|Restituisce gli attributi del volto (posa della testa, età, emozione e così via) se sono specificati nella chiamata di rilevamento. |  Non restituisce gli attributi della faccia.     |
|Restituisce i punti di riferimento faccia se sono specificati nella chiamata di rilevamento.   | Non restituisce punti di riferimento faccia.  |

Il modo migliore per confrontare `detection_01` `detection_02` le prestazioni dei modelli e consiste nell'usarle in un set di dati di esempio. Ti consigliamo di chiamare l'API [Face - Detect] su una varietà di immagini, in particolare immagini di molti volti o di volti difficili da vedere, usando ogni modello di rilevamento. Prestare attenzione al numero di facce restituite da ogni modello.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come specificare il modello di rilevamento da utilizzare con diverse API Face. Seguire quindi una guida introduttiva per iniziare a usare il rilevamento volti.

* [Face .NET SDK](../Quickstarts/csharp-sdk.md)
* [Face Python SDK](../Quickstarts/python-sdk.md)
* [Face Go SDK](../Quickstarts/go-sdk.md)

[Viso - Rilevamento]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Viso - Identificazione]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
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
