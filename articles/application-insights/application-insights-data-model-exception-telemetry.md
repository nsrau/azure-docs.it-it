---
title: Modello di dati di Azure Application Insights Telemetry - Telemetria delle eccezioni | Microsoft Docs
description: Modello di dati di Application Insights per la telemetria delle eccezioni
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: dc5480b90ef6b5520f47c51f0c105202d7071089
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093594"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetria delle eccezioni: modello di dati di Application Insights

In [Application Insights](app-insights-overview.md), un'istanza di eccezione rappresenta un'eccezione gestita o non gestita che si è verificata durante l'esecuzione dell'applicazione monitorata.

## <a name="problem-id"></a>ID problema

Identificatore del punto nel codice in cui è stata generata l'eccezione. Usato per il raggruppamento delle eccezioni. In genere una combinazione di tipo di eccezione e una funzione dallo stack di chiamate.

Lunghezza massima: 1024 caratteri

## <a name="severity-level"></a>Livello di gravità

Livello di gravità della traccia. Il valore può essere `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Dettagli eccezione

(Da aggiungere)

## <a name="custom-properties"></a>Proprietà personalizzate

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Misure personalizzate

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](application-insights-data-model.md).
- Informazioni su come [diagnosticare le eccezioni nelle app Web con Application Insights](app-insights-asp-net-exceptions.md).
- Verificare quali [piattaforme](app-insights-platforms.md) supportano Application Insights.
