---
title: includere il file
description: File di inclusione
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 48080bb4d1e24f7f98d3dfe1fd63b65ba46df35e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289906"
---
### <a name="property-limits"></a>Limiti delle proprietà

Azure Time Series Insights limiti delle proprietà sono aumentati a 1.000 da un limite massimo di 800 in Gen1. Le proprietà dell'evento fornite presentano colonne JSON, CSV e del grafico corrispondenti che è possibile visualizzare all'interno di [Azure Time Series Insights Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Proprietà massime |
| --- | --- |
| Gen2 (L1) | Proprietà 1.000 (colonne) |
| Gen1 (S1) | Proprietà 600 (colonne) |
| Gen1 (S2) | Proprietà 800 (colonne) |

### <a name="event-sources"></a>Origini eventi

È supportato un massimo di due origini evento per ogni istanza.

* Informazioni su come [aggiungere un'origine di hub eventi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Configurare [un'origine Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)Internet delle cose.

Per impostazione predefinita, gli [ambienti Gen2 supportano le velocità in ingresso](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingress-throughput-limits) fino a **1 MB al secondo (MB/s) per ogni ambiente**. Se necessario, i clienti possono ridimensionare gli ambienti fino a **16 MB/s** di velocità effettiva. Esiste anche un limite per partizione di **0,5 MB/s**.

### <a name="api-limits"></a>Limiti API

I limiti dell'API REST per Azure Time Series Insights Gen2 sono specificati nella [documentazione di riferimento dell'API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
