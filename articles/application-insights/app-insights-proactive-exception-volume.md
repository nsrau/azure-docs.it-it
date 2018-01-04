---
title: Rilevamento - aumento anomalo nel volume di eccezione, in Azure Application Insights smart | Documenti Microsoft
description: Monitorare le eccezioni delle applicazioni con Azure Application Insights per modelli insolite nel volume di eccezione.
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
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: 8030f3331a03170bb265c417a57725544bdc7d3f
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2017
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Aumento anomalo della volume eccezione (anteprima)

Application Insights automaticamente analizza le eccezioni generate nell'applicazione e può avvisare insoliti modelli nei dati di telemetria di eccezione.

Questa funzionalità è necessaria alcuna configurazione speciale, diversa da [configurazione di segnalazione eccezione](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) per l'app. È attiva quando l'applicazione genera dati di telemetria sufficienti eccezione.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando otterrebbe questo tipo di notifica di rilevamento intelligente
È possibile ottenere questo tipo di notifica se l'app è stato rilevato un aumento anomalo del numero di eccezioni di un tipo specifico durante un giorno, rispetto a una linea di base calcolata in sette giorni precedenti.
Vengono utilizzati algoritmi di Machine learning per rilevare l'aumento del numero di eccezioni, tenendo conto una crescita naturale l'utilizzo dell'applicazione.

## <a name="does-my-app-definitely-have-a-problem"></a>Verifica di eventuali problemi dell'app
Una notifica non significa che l'app ha sicuramente un problema. Anche se un numero eccessivo di eccezioni indica in genere problemi di un'applicazione, queste eccezioni potrebbero essere innocui e gestita correttamente dall'applicazione.

## <a name="how-do-i-fix-it"></a>Risoluzione
Le notifiche riguardano informazioni di diagnostica per il supporto nel processo di diagnostica:
1. **Valutazione.** La notifica è possibile visualizzare il numero di utenti o le richieste di quanti sono interessate. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Il problema interessa tutto il traffico o solo alcune operazioni? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosticare.** Il rilevamento contiene informazioni sul metodo da cui è stata generata l'eccezione, nonché il tipo di eccezione. È inoltre possibile utilizzare gli elementi correlati e i report collegamento a informazioni che consentono inoltre di supporto di diagnosticare il problema.