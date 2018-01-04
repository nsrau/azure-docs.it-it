---
title: Inviare processi di ritaglio di Azure Media Clipper | Microsoft Docs
description: Passaggi per inviare processi di ritaglio da Azure Media Clipper
services: media-services
keywords: clip;clip secondaria;codifica;multimediale
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8372c405087c0dc7a000a65265bb99c395c3a8d6
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Inviare processi di ritaglio da Azure Media Clipper
Per gestire l'invio di processi di ritaglio, Azure Media Clipper richiede l'implementazione di un metodo **submitSubclipCallback**. Questa funzione è necessaria per implementare un'operazione POST HTTP per l'output di Clipper in un servizio Web. Nel servizio Web sarà possibile inviare il processo di codifica. L'output di Clipper è un set di impostazioni di codifica di Media Encoder Standard per i processi con rendering o il payload dell'API REST per chiamate di filtri manifesto dinamico. Questo modello pass-through è necessario perché le credenziali dell'account Servizi multimediali non sono sicure nel browser del client.

Il diagramma di sequenza seguente illustra il flusso di lavoro tra il browser client, il servizio Web e Servizi multimediali di Azure: ![Diagramma di sequenza di Azure Media Clipper](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

Nel diagramma precedente le quattro entità sono: il browser dell'utente finale, il servizio Web, l'endpoint rete per la distribuzione di contenuti di hosting delle risorse di Clipper e Servizi multimediali di Azure. Quando l'utente finale accede a una pagina Web, la pagina ottiene le risorse JavaScript e CSS di Clipper dall'edpoint rete per la distribuzione di contenuti di hosting. L'utente finale configura il processo di ritaglio o la chiamata di creazione del filtro manifesto dinamico dal browser. Quando l'utente finale invia il processo o la chiamata di creazione del filtro, il browser inserisce il payload del processo in un servizio Web da distribuire. Questo servizio Web invia infine il processo di ritaglio o la chiamata di creazione del filtro a Servizi multimediali di Azure usando le credenziali dell'account servizi multimediali.

L'esempio di codice seguente illustra un metodo **submitSubclipCallback** di esempio. In questo metodo si implementa l'operazione POST HTTP per il set di impostazioni di codifica Media Encoder Standard. Se l'operazione POST ha esito positivo (**result**), l'oggetto **Promise** viene risolto. In caso contrario, viene rifiutato con i dettagli dell'errore.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, retuns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
L'output dell'invio del processo è il set di impostazioni di codifica Media Encoder Standard per un processo con rendering o il payload dell'API REST per i filtri manifesto dinamico.

## <a name="submitting-encoding-job-to-create-video"></a>Invio di un processo di codifica per creare video
È possibile inviare un processo di codifica Media Encoder Standard per creare una clip video con fotogrammi accurati. Il processo di codifica produce video con rendering, un nuovo file MP4 frammentato.

Il contratto di output del processo per il ritaglio con rendering è un oggetto JSON con le proprietà seguenti:

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to sumit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

Per eseguire il processo di codifica, inviare il processo di codifica Media Encoder Standard col il set di impostazioni associato. Vedere questo articolo per informazioni dettagliate sull'invio di processi di codifica tramite [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) o [API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Creazione rapida di clip video senza codifica
In alternativa alla creazione di un processo di codifica è possibile usare Azure Media Clipper per creare i filtri manifesto dinamico. I filtri non richiedono la codifica e possono essere creati rapidamente in quanto non viene creato un nuovo asset. Il contratto di output per un ritaglio con filtro è un oggetto JSON con le proprietà seguenti:

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

Per inviare la chiamata REST per creare un filtro manifesto dinamico, inviare il payload del filtro associato tramite [API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).