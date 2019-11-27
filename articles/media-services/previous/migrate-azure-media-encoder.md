---
title: Eseguire la migrazione da Azure Media Encoder a Media Encoder Standard | Microsoft Docs
description: In questo argomento viene illustrato come eseguire la migrazione da Azure Media Encoder a Media Encoder Standard processore di contenuti multimediali.
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
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: e9c83a25147696b5e492241a191b3104df001c7c
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424030"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Eseguire la migrazione da Azure Media Encoder a Media Encoder Standard

Questo articolo illustra i passaggi per eseguire la migrazione dal processore di contenuti multimediali legacy Azure Media Encoder (AME), che è in fase di ritiro il 1 ° marzo 2020 al processore di contenuti multimediali Media Encoder Standard.  

Quando si codificano i file con AME, i clienti utilizzano in genere una stringa del set di impostazioni denominata, ad esempio `H264 Adaptive Bitrate MP4 Set 1080p`. Per eseguire la migrazione, è necessario aggiornare il codice per usare il processore di contenuti multimediali **Media Encoder standard** anziché AME e uno dei [set di impostazioni di sistema](media-services-mes-presets-overview.md) equivalenti, ad esempio `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Migrazione a Media Encoder Standard

Ecco un esempio di C# codice tipico che usa il processore di contenuti multimediali legacy. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
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

Se è stato creato un set di impostazioni di codifica personalizzato per AME usando il relativo schema, esiste uno [schema equivalente per Media Encoder standard](media-services-mes-schema.md). Per domande su come eseguire il mapping delle impostazioni precedenti al nuovo codificatore, contattare Microsoft tramite mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Differenze note 

Media Encoder Standard è più affidabile, affidabile, offre prestazioni migliori e produce un output di qualità migliore rispetto al codificatore AME legacy. Eseguire anche queste operazioni: 

* Media Encoder Standard produce file di output con una convenzione di denominazione diversa da AME.
* Media Encoder Standard produce elementi quali file contenenti i metadati del [file di input](media-services-input-metadata-schema.md) e i [metadati dei file di output](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Passaggi successivi

* [Componenti legacy](legacy-components.md)
* [Pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/#encoding)
