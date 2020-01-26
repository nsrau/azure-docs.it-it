---
title: Set di impostazioni per la codifica compatibile con il contenuto-servizi multimediali di Azure |
description: Questo articolo illustra la codifica compatibile con i contenuti in Servizi multimediali di Microsoft Azure V3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 34b4386d536a9845c8a48e7e54612548367b52bc
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759347"
---
# <a name="a-preset-for-content-aware-encoding"></a>Set di impostazioni per la codifica compatibile con il contenuto

Per preparare il contenuto per la distribuzione tramite [streaming a bitrate adattivo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), il video deve essere codificato a più velocità in bit (da alto a basso). Per garantire una riduzione del livello di qualità, poiché la velocità in bit è ridotta, è possibile risolvere il video. In questo modo si ottiene una cosiddetta scala di codifica, ovvero una tabella di risoluzioni e bitrate; vedere i [set di impostazioni di codifica predefiniti di](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)servizi multimediali.

## <a name="overview"></a>Overview

L'interesse per lo superamento di un approccio con un set di impostazioni-tutti i video è aumentato dopo che è stato pubblicato il [Blog](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) di Netflix nel 2015 dicembre. Da allora, nel Marketplace sono state rilasciate più soluzioni per la codifica compatibile con i contenuti; per una panoramica, vedere [questo articolo](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) . L'idea è tenere presente il contenuto: per personalizzare o ottimizzare la scala di codifica per la complessità del singolo video. A ogni risoluzione, esiste una velocità in bit oltre la quale un aumento della qualità non è percettivo, il codificatore opera a questo valore a bitrate ottimale. Il livello successivo di ottimizzazione consiste nel selezionare le risoluzioni in base al contenuto. ad esempio, un video di una presentazione di PowerPoint non può trarre vantaggio dal passaggio inferiore a 720p. Per proseguire, è possibile eseguire l'attività del codificatore per ottimizzare le impostazioni per ogni scatto nel video. Netflix descrive [questo approccio](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) in 2018.

Nei primi 2017, Microsoft ha rilasciato il set di impostazioni di [streaming adattivo](autogen-bitrate-ladder.md) per risolvere il problema della variabilità nella qualità e nella risoluzione dei video di origine. I nostri clienti hanno una diversa combinazione di contenuto, alcuni a 1080p, altri a 720p e alcuni a SD e risoluzioni ridotte. Inoltre, non tutto il contenuto di origine era un mezzanino di alta qualità dei film o degli studi TV. Il set di impostazioni di streaming adattivo risolve questi problemi garantendo che la scala in bit non superi mai la risoluzione o la velocità in bit media del mezzanino di input.

Il nuovo set di impostazioni di codifica compatibile con il contenuto estende tale meccanismo, incorporando logica personalizzata che consente al codificatore di cercare il valore di velocità in bit ottimale per una determinata risoluzione, ma senza richiedere un'analisi computazionale completa. Questo set di impostazioni genera un set di MP4s allineati a GOP. Dato un contenuto di input, il servizio esegue un'analisi leggera iniziale del contenuto di input e usa i risultati per determinare il numero ottimale di livelli, le impostazioni di velocità in bit e risoluzione appropriate per il recapito tramite flusso adattivo. Questo set di impostazioni è particolarmente efficace per i video con complessità bassa e media, in cui i file di output saranno a velocità in bit inferiori rispetto al set di impostazioni di streaming adattivo, ma con una qualità che offre comunque una valida esperienza ai visualizzatori. L'output conterrà file MP4 con interfoliazione video e audio

Vedere i grafici di esempio seguenti che mostrano il confronto usando metriche di qualità come [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) e [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). L'origine è stata creata tramite la concatenazione di brevi clip di foto ad alta complessità da film e programmi TELEVISIVi, destinati a sottolineare il codificatore. Per definizione, questo set di impostazioni genera risultati che variano dal contenuto al contenuto. significa anche che, per alcuni contenuti, potrebbe non esserci una riduzione significativa della velocità in bit o miglioramento della qualità.

![Curva di distorsione della velocità (RD) con PSNR](media/cae-experimental/msrv1.png)

**Figura 1: curva della velocità di distorsione (RD) con la metrica PSNR per un'origine di complessità elevata**

![Curva di distorsione della velocità (RD) con VMAF](media/cae-experimental/msrv2.png)

**Figura 2: curva della velocità di distorsione (RD) con la metrica VMAF per un'origine di complessità elevata**

Di seguito sono riportati i risultati per un'altra categoria di contenuto di origine, in cui il codificatore è stato in grado di determinare che l'input era di scarsa qualità (molti artefatti di compressione a causa della velocità in bit ridotta). Si noti che con il set di impostazioni compatibile con il contenuto, il codificatore ha deciso di produrre un solo livello di output, a una velocità in bit abbastanza bassa, in modo che la maggior parte dei client sarebbe in grado di riprodurre il flusso senza bloccarsi.

![Curva RD con PSNR](media/cae-experimental/msrv3.png)

**Figura 3: curva RD con PSNR per l'input di bassa qualità (a 1080p)**

![Curva RD con VMAF](media/cae-experimental/msrv4.png)

**Figura 4: curva RD con VMAF per l'input di bassa qualità (a 1080p)**

## <a name="use-the-experimental-preset"></a>Usa il set di impostazioni sperimentale

È possibile creare trasformazioni che usano questo set di impostazioni come indicato di seguito. Se si usa un'esercitazione come [questa](stream-files-tutorial-with-api.md), è possibile aggiornare il codice nel modo seguente:

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
> Gli algoritmi sottostanti sono soggetti ad ulteriori miglioramenti. Nel tempo è possibile apportare modifiche alla logica usata per generare scale a bitrate, con l'obiettivo di fornire un algoritmo affidabile e adattabile a un'ampia gamma di condizioni di input. I processi di codifica che usano questo set di impostazioni verranno comunque fatturati in base ai minuti di output e l'asset di output può essere recapitato dagli endpoint di streaming in protocolli quali DASH e HLS.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come questa nuova opzione di ottimizzazione dei video, è possibile provarla. È possibile inviare commenti e suggerimenti usando i collegamenti alla fine di questo articolo.
