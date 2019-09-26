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
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: d4f4cfc005b2d5a63512245baee5230e9a26cb37
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309216"
---
# <a name="azure-media-services-legacy-components"></a>Componenti legacy di servizi multimediali di Azure

Nel corso del tempo sono stati apportati miglioramenti e miglioramenti costanti ai componenti di servizi multimediali. Di conseguenza, alcuni componenti legacy sono stati ritirati. È possibile trovare le istruzioni su come eseguire la migrazione dell'applicazione dal componente legacy a un componente corrente negli articoli seguenti.

## <a name="legacy-components-and-migration-guidance"></a>Guida alla migrazione e ai componenti legacy

Microsoft sta annunciando la deprecazione dei processori di contenuti multimediali *Windows Azure Media Encoder* (biaigi) e *Azure Media Encoder* (AME). Questi processori verranno ritirati il 30 novembre 2019.

* [Eseguire la migrazione da Azure Media Encoder Windows a Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Eseguire la migrazione da Azure Media Encoder a Media Encoder Standard](migrate-azure-media-encoder.md)

Stiamo anche annunciando la deprecazione di *Azure Media Indexer* e *Azure Media Indexer 2*. Il processore di contenuti multimediali [Azure Media Indexer](media-services-index-content.md) verrà ritirato il 1 ° ottobre del 2020. I processori di contenuti multimediali [[Azure Media Indexer 2 (anteprima)](media-services-process-content-with-indexer2.md) verranno ritirati il 1 ° gennaio 2019.  [Servizi multimediali di Azure video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) sostituisce questi processori di contenuti multimediali legacy.

* [Eseguire la migrazione da Azure Media Indexer e Azure Media Indexer 2 a servizi multimediali di Azure video Indexer](migrate-indexer-v1-v2.md).

## <a name="next-steps"></a>Passaggi successivi

[Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3](../latest/migrate-from-v2-to-v3.md)
