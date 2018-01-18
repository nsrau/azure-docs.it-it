---
title: Rilevamento intelligente - Potenziale perdita di memoria rilevata da Azure Application Insights | Microsoft Docs
description: Monitorare le applicazioni con Azure Application Insights per rilevare potenziali perdite di memoria.
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: e98caaa387418d746905990436b69925a591b260
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="memory-leak-detection-preview"></a>Rilevamento della perdita di memoria (anteprima)

Application Insights analizza automaticamente il consumo di memoria di ogni processo dell'applicazione e può generare un avviso in caso di potenziali perdite di memoria o aumento del consumo di memoria.

Questa funzionalità non richiede una configurazione specifica a parte la [configurazione dei contatori delle prestazioni](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) per l'app. La funzionalità è attiva quando l'app genera una quantità sufficiente di dati di telemetria dei contatori delle prestazioni della memoria (ad esempio, byte privati).


## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando si riceve questo tipo di notifica di rilevamento intelligente?
Una notifica tipica viene generata in seguito a un aumento consistente del consumo di memoria per un lungo periodo di tempo (alcune ore), in uno o più processi e/o in uno o più computer che fanno parte dell'applicazione.
Vengono usati algoritmi di apprendimento automatico per rilevare un aumento del consumo di memoria corrispondente a un modello di perdita di memoria distinguendolo da un aumento del consumo di memoria dovuto a un maggiore utilizzo dell'applicazione.

## <a name="does-my-app-definitely-have-a-problem"></a>L'app ha sicuramente un problema?
Una notifica non significa che l'app ha sicuramente un problema. Anche se i modelli di perdita di memoria indicano in genere un problema dell'applicazione, tali modelli potrebbero essere tipici di un processo specifico o avere una giustificazione naturale e in questo caso possono essere ignorati.

## <a name="how-do-i-fix-it"></a>Risoluzione
Le notifiche includono informazioni di diagnostica a supporto del processo di analisi diagnostica:
1. **Valutazione.** La notifica indica la quantità di aumento di memoria (in GB) e l'intervallo di tempo in cui la memoria è aumentata. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Quanti computer presentano il modello di perdita di memoria? Quante eccezioni sono state generate durante la potenziale perdita di memoria? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosi.** Il rilevamento contiene il modello di perdita di memoria, che mostra il consumo di memoria del processo nel tempo. È anche possibile usare gli elementi e i report correlati che rimandano a informazioni di supporto, per diagnosticare meglio il problema.
