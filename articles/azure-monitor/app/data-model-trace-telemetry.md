---
title: 'Modello di dati di applicazione Azure Insights: telemetria di traccia'
description: Modello di dati di Application Insights per la telemetria delle tracce
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8fea4bbf590816b2ef168a2ed16f197389ee282e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320545"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria delle tracce: modello di dati di Application Insights

In [Application Insights](./app-insights-overview.md), la telemetria di traccia rappresenta le istruzioni di traccia in stile `printf` che vengono cercate come testo. `Log4Net`, `NLog` e altre voci del file di log basato su testo vengono convertite in istanze di questo tipo. La traccia non dispone di misure come l'estendibilità.

## <a name="message"></a>Message

Messaggio di traccia.

Lunghezza massima: 32.768 caratteri

## <a name="severity-level"></a>Livello di gravità

Livello di gravità della traccia. Il valore può essere `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Proprietà personalizzate

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare i log di traccia .NET in Application Insights](./asp-net-trace-logs.md).
- [Esplorare i log di traccia Java in Application Insights](./java-trace-logs.md).
- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](data-model.md).
- [Scrivere dati di telemetria di traccia personalizzata](./api-custom-events-metrics.md#tracktrace)
- Verificare quali [piattaforme](./platforms.md) supportano Application Insights.

