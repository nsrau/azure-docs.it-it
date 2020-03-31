---
title: File di inclusione
description: File di inclusione
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77123182"
---
### <a name="general-availability-and-preview-comparison"></a>Disponibilità generale e confronto delle anteprime

La tabella seguente riepiloga diverse differenze principali tra la disponibilità generale (GA) di Azure Time Series Insights e le istanze di anteprima.

| | GA | Anteprima |
| --- | --- | ---|
| Cittadino di prima classe | Incentrato sugli eventi | Incentrato sulle serie temporali |
| Ragionamento semantico | Basso livello (dati di riferimento) | Alto livello (modelli) |
| Contestualizzazione dei dati | Livello non dispositivo | Livello dispositivo e non dispositivo |
| Archiviazione della logica di calcoloCompute logic storage | No | Memorizzato nelle variabili di tipo parte del modello |
| Archiviazione e controllo degli accessi | No | Abilitato tramite modello |
| Aggregazioni/Campionamento | No | Evento ponderato e tempo ponderato |
| Ricostruzione del segnale | No | Interpolazione |
| Produzione di serie temporali derivate | No | Sì, unisce e unisce |
| Flessibilità linguistica | Non componibile | Componibile |
| Linguaggio delle espressioni | Stringa predicato | Espressioni di serie temporali (stringhe, valori, espressioni e funzioni) |

### <a name="property-limits"></a>Limiti di proprietà

I limiti delle proprietà di Time Series Insights sono aumentati a 1.000 rispetto a un limite massimo di 800 in GA. Le proprietà degli eventi fornite dispongono di colonne JSON, CSV e del grafico corrispondenti che è possibile visualizzare all'interno [dell'Esplorazione anteprima di Time Series Insights.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)

| SKU | Proprietà massime |
| --- | --- |
| Anteprima PAYG | 1.000 proprietà (colonne) |
| GA S1 | 600 proprietà (colonne) |
| GA S2 | 800 proprietà (colonne) |

### <a name="event-sources"></a>Origini eventi

È supportato un massimo di due origini eventi per istanza. 

* Informazioni su come [aggiungere un'origine dell'hub eventi.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)
* Configurare [un'origine hub IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Per impostazione predefinita, gli ambienti di [anteprima supportano velocità](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) in ingresso fino a **1 megabyte al secondo (MB/s) per ambiente.** Se necessario, i clienti possono scalare i propri ambienti di anteprima fino a **16 MB/s** di velocità effettiva. È inoltre previsto un limite per partizione di **0,5 MB/s**. 

### <a name="api-limits"></a>Limiti API

I limiti dell'API REST per Time Series Insights Preview sono specificati nella documentazione di [riferimento dell'API REST.](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)
