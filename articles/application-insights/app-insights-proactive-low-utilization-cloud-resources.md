---
title: Rilevamento intelligente - Sottoutilizzo di risorse cloud rilevato da Azure Application Insights | Microsoft Docs
description: Monitorare le applicazioni con Azure Application Insights per rilevare casi di sottoutilizzo delle risorse cloud.
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
ms.openlocfilehash: 8382f6047ae222a01cc0e8d6ca9dcf5593d0dff6
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="low-utilization-of-cloud-resources-preview"></a>Sottoutilizzo delle risorse cloud (anteprima)

Application Insights analizza automaticamente l'utilizzo della CPU di ogni istanza del ruolo nell'applicazione e rileva le istanze con sottoutilizzo della CPU. Questo rilevamento consente di ridurre le risorse di Azure e di conseguenza i costi, riducendo il numero di istanze del ruolo usate da ogni ruolo oppure riducendo il numero di ruoli.

Questa funzionalità non richiede una configurazione specifica a parte la [configurazione dei contatori delle prestazioni](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) per l'app. La funzionalità è attiva quando l'app genera una quantità sufficiente di dati di telemetria del contatore delle prestazioni della CPU (% tempo processore).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando si riceve questo tipo di notifica di rilevamento intelligente?
Una notifica tipica viene generata quando numerose istanze del ruolo Web/di lavoro presentano un sottoutilizzo della CPU.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>L'app utilizza sicuramente una quantità troppo bassa di risorse?
No, una notifica non significa che l'app utilizza sicuramente una quantità troppo bassa di risorse. Anche se i modelli di sottoutilizzo della CPU indicano in genere che il consumo di risorse può essere ridotto, questo comportamento può essere tipico di un ruolo specifico o avere una giustificazione naturale e in questo caso può essere ignorato. Potrebbero, ad esempio, essere necessarie più istanze per altre risorse, ad esempio per la memoria o la rete e non per la CPU.

## <a name="how-do-i-fix-it"></a>Risoluzione
Le notifiche includono informazioni di diagnostica a supporto del processo di diagnostica:
1. **Valutazione.** La notifica indica i ruoli dell'app che presentano un sottoutilizzo della CPU. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Quanti ruoli presentano un sottoutilizzo della CPU e quante istanze in ogni ruolo usano una quantità troppo bassa di CPU? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosi.** Il rilevamento include la percentuale di CPU usata e indica l'utilizzo della CPU di ogni istanza nel tempo. È anche possibile usare gli elementi e i report correlati che rimandano a informazioni di supporto, ad esempio i percentili di utilizzo della CPU, per diagnosticare meglio il problema.
