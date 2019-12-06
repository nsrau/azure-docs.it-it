---
title: Modello di dati di telemetria delle eccezioni di applicazione Azure Insights
description: Modello di dati di Application Insights per la telemetria delle eccezioni
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 7decac2e5e65ff7eedb374858fe15360b4a1b2c5
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872622"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetria delle eccezioni: modello di dati di Application Insights

In [Application Insights](../../azure-monitor/app/app-insights-overview.md), un'istanza di eccezione rappresenta un'eccezione gestita o non gestita che si è verificata durante l'esecuzione dell'applicazione monitorata.

## <a name="problem-id"></a>ID problema

Identificatore del punto nel codice in cui è stata generata l'eccezione. Usato per il raggruppamento delle eccezioni. In genere una combinazione di tipo di eccezione e una funzione dallo stack di chiamate.

Lunghezza massima: 1024 caratteri

## <a name="severity-level"></a>Livello di gravità

Livello di gravità della traccia. Il valore può essere `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Dettagli eccezione

(Da aggiungere)

## <a name="custom-properties"></a>Proprietà personalizzate

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Misure personalizzate

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](data-model.md).
- Informazioni su come [diagnosticare le eccezioni nelle app Web con Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Verificare quali [piattaforme](../../azure-monitor/app/platforms.md) supportano Application Insights.
