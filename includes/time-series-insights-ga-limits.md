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
ms.openlocfilehash: 11469d992e0f5669cd3fc1e3864627dd0b8ae23d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263345"
---
Di seguito sono riepilogati i limiti chiave in Disponibilità generale.

### <a name="sku-ingress-rates-and-capacities"></a>Frequenze e capacità di ingresso SKU

Le velocità e le capacità in ingresso degli SKU S1 e S2 offrono flessibilità durante la configurazione di un nuovo ambiente Time Series Insights. La capacità SKU indica la frequenza di ingresso giornaliera in base al numero di eventi o byte archiviati, a seconda dell'evento che si presenta per primo. Si noti che l'ingresso viene misurato *al minuto*e **la limitazione viene** applicata utilizzando l'algoritmo del bucket di token. L'ingresso è misurato in blocchi da 1 KB. Ad esempio, un evento effettivo da 0,8 KB verrebbe misurato come un evento e un evento da 2,6 KB viene conteggiato come tre eventi.

| Capacità SKU SKU | Tasso di ingresso | Capacità massima di storage
| --- | --- | --- |
| 1 | 1 GB (1 milione di eventi) al giorno | 30 GB (30 milioni di eventi) al mese |
| 10 | 10 GB (10 milioni di eventi) al giorno | 300 GB (300 milioni di eventi) al mese |

| Capacità SKU S2 | Tasso di ingresso | Capacità massima di storage
| --- | --- | --- |
| 1 | 10 GB (10 milioni di eventi) al giorno | 300 GB (300 milioni di eventi) al mese |
| 10 | 100 GB (100 milioni di eventi) al giorno | 3 TB (3 miliardi di eventi) al mese |

> [!NOTE]
> La capacità ha una scalabilità lineare, pertanto uno SKU S1 con capacità 2 supporta una velocità in ingresso di 2 GB (2 milioni) di eventi al giorno e 60 GB (60 milioni) di eventi al mese.

Gli ambienti SKU S2 supportano un numero notevolmente maggiore di eventi al mese e hanno una capacità di ingresso significativamente maggiore.

| SKU  | Numero di eventi al mese  | Numero di eventi al minuto | Dimensioni evento al minuto  |
|---------|---------|---------|---------|---------|
| S1     |   30 milioni   |  720    |  720 KB   |
 |S2     |   300 milioni   | 7.200   | 7.200 KB  |

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