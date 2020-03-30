---
title: Predefinito per la codifica in grado di riconoscere il contenuto - Servizi multimediali di AzureA preset for content-aware encoding - Azure Media Services
description: Questo articolo illustra la codifica in grado di riconoscere il contenuto in Servizi multimediali di Microsoft Azure versione 3.This article discusses content-aware encoding in Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2020
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 3ea6c4226a59ba020a477cc5811033ff3dc3c2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772132"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Utilizzare il predefinito di codifica in grado di riconoscere il contenuto per trovare il valore di bitrate ottimale per una determinata risoluzione

Per preparare i contenuti per la distribuzione mediante [streaming a bitrate adattivo,](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)il video deve essere codificato a più velocità in bit (dal più alto al più basso). Ciò garantisce una degradazione normale della qualità, poiché il bitrate viene abbassato, così come la risoluzione del video. Tale codifica a velocità in bit multipla fa uso di una cosiddetta scala di codifica – una tabella di risoluzioni e velocità in bit, vedere i predefiniti di [codifica incorporati di](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)Servizi multimediali .

Dovresti essere a conoscenza del contenuto che stai elaborando e personalizzare/ottimizzare la scala di codifica in base alla complessità del singolo video. Ad ogni risoluzione, c'è un bitrate oltre il quale qualsiasi aumento di qualità non è percettivo - l'encoder opera a questo valore di bitrate ottimale. Il livello successivo di ottimizzazione consiste nel selezionare le risoluzioni in base al contenuto, ad esempio un video di una presentazione di PowerPoint non trae vantaggio dall'andare al di sotto di 720p. Andando oltre, l'encoder può essere incaricato di ottimizzare le impostazioni per ogni ripresa all'interno del video. 

Il preset [di adaptive Streaming](autogen-bitrate-ladder.md) di Microsoft risolve parzialmente il problema della variabilità nella qualità e nella risoluzione dei video sorgente. I nostri clienti hanno un mix variabile di contenuti, alcuni a 1080p, altri a 720p, e alcuni a SD e risoluzioni inferiori. Inoltre, non tutti i contenuti di origine sono soppalchi di alta qualità da studi cinematografici o televisivi. Il preset Adaptive Streaming risolve questi problemi assicurando che la scala bitrate non superi mai la risoluzione o la velocità in bit media del mezzanino di input. Tuttavia, questa impostazione non esamina le proprietà di origine diverse dalla risoluzione e dalla velocità in bit.

## <a name="the-content-aware-encoding"></a>La codifica in grado di riconoscere il contenuto 

Il predefinito di codifica in grado di riconoscere il contenuto estende il meccanismo di "streaming bitrate adattivo", incorporando la logica personalizzata che consente al codificatore di cercare il valore di bitrate ottimale per una determinata risoluzione, ma senza richiedere un'analisi computazionale estesa. Questa preimpostazione produce un set di MP4 allineati a GOP. Dato qualsiasi contenuto di input, il servizio esegue un'analisi iniziale a peso leggero del contenuto di input e usa i risultati per determinare il numero ottimale di layer, le impostazioni di distribuzione e risoluzione appropriate per la distribuzione tramite streaming adattivo. Questa preimpostazione è particolarmente efficace per i video con bassa e media complessità, in cui i file di output saranno a velocità in bit inferiori rispetto al preset Adaptive Streaming ma con una qualità che offre comunque una buona esperienza agli spettatori. L'uscita conterrà file MP4 con interfoliazione video e audio

I grafici di esempio seguenti mostrano il confronto utilizzando metriche di qualità come [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) e [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). La sorgente è stata creata concatenando brevi clip di riprese ad alta complessità da film e programmi TV, destinati a sottolineare l'encoder. Per definizione, questa preimpostazione produce risultati che variano da contenuto a contenuto - significa anche che per alcuni contenuti, non ci può essere una riduzione significativa del bitrate o miglioramento della qualità.

![Curva di distorsione del tasso (RD) con PSNR](media/content-aware-encoding/msrv1.png)

**Figura 1: curva di distorsione del tasso (RD) utilizzando la metrica PSNR per la fonte ad alta complessità**

![Curva di distorsione del tasso (RD) con VMAF](media/content-aware-encoding/msrv2.png)

**Figura 2: curva di distorsione del tasso (RD) utilizzando la metrica VMAF per la fonte ad alta complessità**

Di seguito sono riportati i risultati per un'altra categoria di contenuto di origine, in cui il codificatore è stato in grado di determinare che l'input era di scarsa qualità (molti artefatti di compressione a causa della velocità in bit bassa). Si noti che con il predefinito content-aware, il codificatore ha deciso di produrre un solo livello di output, con un bitrate sufficientemente basso in modo che la maggior parte dei client sarebbe in grado di riprodurre il flusso senza bloccarsi.

![Curva RD con PSNR](media/content-aware-encoding/msrv3.png)

**Figura 3: curva RD con PSNR per input di bassa qualità (a 1080p)**

![Curva RD con VMAF](media/content-aware-encoding/msrv4.png)

**Figura 4: curva RD con VMAF per input di bassa qualità (a 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Come utilizzare il predefinito di codifica in grado di riconoscere il contenuto 

È possibile creare trasformazioni che utilizzano questo predefinito come indicato di seguito. 

> [!TIP]
> Vedere la sezione [Passaggi successivi](#next-steps) per esercitazioni sull'utilizzo degli output di tranform. L'asset di output può essere recapitato dagli endpoint di streaming di Servizi multimediali in protocolli quali MPEG-DASH e HLS (come illustrato nelle esercitazioni).


```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> I processi `ContentAwareEncoding` di codifica che utilizzano il predefinito vengono fatturati in base ai minuti di output. 

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Caricare, codificare e trasmettere video in streaming con Servizi multimediali v3](stream-files-tutorial-with-api.md)
* [Esercitazione: Codificare un file remoto in base all'URL e trasmettere il video - RESTTutorial: Encode a remote file based on URL and stream the video - REST](stream-files-tutorial-with-rest.md)
* [Esercitazione: Codificare un file remoto in base all'URL e trasmettere il video - CLI](stream-files-cli-quickstart.md)
* [Esercitazione: Codificare un file remoto in base all'URL e trasmettere il video - .NETTutorial: Encode a remote file based on URL and stream the video - .NET](stream-files-dotnet-quickstart.md)
* [Esercitazione: Codificare un file remoto in base all'URL e trasmettere il video - Node.jsTutorial: Encode a remote file based on URL and stream the video - Node.js](stream-files-nodejs-quickstart.md)
