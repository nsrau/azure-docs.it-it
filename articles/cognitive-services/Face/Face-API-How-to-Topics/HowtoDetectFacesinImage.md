---
title: Rilevare i volti in un'immagine - Faccia
titleSuffix: Azure Cognitive Services
description: Questa guida mostra come usare il rilevamento volti per estrarre attributi come sesso, età o posa da una determinata immagine.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169886"
---
# <a name="get-face-detection-data"></a>Ottenere dati di rilevamento voltiGet face detection data

Questa guida mostra come usare il rilevamento volti per estrarre attributi come sesso, età o posa da una determinata immagine. I frammenti di codice in questa guida vengono scritti in C , usando la libreria client di Azure Cognitive Services Face.The code snippets in this guide are written in C's using the Azure Cognitive Services Face client library. La stessa funzionalità è disponibile tramite [l'API REST.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

Questa guida mostra come:

- Ottenere le posizioni e le dimensioni delle facce in un'immagine.
- Ottenere le posizioni di vari punti di riferimento del viso, come pupille, naso e bocca, in un'immagine.
- Indovina il sesso, l'età, l'emozione e altri attributi di un volto rilevato.

## <a name="setup"></a>Configurazione

In questa guida si presuppone che sia `faceClient`già stato costruito un oggetto [FaceClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) denominato , con una chiave di sottoscrizione Face e un URL endpoint. A questo punto, è possibile utilizzare la funzionalità di rilevamento volti chiamando [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), utilizzato in questa guida, o [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Per istruzioni su come configurare questa funzionalità, seguire una delle guide introduttive.

Questa guida è incentrata sulle specifiche della chiamata Detect, ad esempio gli argomenti che è possibile passare e le operazioni che è possibile eseguire con i dati restituiti. È consigliabile eseguire una query solo per le funzionalità necessarie. Il completamento di ogni operazione richiede più tempo.

## <a name="get-basic-face-data"></a>Ottenere i dati di base del voltoGet basic face data

Per trovare i volti e ottenere le relative posizioni in un'immagine, chiamare il metodo con il parametro _returnFaceId_ impostato su **true**. Questa è l'impostazione predefinita.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

È possibile eseguire una query sugli oggetti [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) restituiti per ottenere gli ID univoci e un rettangolo che fornisce le coordinate pixel della faccia.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Per informazioni su come analizzare la posizione e le dimensioni della faccia, vedere [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Di solito, questo rettangolo contiene gli occhi, le sopracciglia, il naso e la bocca. La parte superiore della testa, delle orecchie e del mento non sono necessariamente incluse. Per utilizzare il rettangolo del viso per ritagliare una testa completa o ottenere un ritratto a metà scatto, ad esempio per un'immagine di tipo ID foto, è possibile espandere il rettangolo in ogni direzione.

## <a name="get-face-landmarks"></a>Ottieni punti di riferimento per i volti

[I punti](../concepts/face-detection.md#face-landmarks) di riferimento del viso sono un insieme di punti facili da trovare su un viso, come le pupille o la punta del naso. Per ottenere i dati dei punti di riferimento della faccia, impostare il parametro _returnFaceLandmarks_ su **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Il codice seguente mostra come recuperare le posizioni del naso e delle pupille:

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

È inoltre possibile utilizzare i dati dei punti di riferimento della faccia per calcolare con precisione la direzione della faccia. Ad esempio, è possibile definire la rotazione della faccia come vettore dal centro della bocca al centro degli occhi. Il codice seguente calcola questo vettore:The following code calculates this vector:

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

Quando si conosce la direzione della faccia, è possibile ruotare la cornice rettangolare della faccia per allinearla in modo più corretto. Per ritagliare i volti in un'immagine, è possibile ruotare l'immagine a livello di codice in modo che vengano sempre visualizzati in posizione verticale.

## <a name="get-face-attributes"></a>Ottenere gli attributi del voltoGet face attributes

Oltre ai rettangoli e ai punti di riferimento del viso, l'API di rilevamento dei volti può analizzare diversi attributi concettuali di un volto. Per un elenco completo, consultate la sezione [concettuale Attributi facciali.](../concepts/face-detection.md#attributes)

Per analizzare gli attributi della faccia, impostare il parametro _returnFaceAttributes_ su un elenco di valori [FaceAttributeType Enum.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Quindi, ottenere i riferimenti ai dati restituiti ed eseguire più operazioni in base alle proprie esigenze.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Per ulteriori informazioni su ciascuno degli attributi, vedere la guida concettuale rilevamento volti [e attributi.](../concepts/face-detection.md)

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come utilizzare le varie funzionalità del rilevamento volti. Successivamente, integra queste funzionalità nella tua app seguendo un'esercitazione approfondita.

- [Esercitazione: Creare un'app WPF per mostrare i dati sui volti in un'immagine](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Esercitazione: Creare un'app Android per rilevare e incorniciare i visi in un'immagine](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Argomenti correlati

- [Documentazione di riferimento (REST)Reference documentation (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentazione di riferimento (.NET SDK)Reference documentation (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)