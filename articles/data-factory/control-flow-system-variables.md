---
title: Variabili di sistema in Azure Data Factory | Microsoft Docs
description: Questo articolo descrive le variabili di sistema supportate da Azure Data Factory. È possibile usare queste variabili nelle espressioni quando si definiscono le entità di Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: d932164f972169c93e368a41c09df039196341b5
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141645"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variabili di sistema supportate da Azure Data Factory
Questo articolo descrive le variabili di sistema supportate da Azure Data Factory. È possibile usare queste variabili nelle espressioni quando si definiscono le entità di Data Factory.

## <a name="pipeline-scope"></a>Ambito della pipeline
È possibile fare riferimento a queste variabili di sistema in un punto qualsiasi della pipeline JSON.

| Nome variabile | Descrizione |
| --- | --- |
| @pipeline().DataFactory |Nome della data factory in cui viene eseguita la pipeline |
| @pipeline().Pipeline |Nome della pipeline |
| @pipeline().RunId | ID dell'esecuzione della pipeline specifica |
| @pipeline().TriggerType | Tipo di trigger che ha richiamato la pipeline (manuale, Utilità di pianificazione) |
| @pipeline().TriggerId| ID del trigger che richiama la pipeline |
| @pipeline().TriggerName| Nome del trigger che richiama la pipeline |
| @pipeline().TriggerTime| Ora in cui il trigger ha richiamato la pipeline. L'ora del trigger è l'ora effettiva di attivazione, non l'orario pianificato. Ad esempio, viene restituito `13:20:08.0149599Z` anziché `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Ambito del trigger di pianificazione
È possibile fare riferimento a queste variabili di sistema in un punto qualsiasi del trigger JSON se il trigger è di tipo "ScheduleTrigger".

| Nome variabile | DESCRIZIONE |
| --- | --- |
| @trigger().scheduledTime |Ora in cui il trigger è stato pianificato per richiamare l'esecuzione della pipeline. Ad esempio, per un trigger che viene generato ogni 5 minuti, questa variabile restituisce rispettivamente `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z`.|
| @trigger().startTime |Ora in cui il trigger ha richiamato **effettivamente** l'esecuzione della pipeline. Per un trigger che viene attivato ogni 5 minuti, ad esempio, questa variabile potrebbe restituire rispettivamente `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` o un output simile. (Nota: Il timestamp è per impostazione predefinita nel formato ISO 8601|

## <a name="tumbling-window-trigger-scope"></a>Ambito del trigger della finestra a cascata
È possibile fare riferimento a queste variabili di sistema in un punto qualsiasi del trigger JSON se il trigger è di tipo "TumblingWindowTrigger".
(Nota: Il timestamp è per impostazione predefinita nel formato ISO 8601

| Nome variabile | Descrizione |
| --- | --- |
| @trigger().outputs.windowStartTime |Ora di avvio della finestra in cui il trigger è stato pianificato per richiamare l'esecuzione della pipeline. Se il trigger della finestra a cascata ha una frequenza "oraria", questa indica l'ora di inizio.|
| @trigger().outputs.windowEndTime |Ora di fine della finestra in cui il trigger è stato pianificato per richiamare l'esecuzione della pipeline. Se il trigger della finestra a cascata ha una frequenza "oraria", questa indica l'ora di fine.|
## <a name="next-steps"></a>Passaggi successivi
Per informazioni sull'uso di queste variabili nelle espressioni, vedere [Expression language & functions](control-flow-expression-language-functions.md) (Linguaggio e funzioni delle espressioni).
