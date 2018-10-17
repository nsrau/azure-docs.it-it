---
title: 'Esempio: Rilevare i visi nelle immagini - API Viso'
titleSuffix: Azure Cognitive Services
description: Usare l'API Viso per rilevare visi nelle immagini.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: a4c74ff70a4426abf97562bf997479a91afbf17a
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124049"
---
# <a name="example-how-to-detect-faces-in-image"></a>Esempio: Come rilevare i visi nelle immagini

Questa guida dimostrerà come rilevare visi da un'immagine, estraendo attributi del viso come sesso, età o posizione della testa. Gli esempi sono scritti in C# usando la libreria client dell'API Viso. 

## <a name="concepts"></a>Concetti

Se non si ha familiarità con i concetti seguenti in questa guida, cercare le definizioni nel [glossario](../Glossary.md) in qualsiasi momento: 

- Rilevamento del viso
- Punti di riferimento del viso
- Posizione della testa
- Attributi del viso

## <a name="preparation"></a>Operazioni preliminari

In questo esempio verranno dimostrate le funzionalità seguenti: 

- Rilevamento dei visi da un'immagine e contrassegno dei visi tramite una cornice rettangolare
- Analisi della posizione di pupille, naso o bocca e contrassegno di questi elementi nell'immagine
- Analisi della posizione della testa, del sesso e dell'età del viso

Per eseguire queste funzionalità, sarà necessario preparare un'immagine con almeno un viso chiaramente visibile. 

## <a name="step-1-authorize-the-api-call"></a>Passaggio 1: Autorizzare la chiamata API

Ogni chiamata all'API Viso richiede una chiave di sottoscrizione. Questa chiave deve essere passata tramite un parametro di stringa di query o essere specificata nell'intestazione della richiesta. Per passare la chiave di sottoscrizione tramite una stringa di query, fare riferimento all'URL della richiesta [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento) a titolo di esempio:

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

In alternativa, la chiave di sottoscrizione può anche essere specificata nell'intestazione della richiesta HTTP: **ocp-apim-subscription-key: &lt;Chiave sottoscrizione&gt;** Quando si usa una libreria client, la chiave di sottoscrizione viene passata tramite il costruttore della classe FaceServiceClient. Ad esempio: 
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step-2-upload-an-image-to-the-service-and-execute-face-detection"></a>Passaggio 2: Caricare un'immagine nel servizio ed eseguire il rilevamento del viso

Il modo più semplice per eseguire il rilevamento del viso è caricando un'immagine direttamente. Si invia a tal fine una richiesta "POST" con il tipo di contenuto del flusso applicazione/flusso di ottetti insieme ai dati letti da un'immagine JPEG. Le dimensioni massime dell'immagine sono di 4 MB.

Quando di usa la libreria client, il rilevamento del viso tramite caricamento viene eseguito passando un oggetto Stream. Vedere l'esempio seguente:

```CSharp
using (Stream s = File.OpenRead(@"D:\MyPictures\image1.jpg"))
{
    var faces = await faceServiceClient.DetectAsync(s, true, true);
 
    foreach (var face in faces)
    {
        var rect = face.FaceRectangle;
        var landmarks = face.FaceLandmarks;
    }
}
```

Si noti che il metodo DetectAsync di FaceServiceClient è asincrono. Anche il metodo chiamante deve essere contrassegnato come asincrono in modo che possa usare la clausola await.
Se l'immagine è già sul Web e ha un URL, è possibile eseguire il rilevamento del viso anche specificando l'URL. In questo esempio, il corpo della richiesta sarà una stringa JSON che contiene l'URL.
Quando si usa la libreria client, il rilevamento del viso tramite un URL può essere eseguito facilmente usando un altro overload del metodo DetectAsync.

