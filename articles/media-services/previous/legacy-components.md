---
title: Componenti legacy di servizi multimediali di Azure | Microsoft Docs
description: Questo argomento illustra i componenti legacy di servizi multimediali di Azure.
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
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 592fd8901fc7f8a82a390f3c125c712ef5829a52
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083721"
---
# <a name="azure-media-services-legacy-components"></a>Componenti legacy di servizi multimediali di Azure

Nel corso del tempo sono stati apportati miglioramenti e miglioramenti costanti ai componenti di servizi multimediali. Di conseguenza, alcuni componenti legacy sono stati ritirati. È possibile trovare le istruzioni su come eseguire la migrazione dell'applicazione dal componente legacy a un componente corrente negli articoli seguenti.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Piani di ritiro dei componenti legacy e informazioni aggiuntive sulla migrazione

Microsoft sta annunciando la deprecazione dei processori di contenuti multimediali *Windows Azure Media Encoder* (biaigi) e *Azure Media Encoder* (AME). Questi processori verranno ritirati il 30 novembre 2019.

* [Eseguire la migrazione da Azure Media Encoder Windows a Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Eseguire la migrazione da Azure Media Encoder a Media Encoder Standard](migrate-azure-media-encoder.md)

Viene inoltre annunciato il ritiro dei seguenti Analisi Servizi multimediali processori di contenuti multimediali: 
 
|Nome processore di contenuti multimediali|Data di ritiro|Note aggiuntive|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1 gennaio 2020|Questo processore di contenuti multimediali verrà sostituito da [servizi multimediali di Azure video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Per altre informazioni, vedere [eseguire la migrazione da Azure Media Indexer 2 a servizi multimediali di Azure video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 ° ottobre di 2020|Questo processore di contenuti multimediali verrà sostituito da [servizi multimediali di Azure video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Per altre informazioni, vedere [eseguire la migrazione da Azure Media Indexer a servizi multimediali di Azure video Indexer](migrate-indexer-v1-v2.md)

## <a name="next-steps"></a>Passaggi successivi

[Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3](../latest/migrate-from-v2-to-v3.md)
