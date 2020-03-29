---
title: Componenti legacy di Servizi multimediali di Azure Documenti Microsoft
description: Questo argomento illustra i componenti legacy di Servizi multimediali di Azure.This topic discusses Azure Media Services legacy components.
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
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921080"
---
# <a name="azure-media-services-legacy-components"></a>Componenti legacy di Servizi multimediali di AzureAzure Media Services legacy components

Nel corso del tempo, ci sono stati miglioramenti e miglioramenti costanti ai componenti di Media Service. Di conseguenza, alcuni componenti legacy sono stati ritirati. Le istruzioni su come eseguire la migrazione dell'applicazione dal componente legacy a un componente corrente sono disponibili negli articoli seguenti.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Piani di pensionamento dei componenti legacy e linee guida per la migrazione

Stiamo annunciando la deprecazione dei processori multimediali Windows *Azure Media Encoder* (WAME) e Azure Media Encoder *(AME).* Questi processori sono in pensione il 31 marzo 2020.

* [Eseguire la migrazione da Windows Azure Media Encoder a Media Encoder StandardMigrate from Windows Azure Media Encoder to Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Eseguire la migrazione da Azure Media Encoder a Media Encoder StandardMigrate from Azure Media Encoder to Media Encoder Standard](migrate-azure-media-encoder.md)

Annunciamo anche il ritiro dei seguenti processori multimediali di Media Analytics: 
 
|Nome processore di contenuti multimediali|Data di ritiro|Note aggiuntive|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1 gennaio 2020|Questo processore multimediale viene sostituito [dall'indicizzatore video di Servizi multimediali](https://docs.microsoft.com/azure/media-services/video-indexer/)di Azure. Per altre informazioni, vedere [Eseguire la migrazione dall'indicizzatore multimediale di Azure 2 all'indicizzatore video di Servizi multimediali di Azure.For](migrate-indexer-v1-v2.md)more information, see Migrate from Azure Media Indexer 2 to Azure Media Services Video Indexer .|
|[Azure Media Indexer](media-services-index-content.md)|1o marzo 2023|Questo processore multimediale viene sostituito [dall'indicizzatore video di Servizi multimediali](https://docs.microsoft.com/azure/media-services/video-indexer/)di Azure. Per altre informazioni, vedere [Eseguire la migrazione dall'indicizzatore multimediale di Azure all'indicizzatore video di Servizi multimediali](migrate-indexer-v1-v2.md) di AzureFor more information, see Migrate from Azure Media Indexer to Azure Media Services Video Indexer|
|[Rilevamento del movimento](media-services-motion-detection.md)|1o giugno 2020|Nessun piano di sostituzione in questo momento.|
|[Riepilogo video](media-services-video-summarization.md)|1o giugno 2020|Nessun piano di sostituzione in questo momento.|
|[Riconoscimento ottico dei caratteri video](media-services-video-optical-character-recognition.md)|1o giugno 2020|Questo processore multimediale viene sostituito [dall'indicizzatore video di Servizi multimediali](https://docs.microsoft.com/azure/media-services/video-indexer/)di Azure. Considerare inoltre l'uso [dell'API di Servizi multimediali di Azure.](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept) <br/>Vedere Confronto tra i predefiniti di [Servizi multimediali di Azure e l'indicizzatore video](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Rilevamento facciale](media-services-face-and-emotion-detection.md)|1o giugno 2020|Questo processore multimediale viene sostituito [dall'indicizzatore video di Servizi multimediali](https://docs.microsoft.com/azure/media-services/video-indexer/)di Azure. Considerare inoltre l'uso [dell'API di Servizi multimediali di Azure.](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept) <br/>Vedere Confronto tra i predefiniti di [Servizi multimediali di Azure e l'indicizzatore video](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Moderatore dei contenuti](media-services-content-moderation.md)|1o giugno 2020|Questo processore multimediale viene sostituito [dall'indicizzatore video di Servizi multimediali](https://docs.microsoft.com/azure/media-services/video-indexer/)di Azure. Considerare inoltre l'uso [dell'API di Servizi multimediali di Azure.](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept) <br/>Vedere Confronto tra i predefiniti di [Servizi multimediali di Azure e l'indicizzatore video](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>Passaggi successivi

[Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3](../latest/migrate-from-v2-to-v3.md)
