---
title: Rilevamento - Pack di rilevamento di sicurezza con Azure Application Insights smart | Documenti Microsoft
description: Monitorare l'applicazione con Azure Application Insights per potenziali problemi di sicurezza.
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
ms.openlocfilehash: 3e4604a154c16b785db1ab903587ae4a35d93c05
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="application-security-detection-pack-preview"></a>Service pack il rilevamento di sicurezza dell'applicazione (anteprima)

Application Insights automaticamente analizza i dati di telemetria generati dall'applicazione e rileva i potenziali problemi di sicurezza. Questa funzionalità consente di identificare potenziali problemi di protezione e gestirli tramite la correzione dell'applicazione o adottando le misure di sicurezza necessarie.

Questa funzionalità è necessaria alcuna configurazione speciale, diversa da [la configurazione dell'app per l'invio di dati di telemetria](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando otterrebbe questo tipo di notifica di rilevamento intelligente
Esistono tre tipi di problemi di sicurezza che vengono rilevati:
1. Accesso con URL non protetto: un URL nell'applicazione si accede tramite HTTP e HTTPS. In genere, un URL che accetti le richieste HTTPS non accettare le richieste HTTP. Questo può indicare un bug o problemi di protezione dell'applicazione.
2. Formato non protetto: un form (o altre richieste "POST") nell'applicazione viene utilizzato HTTP anziché HTTPS. Utilizzo di HTTP può compromettere i dati utente inviati dal modulo.
3. Attività utente sospetta: si accede all'applicazione da paesi diversi dallo stesso utente approssimativamente nello stesso momento. Ad esempio, l'utente stesso accesso all'applicazione (Spagna) e degli Stati Uniti all'interno della stessa ora. Questo rilevamento indica un tentativo di accesso potenzialmente dannose per l'applicazione.

## <a name="does-my-app-definitely-have-a-security-issue"></a>L'applicazione sicuramente dispone di un problema di sicurezza?
No, una notifica non significa che l'app abbia indubbiamente un problema di sicurezza. In molti casi, un'identificazione di uno degli scenari precedenti può indicare un problema di sicurezza. Tuttavia, il rilevamento dispone di una motivazione aziendale naturale e può essere ignorato.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Come risolvere il rilevamento di "Accesso con URL non protetto"?
1. **Valutazione.** La notifica fornisce il numero di utenti che gli URL non protetti e l'URL che è stata la maggior parte delle interessato dall'accesso non protetta. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** La percentuale di utenti di accedere al URL non protetto? URL di quanti sono stati interessati? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosticare.** Il rilevamento fornisce l'elenco di richieste non protette e gli elenchi di URL e gli utenti interessati, che consentono di isolare ulteriormente il problema.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Come risolvere il rilevamento "Modulo non sicuro"?
1. **Valutazione.** La notifica fornisce il numero di moduli non sicuri e numero di utenti i cui dati potenzialmente compromesso. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Il modulo è stato coinvolto nel maggior numero di trasmissioni non sicure e qual è la distribuzione delle trasmissioni non protette nel corso del tempo? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosticare.** Il rilevamento fornisce l'elenco dei formati non protetti e una suddivisione del numero di trasmissioni non sicure per ogni form, che consentono di isolare ulteriormente il problema.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Come risolvere il rilevamento "attività utente sospetta"?
1. **Valutazione.** La notifica fornisce il numero di utenti che si è verificato comportamenti sospetti. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Dei paesi in cui è stata originata le richieste sospette L'utente che era il più sospette? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosticare.** Il rilevamento fornisce l'elenco di utenti sospetti e l'elenco di paesi per ogni utente, che consentono di isolare ulteriormente il problema.
