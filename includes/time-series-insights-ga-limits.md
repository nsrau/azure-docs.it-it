---
title: File di inclusione
description: File di inclusione
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 10/16/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: e8c040bc186932680ce7eb4418a787dc061d02ce
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991154"
---
Di seguito sono riepilogati i limiti chiave della disponibilità generale.

### <a name="sku-ingress-rates-and-capacities"></a>Velocità e capacità in ingresso dello SKU

Le capacità e le velocità in ingresso dello SKU S1 e S2 garantiscono flessibilità durante la configurazione di un nuovo ambiente Time Series Insights.

| Capacità SKU S1 | Velocità in ingresso | Capacità di archiviazione massima
| --- | --- | --- |
| 1 | 1 GB (1 milione di eventi) | 30 GB (30 milioni di eventi) al mese |
| 10 | 10 GB (10 milioni di eventi) | 300 GB (300 milioni di eventi) al mese |

| Capacità SKU S2 | Velocità in ingresso | Capacità di archiviazione massima
| --- | --- | --- |
| 1 | 10 GB (10 milioni di eventi) | 300 GB (300 milioni di eventi) al mese |
| 10 | 100 GB (100 milioni di eventi) | 3 TB (3 miliardi di eventi) al mese |

> [!NOTE]
> La capacità ha una scalabilità lineare, pertanto uno SKU S1 con capacità 2 supporta una velocità in ingresso di 2 GB (2 milioni) di eventi al giorno e 60 GB (60 milioni) di eventi al mese.

Gli ambienti SKU S2 supportano sostanzialmente più eventi al mese e hanno una capacità di ingresso significativamente superiore.

| SKU  | Conteggio eventi al mese  | Dimensioni evento al mese  | Conteggio eventi al minuto | Dimensioni evento al minuto  |
|---------|---------|---------|---------|---------|
| S1     |   30 milioni     |  30 GB     |  720    |  720 KB   |
 |S2     |   300 milioni    |   300 GB   | 7\.200   | 7\.200 KB  |

### <a name="property-limits"></a>Limiti delle proprietà

I limiti delle proprietà GA dipendono dall'ambiente SKU selezionato. Le proprietà dell'evento fornite presentano colonne JSON, CSV e del grafico corrispondenti che possono essere visualizzate all'interno di [esplora Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| SKU | Proprietà massime |
| --- | --- |
| S1 | Proprietà 600 (colonne) |
| S2 | Proprietà 800 (colonne) |

### <a name="event-sources"></a>Origini eventi

È supportato un massimo di due origini evento per ogni istanza. 

* Informazioni su come [aggiungere un'origine di hub eventi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Configurare [un'origine Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)Internet delle cose.

### <a name="api-limits"></a>Limiti API

I limiti dell'API REST per Time Series Insights disponibilità generale sono specificati nella [documentazione di riferimento dell'API REST](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits).