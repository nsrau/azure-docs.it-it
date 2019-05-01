---
title: Modello di dati di Azure Application Insights Telemetry - Telemetria delle tracce | Microsoft Docs
description: Modello di dati di Application Insights per la telemetria delle tracce
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: df85aafc81b199610c02f0faecb06e804fda24bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899290"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria di traccia: Modello di dati di Application Insights

In [Application Insights](../../azure-monitor/app/app-insights-overview.md), la telemetria di traccia rappresenta le istruzioni di traccia in stile `printf` che vengono cercate come testo. `Log4Net`, `NLog` e altre voci del file di log basato su testo vengono convertite in istanze di questo tipo. La traccia non dispone di misure come l'estendibilità.

## <a name="message"></a>`Message`

Messaggio di traccia.

Lunghezza massima: 32768 caratteri

## <a name="severity-level"></a>Livello di gravità

Livello di gravità della traccia. Il valore può essere `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Proprietà personalizzate

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare i log di traccia .NET in Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Esplorare i log di traccia Java in Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](data-model.md).
- [Scrivere dati di telemetria di traccia personalizzata](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Verificare quali [piattaforme](../../azure-monitor/app/platforms.md) supportano Application Insights.
