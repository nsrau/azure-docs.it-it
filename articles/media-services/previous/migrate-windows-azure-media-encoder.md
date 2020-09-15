---
title: Eseguire la migrazione da Azure Media Encoder Windows a Media Encoder Standard | Microsoft Docs
description: In questo argomento viene illustrato come eseguire la migrazione da Windows Azure Media Encoder al processore di contenuti multimediali Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 9125639ab5b563a8db7cfa5730bd9673f383071c
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90529248"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Eseguire la migrazione da Azure Media Encoder Windows a Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Questo articolo illustra i passaggi per eseguire la migrazione dal processore di contenuti multimediali Windows Azure Media Encoder (BIAIGI) Legacy (in fase di ritiro) al processore di contenuti multimediali di Media Encoder Standard. Per le date di ritiro, vedere questo argomento relativo ai [componenti legacy](legacy-components.md).

Quando si codificano i file con BIAIGI, i clienti utilizzano in genere una stringa di set di impostazioni denominata come `H264 Adaptive Bitrate MP4 Set 1080p` . Per eseguire la migrazione, è necessario aggiornare il codice per usare il processore di contenuti multimediali **Media Encoder standard** invece di biaigi e uno dei set di [impostazioni di sistema](media-services-mes-presets-overview.md) equivalenti, ad esempio `H264 Multiple Bitrate 1080p` . 

## <a name="migrating-to-media-encoder-standard"></a>Migrazione a Media Encoder Standard

Di seguito è riportato un esempio di codice C# tipico che usa il componente legacy. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Di seguito è illustrata la versione aggiornata che usa Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Scenari avanzati 

Se è stato creato un set di impostazioni di codifica personalizzato per BIAIGI usando il relativo schema, esiste uno [schema equivalente per Media Encoder standard](media-services-mes-schema.md).

## <a name="known-differences"></a>Differenze note 

Media Encoder Standard è più affidabile, affidabile, offre prestazioni migliori e produce un output di qualità migliore rispetto al codificatore BIAIGI legacy. Inoltre: 

* Media Encoder Standard produce file di output con una convenzione di denominazione diversa rispetto a BIAIGI.
* Media Encoder Standard produce elementi quali file contenenti i metadati del [file di input](media-services-input-metadata-schema.md) e i [metadati dei file di output](media-services-output-metadata-schema.md).
* Come descritto nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/#encoding) (soprattutto nella sezione delle domande frequenti), quando si codificano i video con Media Encoder standard, viene addebitato in base alla durata dei file prodotti come output. Con BIAIGI, la fatturazione viene effettuata in base alle dimensioni dei file video di input e dei file video di output.

## <a name="need-help"></a>Richiesta di assistenza

È possibile aprire un ticket di supporto passando a [Nuova richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Passaggi successivi

* [Componenti legacy](legacy-components.md)
* [Pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/#encoding)
