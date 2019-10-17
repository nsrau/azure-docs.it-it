---
title: Come scalare l'ambiente Time Series Insights di Azure | Microsoft Docs
description: Questo articolo spiega come ridimensionare l'ambiente Time Series Insights. Per aggiungere o sottrarre capacità in uno SKU di prezzo, usare il portale di Azure.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: a899de22137decc1eb1578369a2751710c17abda
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332856"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Come scalare l'ambiente Time Series Insights

Questo articolo descrive come modificare la capacità dell'ambiente di Time Series Insights usando il [portale di Azure](https://portal.azure.com). Per capacità si intende il moltiplicatore applicato alla velocità in ingresso, alla capacità di archiviazione e ai costi associati allo SKU selezionato.

È possibile usare il portale di Azure per aumentare o diminuire la capacità in uno SKU di prezzo specifico.

Non è però consentita la modifica dello SKU di piano tariffario. Ad esempio, un ambiente con uno SKU di prezzo S1 non può essere convertito in un ambiente con SKU S2 o viceversa.

## <a name="s1-sku-ingress-rates-and-capacities"></a>Capacità e velocità di ingresso dello SKU S1

| Capacità SKU S1 | Velocità in ingresso | Capacità massima di archiviazione
| --- | --- | --- |
| 1 | 1 GB (1 milione di eventi) | 30 GB (30 milioni di eventi) al mese |
| 10 | 10 GB (10 milioni di eventi) | 300 GB (300 milioni di eventi) al mese |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Capacità e velocità di ingresso dello SKU S2

| Capacità SKU S2 | Velocità in ingresso | Capacità massima di archiviazione
| --- | --- | --- |
| 1 | 10 GB (10 milioni di eventi) | 300 GB (300 milioni di eventi) al mese |
| 10 | 100 GB (100 milioni di eventi) | 3 TB (3 miliardi di eventi) al mese |

La capacità ha una scalabilità lineare, pertanto uno SKU S1 con capacità 2 supporta una velocità in ingresso di 2 GB (2 milioni) di eventi al giorno e 60 GB (60 milioni) di eventi al mese.

## <a name="change-the-capacity-of-your-environment"></a>Modificare la capacità dell'ambiente

1. Nel portale di Azure individuare selezionare l'ambiente Time Series Insights in uso.

1. Nel menu per l'ambiente Time Series Insights, selezionare **Configura**.

   [@no__t -1configure. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Regolare il dispositivo di scorrimento **Capacità** per selezionare una capacità che soddisfi i requisiti in termini di velocità in ingresso e capacità di archiviazione. Si noti che la **velocità in ingresso**, la **capacità di archiviazione** e i **costi stimati** vengono aggiornati in modo dinamico per illustrare l'impatto della modifica.

   [![Slider](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   In alternativa, è possibile digitare il numero del moltiplicatore di capacità nella casella di testo a destra del dispositivo di scorrimento.

1. Selezionare **Salva** per ridimensionare l'ambiente. Finché non viene eseguito il commit della modifica, viene visualizzato temporaneamente l'indicatore di stato.

1. Verificare che la nuova capacità sia [sufficiente per evitare la limitazione delle richieste](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Understanding retention in Time Series Insights](time-series-insights-concepts-retention.md) (Informazioni sulla conservazione in Time Series Insights).

- Informazioni sulla [configurazione della conservazione dei dati in Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Informazioni sulla [pianificazione dell'ambiente](time-series-insights-environment-planning.md).