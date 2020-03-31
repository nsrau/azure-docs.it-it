---
title: Eseguire la migrazione da Azure Media Encoder a Media Encoder Standard . Documenti Microsoft
description: Questo argomento illustra come eseguire la migrazione da Azure Media Encoder al processore multimediale Standard del codificatore multimediale.
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
ms.openlocfilehash: f8fe1b13db6473e80f0d7cdc638b775a0c8062c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513502"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Eseguire la migrazione da Azure Media Encoder a Media Encoder StandardMigrate from Azure Media Encoder to Media Encoder Standard

Questo articolo illustra i passaggi per la migrazione dal processore multimediale AME (Azure Media Encoder) legacy (in fase di ritiro) al processore multimediale Standard del codificatore multimediale. Per le date di pensionamento, vedere questo argomento relativo ai [componenti legacy.](legacy-components.md)

Quando si codificano file con AME, i `H264 Adaptive Bitrate MP4 Set 1080p`clienti in genere utilizzavano una stringa preimpostata con nome, ad esempio . Per eseguire la migrazione, il codice deve essere aggiornato per utilizzare il processore multimediale Media Encoder `H264 Multiple Bitrate 1080p` **Standard** anziché AME e una delle impostazioni predefinite di sistema [equivalenti](media-services-mes-presets-overview.md) come . 

## <a name="migrating-to-media-encoder-standard"></a>Migrazione a Media Encoder Standard

Di seguito è riportato un tipico esempio di codice c'è che usa il processore multimediale legacy. 

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

Ecco la versione aggiornata che utilizza Media Encoder Standard.

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

Se è stato creato un predefinito di codifica per AME utilizzando il relativo schema, esiste [uno schema equivalente per Media Encoder Standard](media-services-mes-schema.md). Se avete domande su come mappare le impostazioni più vecchie al nuovo encoder, si prega di contattarci tramitemailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Differenze note 

Media Encoder Standard è più robusto, affidabile, ha prestazioni migliori e produce un output di qualità migliore rispetto al codificatore AME legacy. Inoltre: 

* Media Encoder Standard produce file di output con una convenzione di denominazione diversa da quella di AME.
* Media Encoder Standard produce elementi quali file contenenti i [metadati](media-services-input-metadata-schema.md) del file di input e i metadati dei file di [output.](media-services-output-metadata-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* [Componenti legacy](legacy-components.md)
* [Pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/#encoding)
