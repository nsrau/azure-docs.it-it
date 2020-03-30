---
title: Azure Application Insights Data Model - Trace Telemetry
description: Modello di dati di Application Insights per la telemetria delle tracce
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 31958b26cdb8a7897cf0051af6600014c07949fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671954"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria delle tracce: modello di dati di Application Insights

In [Application Insights](../../azure-monitor/app/app-insights-overview.md), la telemetria di traccia rappresenta le istruzioni di traccia in stile `printf` che vengono cercate come testo. `Log4Net`, `NLog` e altre voci del file di log basato su testo vengono convertite in istanze di questo tipo. La traccia non dispone di misure come l'estendibilità.

## <a name="message"></a>Message

Messaggio di traccia.

Lunghezza massima: 32.768 caratteri

## <a name="severity-level"></a>Livello di gravità

Livello di gravità della traccia. Il valore può essere `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Proprietà personalizzate

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare i log di traccia .NET in Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Esplorare i log](../../azure-monitor/app/java-trace-logs.md)di traccia Java in Application Insights .
- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](data-model.md).
- [Scrivere dati di telemetria di traccia personalizzata](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Verificare quali [piattaforme](../../azure-monitor/app/platforms.md) supportano Application Insights.
