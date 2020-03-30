---
title: Confronto tra l'indicizzatore video e i predefiniti di Servizi multimediali di Azure 3
description: Questo articolo confronta le funzionalità dell'indicizzatore video e le impostazioni predefinite di Servizi multimediali di Azure.This article compares Video Indexer capabilities and Azure Media Services v3 presets.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: dcfc6ea4afe23424e72c625518356be52f62bc81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602182"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Confrontare i set di impostazioni di Servizi multimediali di Azure v3 e di Video Indexer 

Questo articolo mette a confronto le funzionalità delle **API di Video Indexer** e delle **API di Servizi multimediali v3**. 

Attualmente, esiste una sovrapposizione tra le funzionalità offerte dalle [API dell'indicizzatore video](https://api-portal.videoindexer.ai/) e le [API di Servizi multimediali v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). La tabella seguente illustra le linee guida correnti per comprendere meglio le differenze e le analogie. 

## <a name="compare"></a>Confronto

|Funzionalità|API di Video Indexer |Set di impostazioni di analisi video e analisi audio<br/>nelle API di Servizi multimediali v3|
|---|---|---|
|Informazioni dettagliate sui contenuti multimediali|[Avanzato](video-indexer-output-json-v2.md) |[Fondamenti](../latest/intelligence-concept.md)|
|Esperienze|Vedere l'elenco completo delle funzionalità supportate: <br/> [Panoramica](video-indexer-overview.md)|Vengono restituite solo informazioni dettagliate sui video|
|Fatturazione|[Prezzi di Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Prezzi di Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformità|Per gli aggiornamenti di conformità più recenti, visitare [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e cercare "Video Indexer" per verificare se è conforme a un certificato di interesse.|Per gli aggiornamenti di conformità più recenti, visitare [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e cercare "Servizi multimediali" per verificare se è conforme a un certificato di interesse.|
|Versione di valutazione gratuita|Stati Uniti orientali|Non disponibile|
|Aree di disponibilità|Vedere [Disponibilità dei servizi cognitivi per area geografica](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Consultate [Disponibilità di Servizi multimediali per regione.](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)|

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Video Indexer](video-indexer-overview.md)

[Panoramica di Servizi multimediali v3](../latest/media-services-overview.md)
