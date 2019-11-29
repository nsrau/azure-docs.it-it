---
title: Come ridimensionare l'ambiente-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come ridimensionare l'ambiente di Azure Time Series Insights usando il portale di Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: bc6c33762fa572e971fff9dee8988a100e50b07c
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561175"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Come scalare l'ambiente Time Series Insights

Questo articolo descrive come modificare la capacità dell'ambiente di Time Series Insights usando il [portale di Azure](https://portal.azure.com). Per capacità si intende il moltiplicatore applicato alla velocità in ingresso, alla capacità di archiviazione e ai costi associati allo SKU selezionato.

È possibile usare il portale di Azure per aumentare o diminuire la capacità in uno SKU di prezzo specifico.

Non è però consentita la modifica dello SKU di piano tariffario. Ad esempio, un ambiente con uno SKU di prezzo S1 non può essere convertito in un ambiente con SKU S2 o viceversa.

## <a name="ga-limits"></a>Limiti GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Modificare la capacità dell'ambiente

1. Nel portale di Azure individuare selezionare l'ambiente Time Series Insights in uso.

1. Nel menu per l'ambiente di Time Series Insights selezionare **Configurazione archiviazione**.

   [![configurare la capacità Time Series Insights](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Regolare il dispositivo di scorrimento **Capacità** per selezionare una capacità che soddisfi i requisiti in termini di velocità in ingresso e capacità di archiviazione. Si noti che la **velocità in ingresso**, la **capacità di archiviazione** e i **costi stimati** vengono aggiornati in modo dinamico per illustrare l'impatto della modifica.

   [![configurare l'ambiente usando il dispositivo di scorrimento capacità](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   In alternativa, è possibile digitare il numero del moltiplicatore di capacità nella casella di testo a destra del dispositivo di scorrimento.

1. Selezionare **Salva** per ridimensionare l'ambiente. Finché non viene eseguito il commit della modifica, viene visualizzato temporaneamente l'indicatore di stato.

1. Verificare che la nuova capacità sia [sufficiente per evitare la limitazione delle richieste](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Understanding retention in Time Series Insights](time-series-insights-concepts-retention.md) (Informazioni sulla conservazione in Time Series Insights).

- Informazioni sulla [configurazione della conservazione dei dati in Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Informazioni sulla [pianificazione dell'ambiente](time-series-insights-environment-planning.md).