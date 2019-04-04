---
title: Confronto tra i set di impostazioni di Video Indexer e Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo argomento mette a confronto i set di impostazioni di Video Indexer e di Servizi multimediali di Azure v3.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 041e76ccecb4dd0fe9c060681609dfb92c03ec5a
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893147"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Confrontare i set di impostazioni di Servizi multimediali di Azure v3 e di Video Indexer 

Questo articolo mette a confronto le funzionalità delle **API di Video Indexer** e delle **API di Servizi multimediali v3**. 

Attualmente è disponibile una sovrapposizione tra le funzionalità offerte dal [API indicizzatore Video](https://api-portal.videoindexer.ai/) e il [le API di servizi multimediali v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). La tabella seguente illustra le linee guida correnti per comprendere meglio le differenze e le analogie. 

## <a name="compare"></a>Confronto

|Funzionalità|API di Video Indexer |Set di impostazioni di analisi video e analisi audio<br/>nelle API di Servizi multimediali v3|
|---|---|---|
|Informazioni dettagliate sui contenuti multimediali|[Avanzato](video-indexer-output-json-v2.md) |[Nozioni fondamentali](../latest/intelligence-concept.md)|
|Esperienze|Vedere l'elenco completo delle funzionalità supportate: <br/> [Panoramica](video-indexer-overview.md)|Vengono restituite solo informazioni dettagliate sui video|
|Fatturazione|[Prezzi di servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Prezzi di servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformità|[Adeguamento di Azure](https://aka.ms/AzureCompliance)|Servizi multimediali è conforme con molte certificazioni. Aprire il file [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e cercare "Servizi multimediali" per vedere se è conforme con un certificato specifico.|
|Versione di valutazione gratuita|Stati Uniti orientali|Non disponibile|
|Disponibilità |Stati Uniti occidentali, Asia orientale, Europa settentrionale|Vedere [Stato di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di indicizzatore video](video-indexer-overview.md)

[Panoramica di servizi multimediali v3](../latest/media-services-overview.md)
