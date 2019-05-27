---
title: Rileva i visi in un'immagine - API viso
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare i vari dati restituiti dalla funzionalità di rilevamento del viso.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 46bd1bdd55725878bc7b1bd55d5e24b78d82aada
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124541"
---
# <a name="get-face-detection-data"></a>Ottenere i dati di rilevamento viso

Questa guida illustra come usare il rilevamento viso per estrarre gli attributi, ad esempio sesso, età o posa da una determinata immagine. I frammenti di codice in questa guida sono scritti in C# usando la libreria di client API viso servizi cognitivi di Azure. La stessa funzionalità è disponibile tramite il [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Questa guida illustra la modalità per:

- Ottenere le posizioni e le dimensioni dei visi in un'immagine.
- Ottenere le posizioni di vari riquadri, ad esempio alunni e naso bocca, in un'immagine.
- Indovinare il sesso, età, emozione e altri attributi di un volto rilevato.

## <a name="setup"></a>Configurazione

Questa guida si presuppone che già creato un [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) oggetto, denominato `faceClient`, con un URL di chiave e l'endpoint sottoscrizione viso. A questo punto, è possibile usare la funzionalità di rilevamento viso chiamando [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), che viene usato in questa Guida, o [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Per istruzioni su come configurare questa funzionalità, vedere la [rileva i volti delle Guide rapide per C# ](../quickstarts/csharp-detect-sdk.md).

Questa guida è incentrata sulle specifiche della chiamata di rilevamento, ad esempio gli argomenti che è possibile passare e operazioni eseguibili con i dati restituiti. Si consiglia di cercare solo le funzionalità che necessarie. Ogni operazione richiede più tempo per completare.

## <a name="get-basic-face-data"></a>Ottenere i dati di base viso

Per trovare visi e ottenere le relative posizioni in un'immagine, chiamare il metodo con il _returnFaceId_ parametro impostato su **true**. Questa è l'impostazione predefinita.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

È possibile eseguire una query restituita [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) gli oggetti per univoco ID e un rettangolo che offra le coordinate di pixel della faccia.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Per informazioni su come analizzare la posizione e le dimensioni del volto, vedere [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Questo rettangolo contiene in genere, gli occhi, eyebrows, naso e bocca. Le proprietà di head, orecchie e mento non sono necessariamente inclusi. Per usare il rettangolo per il viso per ritagliare un elemento head completo oppure ottenere ritratto cattura intermedio, ad esempio per un'immagine di tipo ID foto, è possibile espandere il rettangolo in ogni direzione.

## <a name="get-face-landmarks"></a>Ottenere i riquadri

[Punti di riferimento di affrontare](../concepts/face-detection.md#face-landmarks) sono un set di punti di facile da trovare in un viso, ad esempio gli alunni o di punta. Per ottenere dati luoghi di interesse viso, impostare il _returnFaceLandmarks_ parametro per **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Il codice seguente viene illustrato come è possibile recuperare le posizioni del naso e studenti:

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

È anche possibile utilizzare dati di punti di riferimento viso per calcolare con precisione la direzione della faccia. Ad esempio, è possibile definire la rotazione del volto come vettore dal centro della bocca al centro degli occhi. Il codice seguente consente di calcolare il vettore:

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

Quando si conosce la direzione del volto, è possibile ruotare il frame rettangolare viso per allinearla più correttamente. Per ritagliare visi in un'immagine, a livello di codice è possibile ruotare l'immagine in modo che le facce vengono sempre visualizzati in verticale.

## <a name="get-face-attributes"></a>Ottenere gli attributi del viso

Oltre ai rettangoli e punti di riferimento, l'API di rilevamento viso in grado di analizzare diversi attributi concettuali di un viso. Per un elenco completo, vedere la [gli attributi facciali](../concepts/face-detection.md#attributes) sezione concettuale.

Per analizzare attributi facciali, impostare il _returnFaceAttributes_ parametro per un elenco delle [FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) valori.

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

Ottenere riferimenti a dati restituiti, quindi eseguire altre operazioni in base alle esigenze.

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

Per altre informazioni su ciascuno degli attributi, vedere la [rilevamento viso ed attributi](../concepts/face-detection.md) Guida concettuale.

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato descritto come utilizzare le diverse funzionalità di rilevamento del viso. Successivamente, è possibile integrare queste funzionalità nell'app, seguire un'esercitazione dettagliata.

- [Esercitazione: Creare un'app WPF per visualizzare i dati di visi in un'immagine](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Esercitazione: Creare un'app Android per rilevare e visi in un'immagine di frame](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Argomenti correlati

- [Documentazione di riferimento (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentazione di riferimento (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)