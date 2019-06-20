---
title: Usare l'attributo HeadPose
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'attributo HeadPose per ruotare automaticamente il rettangolo del viso o rilevare movimenti della testa in un feed video.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 168b4fce873206e39a32a83da3dc5509b431d6a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058586"
---
# <a name="use-the-headpose-attribute"></a>Usare l'attributo HeadPose

In questa guida verrà illustrato come usare l'attributo HeadPose di un volto rilevato per abilitare alcuni scenari chiave.

## <a name="rotate-the-face-rectangle"></a>Ruotare il rettangolo del viso

Il rettangolo del viso, restituito con ogni viso rilevato, contrassegna la posizione e le dimensioni del viso nell'immagine. Per impostazione predefinita, il rettangolo è sempre allineato all'immagine (i relativi lati sono verticali e orizzontali). Questo comportamento può risultare inefficiente per inquadrare i visi angolati. Nei casi in cui si intende ritagliare i visi in un'immagine a livello di programmazione, è preferibile poter ruotare il rettangolo da ritagliare.

L'app di esempio [WPF Viso di Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) usa l'attributo HeadPose per ruotare i rettangoli del viso rilevati.

### <a name="explore-the-sample-code"></a>Esplorare il codice di esempio

Per ruotare il rettangolo del viso a livello di programmazione, è possibile usare l'attributo HeadPose. Se si specifica questo attributo quando si rilevano i visi (vedere [Ottenere i dati di rilevamento viso](HowtoDetectFacesinImage.md)), sarà possibile cercarlo in un secondo momento. Il metodo seguente dell'app [WPF Viso di Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) accetta un elenco di oggetti **DetectedFace** e restituisce un elenco di oggetti **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** . In questo caso, **Face** è una classe personalizzata che archivia i dati del viso, incluse le coordinate aggiornate del rettangolo. Vengono calcolati nuovi valori per **top**, **left**, **width** e **height**, mentre un nuovo campo **FaceAngle** specifica la rotazione.

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

### <a name="display-the-updated-rectangle"></a>Visualizzare il rettangolo aggiornato

Da qui, è possibile usare gli oggetti **Face** restituiti nella visualizzazione. Le righe seguenti del file [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) mostra il rendering del nuovo rettangolo a partire da questi dati:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Rilevare i movimenti della testa

È possibile rilevare i movimenti della testa, quando ad esempio si annuisce o si scuote la testa, tenendo traccia in tempo reale delle modifiche di HeadPose. È possibile usare questa funzionalità come rilevatore di vitalità personalizzato.

Per rilevamento della vitalità si intende l'attività che consente di determinare se un soggetto è una persona reale o e non un'immagine o una rappresentazione video. Un rilevatore di movimenti della testa può essere utile per verificare la vitalità, in particolare in contrapposizione alla rappresentazione dell'immagine di una persona.

> [!CAUTION]
> Per rilevare i movimenti della testa in tempo reale, è necessario chiamare l'API Viso con una frequenza elevata (più di una volta al secondo). Se si dispone di una sottoscrizione con livello gratuito (f0), questa operazione non è possibile. Se si dispone di una sottoscrizione a pagamento, assicurarsi di aver calcolato i costi associati all'esecuzione di chiamate API rapide per il rilevamento dei movimenti della testa.

Per un esempio di utilizzo del rilevamento dei movimenti della testa, vedere l'[esempio HeadPose dell'API Viso](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) su GitHub.

## <a name="next-steps"></a>Passaggi successivi

Per un esempio di utilizzo dei rettangoli del viso rilevati, vedere l'app [WPF Viso di Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) su GitHub. In alternativa, vedere l'app dell'[esempio HeadPose dell'API Viso](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples), che tiene traccia dell'attributo HeadPose in tempo reale per rilevare i movimenti della testa.