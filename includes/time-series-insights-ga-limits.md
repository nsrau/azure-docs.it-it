---
title: File di inclusione
description: File di inclusione
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: f097479b67a7b48eca4a2710db3bd7eed6ddc982
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013684"
---
Di seguito sono riepilogati i limiti chiave in Disponibilità generale.

### <a name="sku-ingress-rates-and-capacities"></a>Frequenze e capacità di ingresso SKU

Le velocità e le capacità in ingresso degli SKU S1 e S2 offrono flessibilità durante la configurazione di un nuovo ambiente Time Series Insights.

| Capacità SKU SKU | Tasso di ingresso | Capacità massima di storage
| --- | --- | --- |
| 1 | 1 GB (1 milione di eventi) | 30 GB (30 milioni di eventi) al mese |
| 10 | 10 GB (10 milioni di eventi) | 300 GB (300 milioni di eventi) al mese |

| Capacità SKU S2 | Tasso di ingresso | Capacità massima di storage
| --- | --- | --- |
| 1 | 10 GB (10 milioni di eventi) | 300 GB (300 milioni di eventi) al mese |
| 10 | 100 GB (100 milioni di eventi) | 3 TB (3 miliardi di eventi) al mese |

> [!NOTE]
> La capacità ha una scalabilità lineare, pertanto uno SKU S1 con capacità 2 supporta una velocità in ingresso di 2 GB (2 milioni) di eventi al giorno e 60 GB (60 milioni) di eventi al mese.

Gli ambienti SKU S2 supportano un numero notevolmente maggiore di eventi al mese e hanno una capacità di ingresso significativamente maggiore.

| SKU  | Numero di eventi al mese  | Dimensioni evento al mese  | Numero di eventi al minuto | Dimensioni evento al minuto  |
|---------|---------|---------|---------|---------|
| S1     |   30 milioni     |  30 GB     |  720    |  720 KB   |
 |S2     |   300 milioni    |   300 GB   | 7.200   | 7.200 KB  |

### <a name="property-limits"></a>Limiti di proprietà

I limiti delle proprietà GA dipendono dall'ambiente SKU selezionato. Le proprietà degli eventi fornite dispongono di colonne JSON, CSV e di grafico corrispondenti che possono essere visualizzate in [Time Series Insights Explorer.](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)

| SKU | Proprietà massime |
| --- | --- |
| S1 | 600 proprietà (colonne) |
| S2 | 800 proprietà (colonne) |

### <a name="event-sources"></a>Origini eventi

È supportato un massimo di due origini eventi per istanza. 

* Informazioni su come [aggiungere un'origine dell'hub eventi.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)
* Configurare [un'origine hub IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Limiti API

I limiti dell'API REST per la disponibilità generale di Time Series Insights sono specificati nella documentazione di [riferimento dell'API REST.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)