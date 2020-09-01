---
title: Set di impostazioni per la codifica compatibile con il contenuto-servizi multimediali di Azure
description: Questo articolo illustra la codifica compatibile con i contenuti in Servizi multimediali di Microsoft Azure V3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 853381dbccea5374806fbeadb1d29632f0525fdb
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265610"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Usare il set di impostazioni di codifica compatibile con il contenuto per trovare il valore di velocità in bit ottimale per una determinata risoluzione

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Per preparare il contenuto per la distribuzione tramite [streaming a bitrate adattivo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), il video deve essere codificato a più velocità in bit (da alto a basso). In questo modo si garantisce una riduzione del livello di qualità, perché la velocità in bit è ridotta, quindi è la risoluzione del video. Tale codifica a più bit usa una cosiddetta scala di codifica, ovvero una tabella di risoluzioni e bitrate, vedere i [set di impostazioni di codifica predefiniti di](/rest/api/media/transforms/createorupdate#encodernamedpreset)servizi multimediali.

È necessario conoscere il contenuto che si sta elaborando e personalizzare/ottimizzare la scala di codifica per la complessità del singolo video. A ogni risoluzione, esiste una velocità in bit oltre la quale un aumento della qualità non è percettivo, il codificatore opera a questo valore a bitrate ottimale. Il livello successivo di ottimizzazione consiste nel selezionare le risoluzioni in base al contenuto. ad esempio, un video di una presentazione di PowerPoint non può trarre vantaggio dal passaggio inferiore a 720p. Per proseguire, è possibile eseguire l'attività del codificatore per ottimizzare le impostazioni per ogni scatto nel video. 

Il set di impostazioni di [streaming adattivo](autogen-bitrate-ladder.md) di Microsoft risolve parzialmente il problema della variabilità nella qualità e nella risoluzione dei video di origine. I nostri clienti hanno una diversa combinazione di contenuto, alcuni a 1080p, altri a 720p e alcuni a SD e risoluzioni ridotte. Inoltre, non tutto il contenuto di origine è un mezzanino di alta qualità dei film o degli studi TV. Il set di impostazioni di streaming adattivo risolve questi problemi garantendo che la scala in bit non superi mai la risoluzione o la velocità in bit media del mezzanino di input. Tuttavia, questo set di impostazioni non esamina le proprietà di origine oltre alla risoluzione e alla velocità in bit.

## <a name="the-content-aware-encoding"></a>Codifica compatibile con il contenuto 

Il set di impostazioni di codifica compatibile con il contenuto estende il meccanismo di streaming a bitrate adattivo, incorporando logica personalizzata che consente al codificatore di ricercare il valore di velocità in bit ottimale per una determinata risoluzione, ma senza richiedere un'analisi computazionale completa. Questo set di impostazioni genera un set di MP4s allineati a GOP. Dato un contenuto di input, il servizio esegue un'analisi leggera iniziale del contenuto di input e usa i risultati per determinare il numero ottimale di livelli, le impostazioni di velocità in bit e risoluzione appropriate per il recapito tramite flusso adattivo. Questo set di impostazioni è particolarmente efficace per i video con complessità bassa e media, in cui i file di output saranno a velocità in bit inferiori rispetto al set di impostazioni di streaming adattivo, ma con una qualità che offre comunque una valida esperienza ai visualizzatori. L'output conterrà file MP4 con interfoliazione video e audio

I grafici di esempio seguenti illustrano il confronto usando metriche di qualità come [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) e [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). L'origine è stata creata tramite la concatenazione di brevi clip di foto ad alta complessità da film e programmi TELEVISIVi, destinati a sottolineare il codificatore. Per definizione, questo set di impostazioni genera risultati che variano dal contenuto al contenuto. significa anche che, per alcuni contenuti, potrebbe non esserci una riduzione significativa della velocità in bit o miglioramento della qualità.

![Curva di distorsione della velocità (RD) con PSNR](media/content-aware-encoding/msrv1.png)

**Figura 1: curva della velocità di distorsione (RD) con la metrica PSNR per un'origine di complessità elevata**

![Curva di distorsione della velocità (RD) con VMAF](media/content-aware-encoding/msrv2.png)

**Figura 2: curva della velocità di distorsione (RD) con la metrica VMAF per un'origine di complessità elevata**

Di seguito sono riportati i risultati per un'altra categoria di contenuto di origine, in cui il codificatore è stato in grado di determinare che l'input era di scarsa qualità (molti artefatti di compressione a causa della velocità in bit ridotta). Si noti che con il set di impostazioni compatibile con il contenuto, il codificatore ha deciso di produrre un solo livello di output, a una velocità in bit abbastanza bassa, in modo che la maggior parte dei client sarebbe in grado di riprodurre il flusso senza bloccarsi.

![Curva RD con PSNR](media/content-aware-encoding/msrv3.png)

**Figura 3: curva RD con PSNR per l'input di bassa qualità (a 1080p)**

![Curva RD con VMAF](media/content-aware-encoding/msrv4.png)

**Figura 4: curva RD con VMAF per l'input di bassa qualità (a 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Come usare il set di impostazioni di codifica compatibile con il contenuto 

È possibile creare trasformazioni che usano questo set di impostazioni come indicato di seguito. 

Vedere la sezione [passaggi successivi](#next-steps) per le esercitazioni che usano output trasformare. L'asset di output può essere recapitato dagli endpoint di streaming di servizi multimediali nei protocolli, ad esempio MPEG-DASH e HLS, come illustrato nelle esercitazioni.

> [!NOTE]
> Assicurarsi di usare il set di impostazioni **ContentAwareEncoding** non ContentAwareEncodingExperimental.

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
> I processi di codifica con il `ContentAwareEncoding` set di impostazioni vengono fatturati in base ai minuti di output. 
  
## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: caricare, codificare e trasmettere in streaming video con servizi multimediali V3](stream-files-tutorial-with-api.md)
* [Esercitazione: Codificare un file remoto basato su URL ed eseguire lo streaming del video - REST](stream-files-tutorial-with-rest.md)
* [Esercitazione: codificare un file remoto in base all'URL e trasmettere il video-CLI](stream-files-cli-quickstart.md)
* [Esercitazione: codificare un file remoto in base all'URL e trasmettere il video-.NET](stream-files-dotnet-quickstart.md)
* [Esercitazione: codificare un file remoto in base all'URL e trasmettere il video Node.js](stream-files-nodejs-quickstart.md)
