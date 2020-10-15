---
title: includere file
description: includere file
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80272631"
---
All'interno di una sottoscrizione è possibile creare più servizi. Il provisioning di ogni servizio può essere effettuato a un livello specifico. Il limite è rappresentato solo dal numero di servizi consentito a ogni livello. Ad esempio, è possibile creare fino a 12 servizi a livello Basic e altri 12 servizi a livello S1 nella stessa sottoscrizione. Per altre informazioni sui livelli, vedere [Scegliere uno SKU o un livello per Ricerca cognitiva di Azure](../articles/search/search-sku-tier.md).

È possibile aumentare i limiti massimi del servizio su richiesta. Se sono necessari più servizi nella stessa sottoscrizione, contattare il supporto di Azure.

| Risorsa            | Gratuito<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Numero massimo di servizi    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Aumento massimo del numero di istanze in unità di ricerca<sup>2</sup> |N/D |3 unità di ricerca |36 unità di ricerca |36 unità di ricerca |36 unità di ricerca |36 unità di ricerca |36 unità di ricerca |36 unità di ricerca |

<sup>1</sup> Il servizio gratuito è basato su risorse condivise, non dedicate. L'aumento delle prestazioni del servizio non è supportato sulle risorse condivise.

<sup>2</sup> Le unità di ricerca sono unità di fatturazione allocate come *replica* o come *partizione*. Per l'archiviazione, l'indicizzazione e le operazioni di query sono necessarie entrambe le risorse. Per altre informazioni sui calcoli delle unità di archiviazione, vedere [Ridimensionare i livelli di risorse per i carichi di lavoro di indicizzazione e query](../articles/search/search-capacity-planning.md). 