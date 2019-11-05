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
ms.openlocfilehash: dfe16b4e965670d115cfa92f1cb3ca812d6375ad
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990842"
---
### <a name="general-availability-and-preview-comparison"></a>Confronto tra disponibilità generale e anteprima

Nella tabella seguente sono riepilogate alcune differenze principali tra Azure Time Series Insights disponibilità generale (GA) e le istanze di anteprima.

| | GA | Preview |
| --- | --- | ---|
| Cittadino di prima classe | Incentrato sugli eventi | Incentrato sulle serie temporali |
| Ragionamento semantico | Di basso livello (dati di riferimento) | Alto livello (modelli) |
| Contestualizzazione dei dati | Livello non dispositivo | Livello dispositivo e non dispositivo |
| Archiviazione logica di calcolo | No | Archiviato in variabili di tipo parte del modello |
| Archiviazione e controllo di accesso | No | Abilitato tramite modello |
| Aggregazioni/campionamento | No | Ponderato e tempo ponderato per l'evento |
| Ricostruzione del segnale | No | Interpolazione |
| Produzione di serie temporali derivate | No | Sì, unioni e join |
| Flessibilità del linguaggio | Non componibile | Componibile |
| Linguaggio delle espressioni | Stringa di predicato | Espressioni Time Series (stringhe di predicato, valori, espressioni e funzioni) |

### <a name="property-limits"></a>Limiti delle proprietà

Time Series Insights limiti delle proprietà sono aumentati fino a 1.000 da un limite massimo di 800 in GA. Le proprietà dell'evento fornite presentano colonne JSON, CSV e del grafico corrispondenti che è possibile visualizzare all'interno di [esplora Time Series Insights Preview](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Proprietà massime |
| --- | --- |
| Anteprima PAYG | Proprietà 1.000 (colonne) |
| S1 GA | Proprietà 600 (colonne) |
| GA S2 | Proprietà 800 (colonne) |

### <a name="event-sources"></a>Origini eventi

È supportato un massimo di due origini evento per ogni istanza. 

* Informazioni su come [aggiungere un'origine di hub eventi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Configurare [un'origine Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)Internet delle cose.

### <a name="api-limits"></a>Limiti API

I limiti dell'API REST per Time Series Insights anteprima sono specificati nella [documentazione di riferimento dell'API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits).