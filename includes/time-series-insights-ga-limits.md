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
ms.openlocfilehash: 1aff5780aa5f4cf21d862a5cee3b140179dac03c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88704527"
---
Di seguito vengono riepilogati i limiti delle chiavi in Azure Time Series Insights Gen1.

### <a name="sku-ingress-rates-and-capacities"></a>Velocità e capacità in ingresso dello SKU

Le capacità e le velocità in ingresso dello SKU S1 e S2 garantiscono flessibilità durante la configurazione di un nuovo ambiente Azure Time Series Insights. La capacità dello SKU indica la velocità in ingresso giornaliera in base al numero di eventi o byte archiviati, a seconda di quale si verifichi per primo. Si noti che il traffico in ingresso viene misurato *al minuto*e la **limitazione** viene applicata usando l'algoritmo bucket del token. Il traffico in ingresso viene misurato in blocchi da 1 KB. Ad esempio, un evento effettivo da 0,8 KB verrebbe misurato come un evento e un evento 2,6-KB viene conteggiato come tre eventi.

| Capacità SKU S1 | Velocità in ingresso | Capacità di archiviazione massima
| --- | --- | --- |
| 1 | 1 GB (1 milione eventi) al giorno | 30 GB (30 milioni di eventi) al mese |
| 10 | 10 GB (eventi 10 milioni) al giorno | 300 GB (300 milioni di eventi) al mese |

| Capacità SKU S2 | Velocità in ingresso | Capacità di archiviazione massima
| --- | --- | --- |
| 1 | 10 GB (eventi 10 milioni) al giorno | 300 GB (300 milioni di eventi) al mese |
| 10 | 100 GB (100 milioni eventi) al giorno | 3 TB (3 miliardi di eventi) al mese |

> [!NOTE]
> La capacità ha una scalabilità lineare, pertanto uno SKU S1 con capacità 2 supporta una velocità in ingresso di 2 GB (2 milioni) di eventi al giorno e 60 GB (60 milioni) di eventi al mese.

Gli ambienti SKU S2 supportano sostanzialmente più eventi al mese e hanno una capacità di ingresso significativamente superiore.

| SKU  | Conteggio eventi al mese  | Conteggio eventi al minuto | Dimensioni evento al minuto  |
|---------|---------|---------|---------|---------|
| S1     |   30 milioni   |  720    |  720 KB   |
 |S2     |   300 milioni   | 7.200   | 7.200 KB  |

### <a name="property-limits"></a>Limiti delle proprietà

I limiti della proprietà Gen1 dipendono dall'ambiente SKU selezionato. Le proprietà dell'evento fornite presentano colonne JSON, CSV e del grafico corrispondenti che possono essere visualizzate all'interno di [esplora Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| SKU | Proprietà massime |
| --- | --- |
| S1 | Proprietà 600 (colonne) |
| S2 | Proprietà 800 (colonne) |

### <a name="event-sources"></a>Origini eventi

È supportato un massimo di due origini evento per ogni istanza.

* Informazioni su come [aggiungere un'origine di hub eventi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Configurare [un'origine Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)Internet delle cose.

### <a name="api-limits"></a>Limiti API

I limiti dell'API REST per Azure Time Series Insights Gen1 sono specificati nella [documentazione di riferimento dell'API REST](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability).