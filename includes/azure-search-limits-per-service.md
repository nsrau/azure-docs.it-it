---
title: File di inclusione
description: File di inclusione
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b298c87d802314bd865bd6f38c35e4361eb69f3f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180527"
---
L'archiviazione è vincolata dallo spazio su disco o da un limite rigido al *numero massimo* di indici, documenti o altre risorse di alto livello, a seconda di quale venga raggiunto per primo. Nella tabella seguente vengono documentati i limiti relativi all'archiviazione. Per i limiti massimi per gli indici, documenti e altri oggetti, vedere [limiti per singola risorsa](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resource | Gratuito | Basic<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Contratto di servizio (SLA)<sup>3</sup>  |No |Yes |Sì |Sì |Sì |Sì |Sì |Yes |
| Archiviazione per partizione |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partizioni per servizio |N/D |1 |12 |12 |12 |3 |12 |12 |
| Dimensioni della partizione |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliche |N/D |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Il livello Basic ha una partizione fissa. Questo livello, unità di ricerca aggiuntive vengono utilizzate per l'allocazione di più repliche per i carichi di lavoro maggiore di query.

<sup>2</sup> S3 HD prevede un limite rigido di tre partizioni, che è inferiore rispetto al limite della partizione per il livello S3. Il limite minore di partizioni viene imposto perché il numero di indici per S3 HD è significativamente superiore. Dato che sono presenti limiti del servizio per entrambe le risorse di calcolo (archiviazione ed elaborazione) e per il contenuto (indici e documenti), il limite relativo al contenuto viene raggiunto per primo.

<sup>3</sup> contratti di servizio vengono offerti per i servizi fatturabili su risorse dedicate. Per i servizi gratuiti e le funzionalità di anteprima non sono previsti contratti di servizio. Per i servizi fatturabili, i contratti di servizio diventano effettivi quando viene effettuato il provisioning di una ridondanza sufficiente per il servizio. Due o più repliche sono necessari per i contratti di servizio di query (lettura). Tre o più repliche sono necessari per i contratti di servizio (lettura e scrittura) di indicizzazione e query. Il numero di partizioni non è un fattore di contratto di servizio. 