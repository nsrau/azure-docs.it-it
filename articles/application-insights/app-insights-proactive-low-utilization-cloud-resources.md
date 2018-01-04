---
title: Rilevamento - utilizzo limitato delle risorse cloud rilevato da Azure Application Insights smart | Documenti Microsoft
description: Monitorare le applicazioni con Azure Application Insights per l'utilizzo limitato delle risorse cloud.
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
ms.openlocfilehash: 4c852d07d771a1eef0e6c2e4ef27cd36f31d8ddd
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="low-utilization-of-cloud-resources-preview"></a>Utilizzo limitato delle risorse cloud (anteprima)

Application Insights automaticamente consente di analizzare l'utilizzo della CPU di ogni istanza del ruolo nell'applicazione e rileva le istanze con utilizzo minimo della CPU. Questo rilevamento consente di ridurre le risorse di Azure e ridurre i costi, riducendo il numero di istanze del ruolo che prevede l'utilizzo di ogni ruolo o riducendo il numero di ruoli.

Questa funzionalità è necessaria alcuna configurazione speciale, diversa da [la configurazione dei contatori delle prestazioni](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters) per l'app. È attiva quando l'applicazione genera sufficiente telemetria di contatore delle prestazioni della CPU (% tempo processore).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando otterrebbe questo tipo di notifica di rilevamento intelligente
Una notifica tipico si verifica quando molte delle istanze del ruolo Web/di lavoro presentano un utilizzo ridotto della CPU.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>L'applicazione in modo definito utilizzano troppe risorse?
No, una notifica non significa che l'app è sicuramente utilizza troppe risorse. Anche se tali modelli di utilizzo minimo della CPU è in genere indicano che potrebbe ridurre il consumo di risorse, questo comportamento può essere utilizzato per il ruolo specifico, o potrebbe avere una motivazione aziendale naturale e può essere ignorato. Ad esempio, è possibile che più istanze sono necessari per le altre risorse, ad esempio non CPU e memoria o rete.

## <a name="how-do-i-fix-it"></a>Risoluzione
Le notifiche riguardano informazioni di diagnostica per il supporto nel processo di diagnostica:
1. **Valutazione.** La notifica Mostra i ruoli applicazione che presentano un utilizzo ridotto della CPU. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Quanti ruoli esibiti utilizzo minimo della CPU e il numero di istanze in ogni ruolo usare CPU insufficiente? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosticare.** Il rilevamento contiene la percentuale di CPU usata, con l'utilizzo della CPU di ogni istanza nel corso del tempo. È possibile utilizzare anche le relazioni di collegamento per altre informazioni, ad esempio i percentili dell'utilizzo della CPU, elementi correlati che consentono di isolare ulteriormente il problema.
