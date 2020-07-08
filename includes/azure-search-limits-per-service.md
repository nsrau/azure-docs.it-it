---
title: File di inclusione
description: File di inclusione
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/11/2020
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fe5c8129434ddb4eec2dd25a3f123f28b4db221b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682632"
---
Un servizio di ricerca è vincolato dallo spazio su disco o da un limite rigido relativo al numero massimo di indici o indicizzatori, a seconda di quale venga raggiunto per primo. Nella tabella seguente vengono documentati i limiti relativi all'archiviazione. Per i limiti massimi degli oggetti, vedere [Limiti per risorsa](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Risorsa | Gratuito | Basic<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Contratto di servizio (SLA)<sup>2</sup>  |No |Sì |Sì |Sì |Sì |Sì |Sì |Sì |
| Archiviazione per partizione |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partizioni per servizio |N/D |1 |12 |12 |12 |3 |12 |12 |
| Dimensioni della partizione |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliche |N/D |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Il livello Basic ha una partizione fissa. È possibile usare unità di ricerca aggiuntive per aggiungere repliche per volumi di query di dimensioni maggiori.

<sup>2</sup> I contratti di servizio sono attivi per servizi fatturabili su risorse dedicate. Per i servizi gratuiti e le funzionalità di anteprima non sono previsti contratti di servizio. Per i servizi fatturabili, i contratti di servizio diventano effettivi quando viene effettuato il provisioning di una ridondanza sufficiente per il servizio. Per i contratti di servizio di query (lettura) sono necessarie due o più repliche. Per i contratti di servizio di query e indicizzazione (lettura-scrittura) sono necessarie tre o più repliche. Il numero di partizioni non è un fattore di cui viene tenuto conto ai fini del contratto di servizio. 