---
title: Modello di dati di Azure Application Insights Telemetry - Telemetria delle eccezioni | Microsoft Docs
description: Modello di dati di Application Insights per la telemetria delle eccezioni
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 17a39660fce598610ff9a95e886282e6b3faffe4
ms.lasthandoff: 04/22/2017


---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetria delle eccezioni: modello di dati di Application Insights

Un'istanza di eccezione rappresenta un'eccezione gestita o non gestita che si è verificata durante l'esecuzione dell'applicazione monitorata.

## <a name="problem-id"></a>ID problema

Identificatore del punto nel codice in cui è stata generata l'eccezione. Usato per il raggruppamento delle eccezioni. In genere una combinazione di tipo di eccezione e una funzione dallo stack di chiamate.

Lunghezza massima: 1024 caratteri

## <a name="severity-level"></a>Livello di gravità

Livello di gravità della traccia. Il valore può essere `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Dettagli eccezione

Sezione in corso di lavoro...

## <a name="custom-properties"></a>Proprietà personalizzate

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Misure personalizzate

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](/application-insights-data-model.md).
- Informazioni su come [diagnosticare le eccezioni nelle app Web con Application Insights](/app-insights-asp-net-exceptions.md).
- Verificare quali [piattaforme](/app-insights-platforms.md) supportano Application Insights.

