---
title: Rilevamento intelligente - Pacchetto per il rilevamento della sicurezza con Azure Application Insights | Microsoft Docs
description: Monitorare le applicazioni con Azure Application Insights per rilevare eventuali problemi di sicurezza.
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
ms.openlocfilehash: 837525d70f28c0710b8a8b86d411a7854402207f
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="application-security-detection-pack-preview"></a>Pacchetto per il rilevamento della sicurezza delle applicazioni (anteprima)

Application Insights analizza automaticamente i dati di telemetria generati dall'applicazione e rileva i potenziali problemi di sicurezza. Questa funzionalità consente di identificare i potenziali problemi di sicurezza e gestirli correggendo l'applicazione o adottando le misure di sicurezza necessarie.

Questa funzionalità non richiede una configurazione specifica a parte la [configurazione dell'app per l'invio di dati di telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando si riceve questo tipo di notifica di rilevamento intelligente?
Vengono rilevati tre tipi di problemi di sicurezza:
1. Accesso a un URL non sicuro: a un URL nell'applicazione è possibile accedere tramite HTTP e HTTPS. In genere, un URL che accetta le richieste HTTPS non deve accettare le richieste HTTP. Ciò può indicare un bug o un problema di sicurezza nell'applicazione.
2. Modulo non sicuro: un modulo (o un'altra richiesta "POST") nell'applicazione usa HTTP al posto di HTTPS. L'uso di HTTP può compromettere i dati utente inviati dal modulo.
3. Attività utente sospetta: uno stesso utente accede all'applicazione da paesi diversi circa nello stesso momento. Ad esempio, lo stesso utente ha eseguito l'accesso all'applicazione dalla Spagna e degli Stati Uniti nel corso di un'ora. Questo rilevamento indica un tentativo di accesso all'applicazione potenzialmente dannoso.

## <a name="does-my-app-definitely-have-a-security-issue"></a>L'app ha sicuramente un problema di sicurezza?
No, una notifica non significa che l'app ha sicuramente un problema di sicurezza. In molti casi se viene rilevato uno degli scenari precedenti può significare che è presente un problema di sicurezza. Il rilevamento, tuttavia, potrebbe avere una giustificazione naturale e in questo caso può essere ignorato.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Come è possibile risolvere il problema se viene rilevato l'accesso a un URL non sicuro?
1. **Valutazione.** La notifica indica il numero di utenti che hanno eseguito l'accesso a URL non sicuri e l'URL maggiormente interessato dal problema di accesso non sicuro. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Che percentuale di utenti ha eseguito l'accesso a URL non sicuri? Quanti sono gli URL interessati? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosi.** Il rilevamento fornisce l'elenco di richieste non sicure e gli elenchi di URL e utenti interessati, per aiutare a diagnosticare meglio il problema.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Come è possibile risolvere il problema se viene rilevato un modulo non sicuro?
1. **Valutazione.** La notifica indica il numero di moduli non sicuri e il numero di utenti i cui dati sono stati potenzialmente compromessi. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Quale modulo è implicato nel maggior numero di trasmissioni non sicure e qual è la distribuzione delle trasmissioni non sicure nel tempo? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosi.** Il rilevamento fornisce l'elenco di moduli non sicuri, con il numero di trasmissioni non sicure per ogni modulo, per aiutare a diagnosticare meglio il problema.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Come è possibile risolvere il problema se viene rilevata attività utente sospetta?
1. **Valutazione.** La notifica indica il numero di utenti diversi che hanno avuto un comportamento sospetto. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Da quali paesi provengono le richieste sospette? Quale utente è stato il più sospetto? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosi.** Il rilevamento fornisce l'elenco di utenti sospetti e l'elenco di paesi per ogni utente, per aiutare a diagnosticare meglio il problema.
