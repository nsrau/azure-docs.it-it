---
title: Eseguire la migrazione da Windows Azure Media Encoder a Media Encoder Standard . Documenti Microsoft
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
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: e75e3f3eecf6c34050aeaa7fe387fffb0de58a74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513202"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Eseguire la migrazione da Windows Azure Media Encoder a Media Encoder StandardMigrate from Windows Azure Media Encoder to Media Encoder Standard

In questo articolo vengono illustrati i passaggi per la migrazione dal processore multimediale legacy Windows Azure Media Encoder (WAME) (che viene ritirato) al processore multimediale Standard media Encoder. Per le date di pensionamento, vedere questo argomento relativo ai [componenti legacy.](legacy-components.md)

Durante la codifica dei file con WAME, `H264 Adaptive Bitrate MP4 Set 1080p`i clienti in genere utilizzavano una stringa preimpostata con nome, ad esempio . Per eseguire la migrazione, il codice deve essere aggiornato per utilizzare il processore multimediale Media Encoder `H264 Multiple Bitrate 1080p` **Standard** anziché WAME e una delle impostazioni predefinite di sistema [equivalenti](media-services-mes-presets-overview.md) come . 

## <a name="migrating-to-media-encoder-standard"></a>Migrazione a Media Encoder Standard

Di seguito è riportato un tipico esempio di codice c'è che usa il componente legacy. 

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

Se è stato creato un predefinito di codifica per WAME utilizzando il relativo schema, esiste [uno schema equivalente per Media Encoder Standard](media-services-mes-schema.md).

## <a name="known-differences"></a>Differenze note 

Media Encoder Standard è più robusto, affidabile, ha prestazioni migliori e produce un output di qualità migliore rispetto al codificatore WAME legacy. Inoltre: 

* Media Encoder Standard produce file di output con una convenzione di denominazione diversa da WAME.
* Media Encoder Standard produce elementi quali file contenenti i [metadati](media-services-input-metadata-schema.md) del file di input e i metadati dei file di [output.](media-services-output-metadata-schema.md)
* Come documentato nella pagina dei [prezzi](https://azure.microsoft.com/pricing/details/media-services/#encoding) (specialmente nella sezione FAQ), quando si codificano i video utilizzando Media Encoder Standard, si ottiene fatturato in base alla durata dei file prodotti come output. Con WAME, verrai fatturato in base alle dimensioni dei file video di input e dei file video di output.

## <a name="need-help"></a>Richiesta di assistenza

È possibile aprire un ticket di supporto passando a [Nuova richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Passaggi successivi

* [Componenti legacy](legacy-components.md)
* [Pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/#encoding)
