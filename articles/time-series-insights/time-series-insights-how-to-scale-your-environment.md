---
title: Come ridimensionare l'ambiente-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come ridimensionare l'ambiente di Azure Time Series Insights usando il portale di Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 0dadf523c5d17ffb91f4fefa71b52d1d1855c978
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570217"
---
# <a name="how-to-scale-your-azure-time-series-insights-gen1-environment"></a>Come ridimensionare l'ambiente Gen1 Azure Time Series Insights

> [!CAUTION]
> È un articolo di Gen1.

Questo articolo descrive come modificare la capacità dell'ambiente di Azure Time Series Insights usando il [portale di Azure](https://portal.azure.com). Per capacità si intende il moltiplicatore applicato alla velocità in ingresso, alla capacità di archiviazione e ai costi associati allo SKU selezionato.

È possibile usare il portale di Azure per aumentare o diminuire la capacità in uno SKU di prezzo specifico.

Non è però consentita la modifica dello SKU di piano tariffario. Ad esempio, un ambiente con uno SKU di prezzo S1 non può essere convertito in un ambiente con SKU S2 o viceversa.

## <a name="ga-limits"></a>Limiti GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Modificare la capacità dell'ambiente

1. Nella portale di Azure individuare e selezionare l'ambiente Azure Time Series Insights.

1. Nel menu per l'ambiente di Azure Time Series Insights selezionare **Configurazione archiviazione**.

   [![Configurare la capacità del Azure Time Series Insights](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Regolare il dispositivo di scorrimento **Capacità** per selezionare una capacità che soddisfi i requisiti in termini di velocità in ingresso e capacità di archiviazione. Si noti che la **velocità in ingresso**, la **capacità di archiviazione** e i **costi stimati** vengono aggiornati in modo dinamico per illustrare l'impatto della modifica.

   [![Configurare l'ambiente usando il dispositivo di scorrimento capacità](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   In alternativa, è possibile digitare il numero del moltiplicatore di capacità nella casella di testo a destra del dispositivo di scorrimento.

1. Selezionare **Salva** per ridimensionare l'ambiente. Finché non viene eseguito il commit della modifica, viene visualizzato temporaneamente l'indicatore di stato.

1. Verificare che la nuova capacità sia [sufficiente per evitare la limitazione delle richieste](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni, vedere informazioni sulla [conservazione in Azure Time Series Insights](time-series-insights-concepts-retention.md).

- Informazioni sulla [configurazione della conservazione dei dati in Azure Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Informazioni sulla [pianificazione dell'ambiente](time-series-insights-environment-planning.md).
