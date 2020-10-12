---
title: Confronto tra Video Indexer e i set di impostazioni di servizi multimediali di Azure V3
description: Questo articolo confronta le funzionalità di Video Indexer e i set di impostazioni di servizi multimediali di Azure V3.
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
ms.openlocfilehash: 0961aa3e573e511a6941bc59ddc335f64799abb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047329"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Confrontare i set di impostazioni di Servizi multimediali di Azure v3 e di Video Indexer 

Questo articolo mette a confronto le funzionalità delle **API di Video Indexer** e delle **API di Servizi multimediali v3**. 

Attualmente esiste una sovrapposizione tra le funzionalità offerte dalle API [video Indexer](https://api-portal.videoindexer.ai/) e le API di [servizi multimediali V3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). La tabella seguente illustra le linee guida correnti per comprendere meglio le differenze e le analogie. 

## <a name="compare"></a>Confronto

|Funzionalità|API di Video Indexer |Set di impostazioni di analisi video e analisi audio<br/>nelle API di Servizi multimediali v3|
|---|---|---|
|Informazioni dettagliate sui contenuti multimediali|[Avanzato](video-indexer-output-json-v2.md) |[Concetti fondamentali](../latest/analyzing-video-audio-files-concept.md)|
|Esperienze|Vedere l'elenco completo delle funzionalità supportate: <br/> [Overview](video-indexer-overview.md)|Vengono restituite solo informazioni dettagliate sui video|
|Fatturazione|[Prezzi di servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Prezzi di servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformità|Per gli aggiornamenti di conformità più recenti, visitare [Offerings.pdfdi conformità di Azure ](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e cercare "video Indexer" per verificare se è conforme a un certificato di interesse.|Per gli aggiornamenti di conformità più recenti, visitare [Offerings.pdfdi conformità di Azure ](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e cercare "servizi multimediali" per verificare se è conforme a un certificato di interesse.|
|Versione di valutazione gratuita|Stati Uniti orientali|Non disponibile|
|Aree di disponibilità|Vedere [disponibilità di servizi cognitivi in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Vedere [disponibilità di servizi multimediali in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Video Indexer](video-indexer-overview.md)

[Panoramica di Servizi multimediali v3](../latest/media-services-overview.md)
