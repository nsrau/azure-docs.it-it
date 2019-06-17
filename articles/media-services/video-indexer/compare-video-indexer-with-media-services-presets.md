---
title: Confronto tra i set di impostazioni di Video Indexer e Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo argomento mette a confronto i set di impostazioni di Video Indexer e di Servizi multimediali di Azure v3.
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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 275178998948e357a6a72fbe5d0b3c9c01485a3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800166"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Confrontare i set di impostazioni di Servizi multimediali di Azure v3 e di Video Indexer 

Questo articolo mette a confronto le funzionalità delle **API di Video Indexer** e delle **API di Servizi multimediali v3**. 

Attualmente è disponibile una sovrapposizione tra le funzionalità offerte dal [API indicizzatore Video](https://api-portal.videoindexer.ai/) e il [le API di servizi multimediali v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). La tabella seguente illustra le linee guida correnti per comprendere meglio le differenze e le analogie. 

## <a name="compare"></a>Confronto

|Funzionalità|API di Video Indexer |Set di impostazioni di analisi video e analisi audio<br/>nelle API di Servizi multimediali v3|
|---|---|---|
|Informazioni dettagliate sui contenuti multimediali|[Avanzate](video-indexer-output-json-v2.md) |[Concetti fondamentali](../latest/intelligence-concept.md)|
|Esperienze|Vedere l'elenco completo delle funzionalità supportate: <br/> [Panoramica](video-indexer-overview.md)|Vengono restituite solo informazioni dettagliate sui video|
|Fatturazione|[Prezzi di Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Prezzi di Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformità|[ISO 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [lo standard ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1, 2,3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci)e [ HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) certified. Per gli aggiornamenti più recenti, visitare [stato corrente di certificazioni di Video Indexer](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|Servizi multimediali è conforme con molte certificazioni. Aprire il file [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e cercare "Servizi multimediali" per vedere se è conforme con un certificato specifico.|
|Versione di valutazione gratuita|Stati Uniti orientali|Non disponibile|
|Aree di disponibilità|Stati Uniti orientali 2, Stati Uniti centro-meridionali, Stati Uniti occidentali 2, Europa settentrionale, Europa occidentale, Asia sud-orientale, Asia orientale e Australia orientale.  Per gli aggiornamenti più recenti, visitare il [prodotti in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) pagina.|Vedere [Stato di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Video Indexer](video-indexer-overview.md)

[Panoramica di Servizi multimediali v3](../latest/media-services-overview.md)
