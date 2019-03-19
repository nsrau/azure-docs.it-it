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
ms.date: 02/22/2019
ms.author: sbowles
ms.openlocfilehash: bf3af8f5d1d2f063199a8275c2f49c70140e8732
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588772"
---
# <a name="get-face-detection-data"></a>Ottenere i dati di rilevamento viso

Questa guida illustra come usare il rilevamento viso per estrarre gli attributi, ad esempio sesso, età o posa da una determinata immagine. I frammenti di codice in questa guida sono scritti in C# usando la libreria di client API viso, ma la stessa funzionalità è disponibile tramite il [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Questa guida viene illustrato come a:

- Ottenere le posizioni e le dimensioni dei visi in un'immagine.
- Ottenere le posizioni di vari riquadri (studenti, naso, bocca e così via) in un'immagine.
- Indovinare il sesso, età e delle emozioni e altri attributi di un volto rilevato.

## <a name="setup"></a>Configurazione

Questa guida si presuppone di aver già costruito un **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** oggetto, denominato `faceClient`, con un URL di chiave e l'endpoint sottoscrizione viso. A questo punto, è possibile usare la funzionalità di rilevamento viso chiamando **[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)** (usato in questa Guida) oppure **[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)**. Vedere le [Guida introduttiva di rilevare i volti a C# ](../quickstarts/csharp-detect-sdk.md) per istruzioni su come impostare questa funzionalità.

Questa guida è incentrata sulle specifiche della chiamata di rilevamento&mdash;gli argomenti che è possibile passare e operazioni eseguibili con i dati restituiti. È consigliabile solo l'esecuzione di query per le funzionalità che necessarie, perché ogni operazione richiede più tempo per completare.

## <a name="get-basic-face-data"></a>Ottenere i dati di base viso

Per trovare visi e ottenere le relative posizioni in un'immagine, chiamare il metodo con il _returnFaceId_ parametro impostato su **true** (impostazione predefinita).

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

L'oggetto restituito **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** oggetti possano essere interrogati per i relativi ID univoco e un rettangolo che fornisce le coordinate di pixel della faccia.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Visualizzare **[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)** per informazioni su come analizzare la posizione e dimensioni della superficie. Questo rettangolo contiene in genere, gli occhi, eyebrows, naso e bocca; le proprietà di head, orecchie e mento non sono necessariamente inclusi. Se si prevede di utilizzare il rettangolo per il viso per ritagliare un head completo o intermedio cattura verticale (un'immagine di tipo ID di foto), è possibile espandere il rettangolo per un certo margine in entrambe le direzioni.

## <a name="get-face-landmarks"></a>Ottenere i riquadri

Riquadri sono un set di punti di facile da trovare in un viso, ad esempio gli alunni o il suggerimento di punta. È possibile ottenere dati di luoghi di interesse viso impostando il _returnFaceLandmarks_ parametro per **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Per impostazione predefinita, esistono 27 punti di riferimento predefiniti. La figura seguente mostra tutti i punti di 27:

![Un diagramma di viso con tutti i 27 riferimenti etichettate](../Images/landmarks.1.jpg)

I punti restituiti sono in unità di pixel, esattamente come il frame di rettangolo viso. Il codice seguente viene illustrato come è possibile recuperare le posizioni del naso e studenti:

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

Viso riferimenti dati sono anche utilizzabile per calcolare con precisione la direzione della faccia. Ad esempio, è possibile definire la rotazione del volto come vettore dal centro della bocca al centro dal punto di vista. Il codice seguente consente di calcolare il vettore:

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

Conoscendo la direzione del volto, quindi è possibile ruotare il frame rettangolare viso per allinearla più correttamente. Se si desidera ritagliare visi in un'immagine, è possibile ruotare a livello di codice l'immagine in modo che le facce vengono sempre visualizzati in verticale.

## <a name="get-face-attributes"></a>Ottenere gli attributi del viso

Oltre ai rettangoli e punti di riferimento, l'API di rilevamento viso in grado di analizzare diversi attributi concettuali di un viso. incluse le seguenti:

- Age
- Sesso
- Intensità del sorriso
- Peli del volto
- Occhiali
- Posa head 3D
- Emozioni

> [!IMPORTANT]
> Questi attributi sono stimati tramite l'uso di algoritmi statistici e potrebbero non essere sempre accurati. Prestare attenzione quando si eseguono decisioni basate sui dati dell'attributo.
>

Per analizzare attributi facciali, impostare il _returnFaceAttributes_ parametro per un elenco delle **[FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** valori.

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

Quindi, ottenere riferimenti a dati restituiti ed eseguire altre operazioni in base alle esigenze.

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

Per altre informazioni su ciascuno degli attributi, vedere la [glossario](../Glossary.md).

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato descritto come utilizzare le diverse funzionalità di rilevamento del viso. Successivamente, vedere la [glossario](../Glossary.md) per un'analisi più approfondita i dati di volti recuperati.

## <a name="related-topics"></a>Argomenti correlati

- [Documentazione di riferimento (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentazione di riferimento (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)
