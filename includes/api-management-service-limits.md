---
title: File di inclusione
description: File di inclusione
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: b40e2ba448236bcef72ffe501b4b14fa0dd64e1c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31434940"
---
| Risorsa | Limite |
| --- | --- |
| Unità di scala | 10 per area<sup>1</sup> |
| Cache | 5 GB per unità<sup>1</sup> |
| Connessioni back-end simultanee<sup>2</sup> per ogni autorità HTTP | 2048 per unità<sup>3</sup> |
| Dimensione massima della risposta memorizzata nella cache | 10 MB |
| Dimensioni massime del documento dei criteri | 256 KB |
| Numero massimo di domini gateway personalizzati | 20 per ogni istanza di servizio<sup>4</sup> |


<sup>1</sup>I limiti di Gestione API sono diversi per ogni livello di prezzo. Per visualizzare i piani tariffari con i relativi limiti di ridimensionamento, vedere [Prezzi di Gestione API](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> Le connessioni vengono raggruppate in pool e riutilizzate, a meno che non siano chiuse esplicitamente dal back-end.
<sup>3</sup> Per unità dei livelli Basic, Standard e Premium. Il livello Developer è limitato a 1024.
<sup>4</sup> Disponibile solo nel livello Premium.


