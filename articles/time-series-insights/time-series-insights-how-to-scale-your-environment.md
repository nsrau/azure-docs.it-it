---
title: Come scalare l'ambiente Time Series Insights di Azure | Microsoft Docs
description: Questo articolo spiega come ridimensionare l'ambiente Time Series Insights. Per aggiungere o sottrarre capacità in uno SKU di prezzo, usare il portale di Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 3f03f5ed75c720c9b0daf30d721ef4d2aee9749c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991146"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Come scalare l'ambiente Time Series Insights

Questo articolo descrive come modificare la capacità dell'ambiente di Time Series Insights usando il [portale di Azure](https://portal.azure.com). Per capacità si intende il moltiplicatore applicato alla velocità in ingresso, alla capacità di archiviazione e ai costi associati allo SKU selezionato.

È possibile usare il portale di Azure per aumentare o diminuire la capacità in uno SKU di prezzo specifico.

Non è però consentita la modifica dello SKU di piano tariffario. Ad esempio, un ambiente con uno SKU di prezzo S1 non può essere convertito in un ambiente con SKU S2 o viceversa.

## <a name="ga-limits"></a>Limiti GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Modificare la capacità dell'ambiente

1. Nel portale di Azure individuare selezionare l'ambiente Time Series Insights in uso.

1. Nel menu per l'ambiente Time Series Insights, selezionare **Configura**.

   [![configure. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Regolare il dispositivo di scorrimento **Capacità** per selezionare una capacità che soddisfi i requisiti in termini di velocità in ingresso e capacità di archiviazione. Si noti che la **velocità in ingresso**, la **capacità di archiviazione** e i **costi stimati** vengono aggiornati in modo dinamico per illustrare l'impatto della modifica.

   [Dispositivo di scorrimento![](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   In alternativa, è possibile digitare il numero del moltiplicatore di capacità nella casella di testo a destra del dispositivo di scorrimento.

1. Selezionare **Salva** per ridimensionare l'ambiente. Finché non viene eseguito il commit della modifica, viene visualizzato temporaneamente l'indicatore di stato.

1. Verificare che la nuova capacità sia [sufficiente per evitare la limitazione delle richieste](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Understanding retention in Time Series Insights](time-series-insights-concepts-retention.md) (Informazioni sulla conservazione in Time Series Insights).

- Informazioni sulla [configurazione della conservazione dei dati in Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Informazioni sulla [pianificazione dell'ambiente](time-series-insights-environment-planning.md).