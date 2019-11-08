---
title: Rilevare visi in un'immagine-API Viso
titleSuffix: Azure Cognitive Services
description: Questa guida illustra come usare il rilevamento viso per estrarre attributi come Gender, Age o pose da un'immagine specifica.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 9b66231d995ffb6980ce36852115c571cd102681
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744274"
---
# <a name="get-face-detection-data"></a>Ottenere i dati di rilevamento viso

Questa guida illustra come usare il rilevamento viso per estrarre attributi come Gender, Age o pose da un'immagine specifica. I frammenti di codice in questa guida sono scritti in C# usando la libreria client di servizi cognitivi di Azure API viso. La stessa funzionalità è disponibile tramite l' [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Questa guida illustra come:

- Ottenere le posizioni e le dimensioni dei visi in un'immagine.
- Ottenere le posizioni dei diversi punti di interesse, ad esempio gli alunni, il naso e la bocca, in un'immagine.
- Indovinare il sesso, l'età, l'emozione e altri attributi di un volto rilevato.

## <a name="setup"></a>Configurazione

In questa guida si presuppone che sia già stato creato un oggetto [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet), denominato `faceClient`, con una chiave di sottoscrizione della faccia e un URL dell'endpoint. Da qui è possibile usare la funzionalità di rilevamento viso chiamando [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), che viene usato in questa guida oppure [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Per istruzioni su come configurare questa funzionalità, seguire una delle guide introduttive.

Questa guida è incentrata sulle specifiche della chiamata di rilevamento, ad esempio sugli argomenti che è possibile passare e sulle operazioni che è possibile eseguire con i dati restituiti. Si consiglia di eseguire una query solo per le funzionalità necessarie. Ogni operazione richiede ulteriore tempo per il completamento.

## <a name="get-basic-face-data"></a>Ottenere i dati di base sui visi

Per trovare i visi e ottenere le rispettive posizioni in un'immagine, chiamare il metodo con il parametro _returnFaceId_ impostato su **true**. Questa è l'impostazione predefinita.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

È possibile eseguire una query sugli oggetti [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) restituiti per i rispettivi ID univoci e un rettangolo che fornisce le coordinate dei pixel della faccia.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Per informazioni su come analizzare la posizione e le dimensioni della faccia, vedere [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). In genere, questo rettangolo contiene gli occhi, le sopracciglia, il muso e la bocca. La parte superiore della testa, delle orecchie e del mento non è necessariamente inclusa. Per usare il rettangolo della faccia per ritagliare un'intestazione completa o ottenere un ritratto a metà ripresa, ad esempio per un'immagine di tipo ID foto, è possibile espandere il rettangolo in ogni direzione.

## <a name="get-face-landmarks"></a>Ottieni punti di riferimento per i volti

I punti di [interesse della faccia](../concepts/face-detection.md#face-landmarks) sono un set di punti di facile individuazione su una faccia, ad esempio gli alunni o la punta del naso. Per ottenere i dati di riferimento del viso, impostare il parametro _returnFaceLandmarks_ su **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Il codice seguente illustra come è possibile recuperare le posizioni del naso e degli alunni:

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

È anche possibile usare i dati dei punti di riferimento della faccia per calcolare accuratamente la direzione della faccia. Ad esempio, è possibile definire la rotazione della faccia come vettore dal centro della bocca al centro degli occhi. Il codice seguente calcola questo vettore:

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

Quando si conosce la direzione della faccia, è possibile ruotare il frame della superficie rettangolare per allinearlo in modo più appropriato. Per ritagliare i visi in un'immagine, è possibile ruotare l'immagine a livello di codice in modo che i visi siano sempre montati verticalmente.

## <a name="get-face-attributes"></a>Ottieni attributi viso

Oltre ai rettangoli e ai punti di riferimento, l'API di rilevamento viso può analizzare diversi attributi concettuali di una faccia. Per un elenco completo, vedere la sezione concettuale degli [attributi Face](../concepts/face-detection.md#attributes) .

Per analizzare gli attributi viso, impostare il parametro _returnFaceAttributes_ su un elenco di valori di [enumerazione FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) .

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

Ottenere quindi i riferimenti ai dati restituiti ed eseguire più operazioni in base alle esigenze.

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

Per ulteriori informazioni su ogni attributo, vedere la Guida concettuale relativa al [rilevamento e agli attributi del viso](../concepts/face-detection.md) .

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come usare le varie funzionalità del rilevamento dei volti. Integrare quindi le funzionalità nell'app seguendo un'esercitazione approfondita.

- [Esercitazione: creare un'app WPF per visualizzare i dati relativi ai visi in un'immagine](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Esercitazione: creare un'app Android per rilevare e incorniciare i visi in un'immagine](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Argomenti correlati

- [Documentazione di riferimento (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentazione di riferimento (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)