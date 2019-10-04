---
title: Rilevamento intelligente - Crescita anomala nel volume delle eccezioni in Azure Application Insights | Documentazione Microsoft
description: Monitorare le eccezioni delle applicazioni con Azure Application Insights per individuare i modelli anomali nel volume delle eccezioni.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: a6e7e8e01ccb623a3ff340c318c9c238c919cb38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61298585"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Aumento anomalo nel volume delle eccezioni (anteprima)

Application Insights analizza automaticamente le eccezioni generate nell'applicazione e può avvisare in caso di modelli insoliti nei dati di telemetria delle eccezioni.

Questa funzionalità non richiede una configurazione specifica, a parte la [configurazione della segnalazione delle eccezioni](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) per l'app. È attiva quando l'app genera un numero sufficiente di dati di telemetria per le eccezioni.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando si riceve questo tipo di notifica di rilevamento intelligente?
Si riceve questo tipo di notifica se l'app presenta un aumento anomalo del numero di eccezioni di un tipo specifico in un giorno, in confronto a una linea di base calcolata nei sette giorni precedenti.
Vengono utilizzati algoritmi di Machine Learning per rilevare l'aumento del numero di eccezioni, tenendo conto di una crescita naturale nell'uso dell'applicazione.

## <a name="does-my-app-definitely-have-a-problem"></a>Verifica di eventuali problemi dell'app
Una notifica non significa che l'app ha sicuramente un problema. Anche se un numero eccessivo di eccezioni indica in genere problemi nell'applicazione, le eccezioni potrebbero essere innocue e gestite correttamente dall'applicazione.

## <a name="how-do-i-fix-it"></a>Risoluzione
Le notifiche includono informazioni di diagnostica a supporto del processo di diagnostica:
1. **Valutazione.** La notifica mostra il numero di utenti o di richieste interessate. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Il problema interessa solo alcune operazioni o tutto il traffico? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosi.** Il rilevamento contiene informazioni sul metodo da cui è stata generata l'eccezione, nonché il tipo di eccezione. È anche possibile usare gli elementi e i report correlati che rimandano a informazioni di supporto, per diagnosticare meglio il problema.