---
title: Sperimentale un set di impostazioni per la codifica compatibile con contenuto - Azure | Microsoft Docs
description: Questo articolo viene illustrata la codifica compatibile con il contenuto in servizi multimediali di Azure
services: media-services
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: sethm
ms.custom: ''
ms.openlocfilehash: b0b5a74a6ca0085f945075a8896c05a724ff062c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717953"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Sperimentale set di impostazioni per la codifica compatibile con il contenuto

Per preparare il contenuto per il recapito tramite [streaming a bitrate adattivo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), video deve essere codificato per più velocità in bit-(in alto al basso). Per assicurare una riduzione del funzionamento della qualità, come la velocità in bit si riducono così è la risoluzione del video. Ciò comporta un cosiddetto ladder codifica: una tabella di risoluzioni e velocità in bit; visualizzare i servizi multimediali [predefiniti di codifica predefiniti](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

## <a name="overview"></a>Panoramica

Crescente interesse verso oltre un approccio di un set di impostazioni-più adatta alle-all-video di procedure aumentato dopo la pubblicazione di Netflix loro [blog](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) a dicembre 2015. Da allora, sono state rilasciate più soluzioni per la codifica compatibile con il contenuto nel marketplace; visualizzare [questo articolo](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) per una panoramica. L'idea è da tenere presente del contenuto, personalizzare o ottimizzare la codifica ladder la complessità del video singoli. Ogni risoluzione, è una velocità in bit oltre il quale un aumento della qualità non è perceptive: il codificatore opera a questo valore ottimale a velocità in bit. Il livello successivo di ottimizzazione consiste nel selezionare le soluzioni basate sul contenuto, ad esempio, un video di presentazione di PowerPoint non trae vantaggio dalla costante di sotto 720p. Procede, il codificatore può essere assegnato il compito per ottimizzare le impostazioni per ciascuna ripresa all'interno del video. Netflix descritto [questo approccio](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) nel 2018.

All'inizio del 2017, Microsoft ha rilasciato il [lo Streaming adattivo](autogen-bitrate-ladder.md) preimpostato per risolvere il problema della variabilità della qualità e la risoluzione dei video di origine. I clienti hanno riscontrato una combinazione di variabile di contenuto, alcuni a 1080p, altri utenti a 720p e pochi alla SD e le risoluzioni più basse. Inoltre, non tutto il contenuto di origine è stata mezzanines di qualità elevata dalla registrazione in Studio TV o pellicola. Il flusso adattivo indirizzi predefiniti questi problemi assicurandosi che le coppie bitrate non superi mai la risoluzione o la velocità in bit media dell'input in formato intermedio.

Contenuto compatibile con set di impostazioni codifica sperimentale estende tale meccanismo, incorporando la logica personalizzata che consenta il codificatore di cercare il valore ottimale a velocità in bit per la risoluzione specificata, ma senza richiedere un'analisi approfondita computazionale. Il risultato finale è che questo nuovo set di impostazioni genera un output con velocità in bit inferiore rispetto a set di impostazioni flusso adattivo, ma con una qualità più elevata. Vedere i grafici di esempio seguenti che illustrano il confronto con le metriche di qualità, ad esempio [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) e [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). L'origine è stato creato concatenando brevi clip di catture di complessità alta dal film e programmi TV, destinato a stress al codificatore. Per definizione, questo set di impostazioni produce risultati che variano dal contenuto agli – significa anche che per la parte del contenuto, potrebbe non esserci riduzione significativa delle velocità in bit o miglioramento della qualità.

![Curva di frequenza-distorsione (RD) utilizzando PSNR](media/cae-experimental/msrv1.png)

**Figura 1: Curva di frequenza-distorsione (RD) con PSNR metriche per l'origine di complessità alta**

![Curva di frequenza-distorsione (RD) utilizzando VMAF](media/cae-experimental/msrv2.png)

**Figura 2: Curva di frequenza-distorsione (RD) con VMAF metriche per l'origine di complessità alta**

Il set di impostazioni è attualmente ottimizzato per elevata complessità, i video di origine di qualità elevata (film, programmi TV). Lavoro è in corso per adattarsi al contenuto di complessità bassa (ad esempio, le presentazioni di PowerPoint), nonché i video di qualità inferiori. Il set di impostazioni Usa lo stesso set di risoluzioni anche come set di impostazioni lo Streaming adattivo. Microsoft sta lavorando in metodi per selezionare il set minimo di soluzioni basate sul contenuto. Come indicato di seguito sono i risultati per un'altra categoria di contenuto di origine, in cui il codificatore è stato in grado di determinare che l'input è di scarsa qualità (molti elementi di compressione a causa di bassa velocità in bit). Si noti che, con l'istanza sperimentale, set di impostazioni, il codificatore ha deciso di produrre un solo livello di output – una sufficientemente bassa velocità, in modo che la maggior parte dei client sarebbero in grado di riprodurre il flusso senza bloccare.

![Curva di desktop remoto con PSNR](media/cae-experimental/msrv3.png)

**Figura 3: Curva di desktop remoto con PSNR per input di bassa qualità (a 1080p)**

![Curva di desktop remoto con VMAF](media/cae-experimental/msrv4.png)

**Figura 4: Curva di desktop remoto con VMAF per input di bassa qualità (a 1080p)**

## <a name="use-the-experimental-preset"></a>Usare il set di impostazioni sperimentali

È possibile creare trasformazioni che utilizzano questo set di impostazioni come indicato di seguito. Se si usa un'esercitazione [simile al seguente](stream-files-tutorial-with-api.md), è possibile aggiornare il codice come indicato di seguito:

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new experimental preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncodingExperimental
      }
   }
};
```

> [!NOTE]
> Il prefisso "sperimentale" viene usato qui per segnalare che gli algoritmi sottostanti sono in continua evoluzione. Punto possibile e saranno soggetti a modifiche nel corso del tempo per la logica usata per la generazione di scale a velocità in bit, con l'obiettivo di convergenza su un algoritmo che è solido e si adatta a un'ampia gamma di condizioni di input. Codifica dei processi mediante questo set di impostazioni verrà comunque essere minuti di output in base alle fatturati e l'asset di output può essere recapitato dall'endpoint di streaming in protocolli, ad esempio DASH e HLS.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso su questa nuova opzione di ottimizzazione dei video, ti invitiamo a provare. È possibile inviare commenti e suggerimenti tramite i collegamenti alla fine di questo articolo o coinvolgere più direttamente all'indirizzo <amsved@microsoft.com>.
