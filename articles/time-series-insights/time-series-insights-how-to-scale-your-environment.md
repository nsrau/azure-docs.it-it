---
title: Come scalare l'ambiente Time Series Insights di Azure | Microsoft Docs
description: Questa esercitazione illustra come scalare l'ambiente Time Series Insights
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: ba6bd1ab05bb7e24dd1bc307218e7a772fbde601
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Come scalare l'ambiente Time Series Insights

Questa esercitazione illustra come scalare l'ambiente Time Series Insights.

> [!NOTE]
> La scalabilità verticale tra i tipi di SKU non è consentita. Un ambiente con uno SKU S1 non può essere convertito in un ambiente S2.

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

## <a name="changing-the-capacity-of-your-environment"></a>Modifica della capacità dell'ambiente

1. Nel portale di Azure selezionare l'ambiente di cui si intende modificare la capacità.
1. In Impostazioni fare clic su Configura.
1. Usare l'apposito dispositivo di scorrimento per selezionare la capacità che soddisfi i requisiti per la velocità in ingresso e la capacità di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

* Verificare che la nuova capacità sia sufficiente a impedire la limitazione delle richieste. Per altre informazioni, vedere la sezione *Your environment might be getting throttled* (L'ambiente potrebbe essere soggetto a limitazione delle richieste) [qui](time-series-insights-diagnose-and-solve-problems.md).