```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

La proprietà FaceRectangle che viene restituita con i visi rilevati corrisponde essenzialmente alle posizioni nel viso in pixel. Questo rettangolo contiene in genere gli occhi, le sopracciglia, il naso e la bocca. La sommità della testa, le orecchie e il mento non sono inclusi. Se si ritaglia una testa completa o un ritratto a mezzo busto (un'immagine del tipo usato per i documenti), è possibile espandere l'area della cornice rettangolare del viso, perché l'area del viso potrebbe essere troppo piccola per alcune applicazioni. Per un posizionamento più preciso del viso, sarà utile ricorrere ai punti di riferimento del viso (meccanismi per l'individuazione delle caratteristiche o della direzione del viso) descritti nella prossima sezione.

## <a name="step-3-understanding-and-using-face-landmarks"></a>Passaggio 3: Comprensione e uso dei punti di riferimento del viso

I punti di riferimenti del viso sono una serie di punti dettagliati su un viso, in genere componenti come pupille, canto o naso. I punti di riferimento del viso sono attributi facoltativi che possono essere analizzati durante il rilevamento del viso. È possibile passare 'true' come valore booleano al parametro di query returnFaceLandmarks quando si chiama l'API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento) oppure usare il parametro facoltativo returnFaceLandmarks per il metodo DetectAsync della classe FaceServiceClient per includere i punti di riferimento del viso nei risultati del rilevamento.

Per impostazione predefinita, esistono 27 punti di riferimento predefiniti. La figura seguente mostra come sono definiti tutti i 27 punti:

![HowToDetectFace](../Images/landmarks.1.jpg)

I punti restituiti sono espressi in unità di pixel, esattamente come la cornice rettangolare per il viso. Risulta pertanto più semplice contrassegnare punti di interesse specifici nell'immagine. Il codice seguente illustra come recuperare le posizioni di naso e pupille:

```CSharp
var faces = await faceServiceClient.DetectAsync(imageUrl, returnFaceLandmarks:true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
 
    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;
 
    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;
 
    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
``` 

Oltre a contrassegnare le caratteristiche di un viso in un'immagine, i punti di riferimento possono anche essere usati per calcolare con precisione la direzione del viso. Ad esempio, è possibile definire la direzione del viso come vettore dal centro della bocca al centro degli occhi. Il codice seguente illustra questa procedura in dettaglio:

```CSharp
var landmarks = face.FaceLandmarks;
 
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

Se si conosce la direzione del viso, è possibile ruotare la cornice rettangolare per allinearla al viso. È evidente che i punti di riferimento del viso possono offrire maggiori dettagli ed essere di grande utilità.

## <a name="step-4-using-other-face-attributes"></a>Passaggio 4: Uso di altri attributi del viso

Oltre ai punti di riferimento del viso, l'API Viso - Rilevamento può anche analizzare vari altri attributi in un viso. Gli attributi disponibili sono:

- Age
- Sesso
- Intensità del sorriso
- Peli del volto
- Posizione della testa 3D

La previsione di questi attributi avviene tramite algoritmi statistici e potrebbe non essere sempre precisa al 100%. Tuttavia, sono comunque utili per classificare i visi sulla base di questi attributi. Per altre informazioni sui singoli attributi, vedere il [glossario](../Glossary.md).

Di seguito è riportato un esempio semplice di estrazione degli attributi del viso durante il rilevamento:

```CSharp
var requiredFaceAttributes = new FaceAttributeType[] {
                FaceAttributeType.Age,
                FaceAttributeType.Gender,
                FaceAttributeType.Smile,
                FaceAttributeType.FacialHair,
                FaceAttributeType.HeadPose,
                FaceAttributeType.Glasses
            };
var faces = await faceServiceClient.DetectAsync(imageUrl,
    returnFaceLandmarks: true,
    returnFaceAttributes: requiredFaceAttributes);

foreach (var face in faces)
{
    var id = face.FaceId;
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
}
``` 

## <a name="summary"></a>Summary

In questa guida sono state presentate le funzionalità dell'API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento): come consente di rilevare i visi in immagini caricate in locale o tramite URL di immagini nel Web, come consente di rilevare i visi racchiudendoli in cornici rettangolari e come supporta anche l'analisi dei punti di riferimento del viso, delle posizioni della testa 3D e di altri attributi del viso.

Per altri dettagli sull'API, vedere la guida di riferimento dell'API per [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento).

## <a name="related-topics"></a>Argomenti correlati

[Come identificare visi nelle immagini](HowtoIdentifyFacesinImage.md)
