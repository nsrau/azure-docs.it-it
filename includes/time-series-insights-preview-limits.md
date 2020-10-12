---
title: includere file
description: includere file
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 7259e1981f873c8385a02fe4f353dcdda495f823
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91287415"
---
### <a name="property-limits"></a>Limiti delle proprietà

Azure Time Series Insights limiti delle proprietà sono aumentati a 1.000 da un limite massimo di 800 in Gen1. Le proprietà dell'evento fornite presentano colonne JSON, CSV e del grafico corrispondenti che è possibile visualizzare all'interno di [Azure Time Series Insights Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Proprietà massime |
| --- | --- |
| Gen2 (L1) | Proprietà 1.000 (colonne) |
| Gen1 (S1) | Proprietà 600 (colonne) |
| Gen1 (S2) | Proprietà 800 (colonne) |

### <a name="streaming-ingestion"></a>Inserimento streaming

* Sono disponibili un massimo di due [origini evento](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) per ogni ambiente.

* Le procedure consigliate e le linee guida generali per le origini eventi sono disponibili [qui](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)

* Per impostazione predefinita, Azure Time Series Insights Gen2 è in grado di inserire i dati in ingresso a una velocità di un **massimo di 1 megabyte al secondo (Mbps) per Azure Time Series Insights ambiente Gen2**. [Per ogni partizione dell'hub](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits) ci sono altre limitazioni. È possibile fornire frequenze fino a 8 MBps inviando un ticket di supporto tramite il portale di Azure. Per altre informazioni, vedere [limiti di velocità effettiva](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)di inserimento di flussi.

### <a name="api-limits"></a>Limiti API

I limiti dell'API REST per Azure Time Series Insights Gen2 sono specificati nella [documentazione di riferimento dell'API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
