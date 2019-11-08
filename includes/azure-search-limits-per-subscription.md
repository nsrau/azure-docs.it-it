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
ms.openlocfilehash: 380a0a76de763df5f2cc6ff702750b36d233728d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818333"
---
È possibile creare più servizi all'interno di una sottoscrizione. Ognuno di essi può essere sottoposti a provisioning a un livello specifico. L'utente è limitato solo dal numero di servizi consentiti a ogni livello. Ad esempio, è possibile creare fino a 12 servizi a livello Basic e altri 12 servizi a livello S1 nella stessa sottoscrizione. Per altre informazioni sui livelli, vedere [scegliere uno SKU o un livello per ricerca cognitiva di Azure](../articles/search/search-sku-tier.md).

È possibile aumentare i limiti massimi del servizio su richiesta. Se sono necessari altri servizi all'interno della stessa sottoscrizione, contattare il supporto tecnico di Azure.

| Risorsa            | Gratis<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Numero massimo di servizi    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Scalabilità massima in unità di ricerca (SU)<sup>2</sup> |N/D |3 unità di ricerca |36 unità di ricerca |36 unità di ricerca |36 unità di ricerca |36 unità di ricerca |36 unità di ricerca |36 unità di ricerca |

<sup>1</sup> Il servizio gratuito è basato su risorse condivise, non dedicate. L'aumento delle prestazioni del servizio non è supportato sulle risorse condivise.

<sup>2</sup> le unità di ricerca sono unità di fatturazione, allocate come *replica* o come *partizione*. Per l'archiviazione, l'indicizzazione e le operazioni di query sono necessarie entrambe le risorse. Per altre informazioni sui calcoli delle unità di archiviazione, vedere [Ridimensionare i livelli di risorse per i carichi di lavoro di indicizzazione e query](../articles/search/search-capacity-planning.md). 