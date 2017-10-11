---
title: Modello di dati di Azure Application Insights Telemetry - Telemetria delle tracce | Microsoft Docs
description: Modello di dati di Application Insights per la telemetria delle tracce
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: bwren
ms.openlocfilehash: e1da0d6a6fbd9ca5486936c326ade667d7b01006
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria delle tracce: modello di dati di Application Insights

In [Application Insights](app-insights-overview.md), la telemetria di traccia rappresenta le istruzioni di traccia in stile `printf` che vengono cercate come testo. `Log4Net`, `NLog` e altre voci del file di log basato su testo vengono convertite in istanze di questo tipo. La traccia non dispone di misure come l'estendibilità.

## <a name="message"></a>Message

Messaggio di traccia.

Lunghezza massima: 32.768 caratteri

## <a name="severity-level"></a>Livello di gravità

Livello di gravità della traccia. Il valore può essere `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Proprietà personalizzate

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare i log di traccia .NET in Application Insights](app-insights-asp-net-trace-logs.md).
- [Esplorare i log di traccia Java in Application Insights](app-insights-java-trace-logs.md).
- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](application-insights-data-model.md).
- [Scrivere dati di telemetria di traccia personalizzata](app-insights-api-custom-events-metrics.md#tracktrace)
- Verificare quali [piattaforme](app-insights-platforms.md) supportano Application Insights.
