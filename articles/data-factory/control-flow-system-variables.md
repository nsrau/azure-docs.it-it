---
title: Variabili di sistema in Azure Data Factory | Microsoft Docs
description: "Questo articolo descrive le variabili di sistema supportate da Azure Data Factory. È possibile usare queste variabili nelle espressioni quando si definiscono le entità di Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: dbf0a12ec1a5348e7c73248cc389ec1a5a5d4a75
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variabili di sistema supportate da Azure Data Factory
Questo articolo descrive le variabili di sistema supportate da Azure Data Factory. È possibile usare queste variabili nelle espressioni quando si definiscono le entità di Data Factory. 

## <a name="pipeline-scope"></a>Ambito della pipeline:

| Nome variabile | Descrizione |
| --- | --- |
| @pipeline().DataFactory |Nome della data factory in cui viene eseguita la pipeline | 
| @pipeline().Pipeline |Nome della pipeline |
| @pipeline().RunId | ID dell'esecuzione della pipeline specifica | 
| @pipeline().TriggerType | Tipo di trigger che ha richiamato la pipeline (manuale, Utilità di pianificazione) | 
| @pipeline().TriggerId| ID del trigger che richiama la pipeline |
| @pipeline().TriggerName| Nome del trigger che richiama la pipeline |
| @pipeline().TriggerTime| Ora in cui il trigger ha richiamato la pipeline. L'ora del trigger è l'ora effettiva di attivazione, non l'orario pianificato. Ad esempio, viene restituito `13:20:08.0149599Z` anziché `13:20:00.00Z` |

## <a name="trigger-scope"></a>Ambito del trigger:

| Nome variabile | Descrizione |
| --- | --- |
| trigger().scheduledTime |Ora in cui il trigger è stato pianificato per richiamare l'esecuzione della pipeline. Ad esempio, per un trigger che viene generato ogni 5 minuti, questa variabile restituisce rispettivamente `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z`.|
| trigger().startTime |Ora in cui il trigger ha richiamato **effettivamente** l'esecuzione della pipeline. Per un trigger che viene attivato ogni 5 minuti, ad esempio, questa variabile potrebbe restituire rispettivamente `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` o un output simile.|

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sull'uso di queste variabili nelle espressioni, vedere [Expression language & functions](control-flow-expression-language-functions.md) (Linguaggio e funzioni delle espressioni). 

