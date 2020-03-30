---
title: Che cos'è la modalità solo report di accesso condizionale? - Azure Active Directory
description: In che modo la modalità solo report può essere utile per la distribuzione dei criteri di accesso condizionaleHow can report-only mode help with Conditional Access policy deployment
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd41e79a1e08c57e806f6ada32faccfa5fdf5792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295291"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Che cos'è la modalità solo report di accesso condizionale?

L'accesso condizionale è ampiamente utilizzato dai nostri clienti per rimanere al sicuro applicando i giusti controlli di accesso nelle giuste circostanze. Tuttavia, una delle sfide con la distribuzione di un criterio di accesso condizionale nell'organizzazione è determinare l'impatto per gli utenti finali. Può essere difficile prevedere il numero e i nomi degli utenti interessati da iniziative di distribuzione comuni, ad esempio il blocco dell'autenticazione legacy, la richiesta dell'autenticazione a più fattori per un popolamento di utenti o l'implementazione di criteri di rischio di accesso. 

La modalità solo report è un nuovo stato dei criteri di accesso condizionale che consente agli amministratori di valutare l'impatto dei criteri di accesso condizionale prima di abilitarli nel proprio ambiente.  Con il rilascio della modalità solo report:

- I criteri di accesso condizionale possono essere abilitati in modalità solo report.
- Durante l'accesso, i criteri in modalità solo report vengono valutati ma non applicati.
- I risultati vengono registrati nelle schede **Accesso condizionale** e Solo **report (anteprima)** dei dettagli del registro di accesso.
- I clienti con una sottoscrizione di Monitoraggio di Azure possono monitorare l'impatto dei criteri di accesso condizionale usando la cartella di lavoro Informazioni sull'accesso condizionale.

> [!WARNING]
> I criteri in modalità solo report che richiedono dispositivi conformi possono richiedere agli utenti su Mac, iOS e Android di selezionare un certificato del dispositivo durante la valutazione dei criteri, anche se la conformità del dispositivo non viene applicata. Questi messaggi di richiesta possono essere ripetuti fino a quando il dispositivo non viene reso conforme. Per impedire agli utenti finali di ricevere richieste durante l'accesso, escludere le piattaforme del dispositivo Mac, iOS e Android dai criteri di solo report che eseguono controlli di conformità del dispositivo.

![Scheda Solo report nel log di accesso di Azure ADReport-only tab in Azure AD sign-in log](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Risultati dei criteri

Quando un criterio in modalità solo report viene valutato per un determinato accesso, sono disponibili quattro nuovi valori di risultato possibili:When a policy in report-only mode is evaluated for a given sign-in, there are four new possible result values:

| Risultato | Descrizione |
| --- | --- |
| Solo rapporto: Operazione riuscita | Tutte le condizioni dei criteri configurate, i controlli delle sovvenzioni non interattivi necessari e i controlli della sessione sono stati soddisfatti. Ad esempio, un requisito di autenticazione a più fattori viene soddisfatto da un'attestazione di autenticazione a più fattori già presente nel token o un criterio del dispositivo conforme viene soddisfatto eseguendo un controllo del dispositivo in un dispositivo conforme. |
| Solo rapporto: Errore | Tutte le condizioni dei criteri configurate sono state soddisfatte, ma non tutti i controlli di concessione non interattivi necessari o i controlli della sessione sono stati soddisfatti. Ad esempio, un criterio si applica a un utente in cui è configurato un controllo di blocco o un dispositivo non riesce un criterio dispositivo conforme. |
| Solo report: è necessaria l'azione dell'utente | Tutte le condizioni dei criteri configurate sono state soddisfatte, ma sarebbe necessaria un'azione da parte dell'utente per soddisfare i controlli di concessione o i controlli di sessione richiesti. Con la modalità solo report, all'utente non viene richiesto di soddisfare i controlli necessari. Ad esempio, agli utenti non vengono richieste richieste le sfide di autenticazione a più fattori o le condizioni per l'utilizzo.   |
| Solo report: non applicato | Non tutte le condizioni dei criteri configurate sono state soddisfatte. Ad esempio, l'utente è escluso dal criterio o il criterio si applica solo a determinati percorsi denominati attendibili. |

## <a name="conditional-access-insights-workbook"></a>Cartella di lavoro approfondimenti sull'accesso condizionaleConditional Access Insights workbook

Gli amministratori hanno la possibilità di creare più criteri in modalità solo report, pertanto è necessario comprendere sia l'impatto individuale di ogni criterio che l'impatto combinato di più criteri valutati insieme. La nuova cartella di lavoro Informazioni sull'accesso condizionale consente agli amministratori di visualizzare le query di accesso condizionale e di monitorare l'impatto di un criterio per un determinato intervallo di tempo, un set di applicazioni e utenti. 
 
## <a name="next-steps"></a>Passaggi successivi

[Configurare la modalità solo report in un criterio di accesso condizionaleConfigure report-only mode on a Conditional Access policy](howto-conditional-access-report-only.md)
