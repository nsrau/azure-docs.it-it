---
title: Rilevamento - potenziale perdita di memoria rilevati da Azure Application Insights smart | Documenti Microsoft
description: Monitorare le applicazioni con Azure Application Insights potenziali perdite di memoria.
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
ms.openlocfilehash: 452d0a9d0231e54df2a7f1df76c3c2c0fcd94d87
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="memory-leak-detection-preview"></a>Rilevamento di perdite di memoria (anteprima)

Application Insights consente di analizzare il consumo di memoria di ogni processo dell'applicazione, automaticamente e visualizza un avviso sulle potenziali perdite di memoria o il consumo di memoria maggiore.

Questa funzionalità è necessaria alcuna configurazione speciale, diversa da [la configurazione dei contatori delle prestazioni](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters) per l'app. È attiva quando l'applicazione genera sufficiente telemetria di contatori delle prestazioni di memoria (ad esempio, i byte privati).


## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando otterrebbe questo tipo di notifica di rilevamento intelligente
Una tipico notifica seguirà un aumento del consumo di memoria per un lungo periodo di tempo (alcune ore), coerenza in uno o più processi e/o di uno o più computer che fanno parte dell'applicazione.
Algoritmi di Machine learning vengono utilizzati per il rilevamento di maggiore utilizzo di memoria che corrisponde a un modello di una perdita di memoria, a differenza di maggiore utilizzo di memoria a causa dell'aumento naturalmente l'utilizzo dell'applicazione.

## <a name="does-my-app-definitely-have-a-problem"></a>Verifica di eventuali problemi dell'app
Una notifica non significa che l'app ha sicuramente un problema. Sebbene in genere i modelli di perdita di memoria indicano problemi di un'applicazione, questi modelli potrebbe essere in genere un processo specifico, o potrebbe avere una motivazione aziendale naturale e possono essere ignorati.

## <a name="how-do-i-fix-it"></a>Risoluzione
Le notifiche riguardano informazioni di diagnostica per il supporto nel processo di analisi diagnostica:
1. **Valutazione.** La notifica Mostra è aumentare la quantità di memoria (in GB) e l'intervallo di tempo in cui la memoria è aumentato. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Il numero di computer si è verificato il modello di perdita di memoria? Il numero di eccezioni sono stato generato durante la potenziale perdita di memoria? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosticare.** Il rilevamento contiene il modello di perdita di memoria, che mostra il consumo di memoria del processo nel tempo. È inoltre possibile utilizzare gli elementi correlati e i report collegamento a informazioni che consentono inoltre di supporto di diagnosticare il problema.
