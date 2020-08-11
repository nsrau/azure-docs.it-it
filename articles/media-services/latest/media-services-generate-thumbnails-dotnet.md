---
title: Come generare anteprime usando il codificatore di servizi multimediali di Azure standard con .NET
description: Questo articolo illustra come usare .NET per codificare un asset e generare anteprime allo stesso tempo usando Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/10/2020
ms.author: inhenkel
ms.openlocfilehash: f6a432ad30fa3ce24693a00478f2194caaa11ff9
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068213"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>Come generare anteprime usando il codificatore standard con .NET

È possibile usare Media Encoder Standard per generare una o più anteprime dal video di input in un formato file di immagine a scelta tra [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) e [BMP](https://en.wikipedia.org/wiki/BMP_file_format).

## <a name="recommended-reading-and-practice"></a>Letture e procedure consigliate

Si consiglia di acquisire familiarità con le trasformazioni personalizzate leggendo [come codificare con una trasformazione personalizzata-.NET](customize-encoder-presets-how-to.md).

## <a name="transform-code-example"></a>Esempio di codice di trasformazione

Nell'esempio di codice riportato di seguito viene creata solo un'anteprima.  È necessario impostare i parametri seguenti:

- **Start** : posizione nel video di input da cui iniziare a generare le anteprime. Il valore può essere nel formato ISO 8601 (ad esempio, PT05S per iniziare a 5 secondi) o un conteggio dei frame (ad esempio, 10 per iniziare al decimo frame) o un valore relativo per la durata del flusso (ad esempio, il 10% per iniziare al 10% della durata del flusso). Supporta anche una macro {Best}, che indica al codificatore di selezionare la migliore anteprima dai primi secondi del video e produrrà solo un'anteprima, indipendentemente dalle altre impostazioni per il passaggio e l'intervallo. Il valore predefinito è macro {Best}.
- **Step** : intervalli di generazione delle anteprime. Il valore può essere in formato ISO 8601 (ad esempio, PT05S per un'immagine ogni 5 secondi) o un numero di frame (ad esempio, 30 per un'immagine ogni 30 fotogrammi) o un valore relativo per la durata del flusso (ad esempio, 10% per un'immagine ogni 10% della durata del flusso). Il valore del passaggio influirà sulla prima anteprima generata, che potrebbe non corrispondere esattamente a quella specificata in fase di inizio del set di impostazioni di trasformazione. Ciò è dovuto al codificatore, che tenta di selezionare la migliore anteprima tra l'ora di inizio e la posizione del passaggio dall'ora di inizio come primo output. Poiché il valore predefinito è 10%, significa che se il flusso ha una durata prolungata, la prima anteprima generata potrebbe essere lontana da quella specificata all'ora di inizio. Provare a selezionare un valore ragionevole per step se si prevede che la prima anteprima sia prossima all'ora di inizio oppure impostare il valore di intervallo su 1 se è necessaria una sola anteprima all'ora di inizio.
- **intervallo** : posizione relativa all'ora di inizio della trasformazione del set di impostazioni nel video di input in corrispondenza della quale interrompere la generazione delle anteprime. Il valore può essere nel formato ISO 8601 (ad esempio, PT5M30S per arrestare a 5 minuti e 30 secondi dall'ora di inizio) oppure un conteggio dei frame, ad esempio 300, per arrestare il frame 1/300 dal frame all'ora di inizio. Se questo valore è 1, significa solo produrre un'anteprima all'ora di inizio o un valore relativo alla durata del flusso (ad esempio, 50% per arrestare a metà della durata del flusso dall'ora di inizio). Il valore predefinito è 100%, che significa arrestarsi alla fine del flusso.
- **Layers** : raccolta di livelli di immagine di output che devono essere generati dal codificatore.

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>Passaggi successivi
[Generare anteprime usando REST](media-services-generate-thumbnails-rest.md)
