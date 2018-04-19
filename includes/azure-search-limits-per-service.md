---
title: File di inclusione
description: File di inclusione
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b4062aab5a453505ef4586f422a124d4bbf715cb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2018
---
L'archiviazione è vincolata dallo spazio su disco o da un limite rigido al *numero massimo* di indici, documenti o altre risorse di alto livello, a seconda di quale venga raggiunto per primo. Nella tabella seguente vengono documentati i limiti relativi all'archiviazione. Per i limiti massimi relativi a indici, documenti e altri oggetti, vedere la sezione sui [limiti per singola risorsa](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Risorsa | Gratuito | Base&nbsp;<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| Contratto di servizio <sup>3</sup>  |No  |Sì |Sì |Sì |Sì |Sì |
| Archiviazione per partizione |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partizioni per servizio |N/D |1 |12 |12 |12 |3 |
| Dimensioni della partizione |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |
| Repliche |N/D |3 |12 |12 |12 |12 |

<sup>1</sup> Il livello Basic ha una partizione fissa. A questo livello vengono usate SU aggiuntive per l'allocazione di più repliche in caso di aumento dei carichi di lavoro di query.

<sup>2</sup> Per S3 HD è previsto un limite rigido di 3 partizioni, inferiore al limite di partizioni per S3. Il limite minore di partizioni viene imposto perché il numero di indici per S3 HD è significativamente superiore. Dato che sono presenti limiti del servizio per entrambe le risorse di calcolo (archiviazione ed elaborazione) e per il contenuto (indici e documenti), il limite relativo al contenuto viene raggiunto per primo.

<sup>3</sup> I contratti di servizio vengono offerti per i servizi fatturabili su risorse dedicate. Per i servizi gratuiti e le funzionalità di anteprima non sono previsti contratti di servizio. Per i servizi fatturabili, i contratti di servizio diventano effettivi quando viene effettuato il provisioning di una ridondanza sufficiente per il servizio. Per il Contratto di servizio di query (lettura) sono necessarie due o più repliche. Per il contratto di servizio di query e indicizzazione (lettura-scrittura) sono necessarie tre o più repliche. Il numero di partizioni non è un fattore di cui tiene conto il Contratto di servizio. 