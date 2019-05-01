---
title: Utilizzare HeadPose per regolare il rettangolo per il viso
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'attributo HeadPose automaticamente ruotare il rettangolo per il viso.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: ddc5bc522c0d3ac258581f2a48a5c3b755302f01
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576503"
---
# <a name="use-the-headpose-attribute-to-adjust-the-face-rectangle"></a>Usare l'attributo HeadPose per regolare il rettangolo per il viso

In questa Guida, si utilizzerà un attributo volto rilevato, HeadPose, per ruotare il rettangolo di un oggetto di visi. L'esempio di codice in questa Guida, dal [WPF viso di servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) app di esempio, Usa .NET SDK.

Il rettangolo per il viso, restituito con ogni volto rilevato, contrassegna la posizione e dimensione del viso nell'immagine. Per impostazione predefinita, il rettangolo è sempre allineato con l'immagine (suoi lati sono perfettamente verticale e orizzontale); Ciò può risultare inefficiente per i visi angolari di framing. In situazioni in cui si desidera a livello di programmazione ritagliare visi in un'immagine, è consigliabile essere in grado di ruotare il rettangolo da ritagliare.

## <a name="explore-the-sample-code"></a>Esplorare il codice di esempio

È possibile ruotare il rettangolo per il viso a livello di programmazione utilizzando l'attributo HeadPose. Se si specifica questo attributo quando rileva i volti (vedere [come rilevare i visi](HowtoDetectFacesinImage.md)), sarà possibile eseguire una query in un secondo momento. Il metodo seguente dal [WPF viso di servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) app accetta un elenco dei **DetectedFace** degli oggetti e restituisce un elenco di **[viso](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** oggetti. **Viso** di seguito è una classe personalizzata che archivi di viso dati, inclusi le coordinate del rettangolo aggiornato. Nuovi valori vengono calcolati per **superiore**, **a sinistra**, **larghezza**, e **altezza**e un nuovo campo **FaceAngle**specifica la rotazione.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

## <a name="display-the-updated-rectangle"></a>Visualizzare il rettangolo aggiornato

A questo punto, è possibile usare l'oggetto restituito **viso** gli oggetti nella visualizzazione. Le righe seguenti dal [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) Mostra come il nuovo rettangolo viene eseguito il rendering da questi dati:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="next-steps"></a>Passaggi successivi

Vedere le [WPF viso di servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) app su GitHub per un esempio pratico di rettangoli ruotata. In alternativa, vedere la [Face API HeadPose esempio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) app, che registra l'attributo HeadPose in tempo reale per rilevare i vari spostamenti head (nodding, agitando).