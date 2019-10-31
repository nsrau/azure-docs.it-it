---
title: Che cos'è la modalità solo report di accesso condizionale? -Azure Active Directory
description: Come può essere utile la modalità solo report per la distribuzione dei criteri di accesso condizionale
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4289f4870ca03657afabec07049b3333412f3899
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180329"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Che cos'è la modalità solo report di accesso condizionale?

L'accesso condizionale viene ampiamente usato dai clienti per garantirne la sicurezza applicando i controlli di accesso corretti nelle circostanze giuste. Tuttavia, uno dei problemi relativi alla distribuzione di un criterio di accesso condizionale nell'organizzazione consiste nel determinare l'effetto degli utenti finali. Può essere difficile prevedere il numero e i nomi degli utenti interessati da iniziative comuni di distribuzione, ad esempio il blocco dell'autenticazione legacy, la richiesta di autenticazione a più fattori per una popolazione di utenti o l'implementazione di criteri di rischio di accesso. 

La modalità solo report è un nuovo stato dei criteri di accesso condizionale che consente agli amministratori di valutare l'impatto dei criteri di accesso condizionale prima di abilitarli nel proprio ambiente.  Con il rilascio della modalità solo report:

- I criteri di accesso condizionale possono essere abilitati in modalità di sola segnalazione.
- Durante l'accesso, i criteri in modalità solo report vengono valutati ma non applicati. I risultati vengono registrati nelle schede **accesso condizionale** e **solo report (anteprima)** dei dettagli del log di accesso.
- I clienti con una sottoscrizione di monitoraggio di Azure possono monitorare l'effetto dei criteri di accesso condizionale usando la cartella di lavoro delle informazioni dettagliate sull'accesso condizionale.

> [!WARNING]
> I criteri in modalità solo report che richiedono dispositivi conformi possono richiedere agli utenti di Mac, iOS e Android di selezionare un certificato del dispositivo durante la valutazione dei criteri, anche se non viene applicata la conformità del dispositivo. Queste richieste possono essere ripetute fino a quando il dispositivo non viene reso conforme. Per impedire agli utenti finali di ricevere richieste durante l'accesso, escludere le piattaforme per dispositivi Mac, iOS e Android dai criteri di solo report che eseguono i controlli di conformità del dispositivo.

![Scheda solo report nel log di accesso Azure AD](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Risultati dei criteri

Quando un criterio in modalità di sola segnalazione viene valutato per un determinato accesso, sono disponibili quattro nuovi valori possibili per i risultati:

| Risultato | Description |
| --- | --- |
| Solo report: operazione riuscita | Sono state soddisfatte tutte le condizioni dei criteri configurati, i controlli di concessione richiesti non interattivi e i controlli della sessione. Un requisito di autenticazione a più fattori, ad esempio, viene soddisfatto da un'attestazione di autenticazione a più fattori già presente nel token o un criterio di dispositivo conforme viene soddisfatto eseguendo un controllo del dispositivo in un dispositivo conforme. |
| Solo report: errore | Tutte le condizioni dei criteri configurate sono state soddisfatte ma non sono stati soddisfatti tutti i controlli di concessione non interattivi o i controlli della sessione richiesti. Ad esempio, un criterio si applica a un utente in cui è configurato un controllo blocco oppure un dispositivo non riesce a rispettare un criterio per i dispositivi conformi. |
| Solo report: azione dell'utente obbligatoria | Tutte le condizioni dei criteri configurate sono state soddisfatte, ma l'azione dell'utente sarebbe necessaria per soddisfare i controlli di concessione o di sessione necessari. Con la modalità solo report, all'utente non viene richiesto di soddisfare i controlli necessari. Ad esempio, agli utenti non vengono richieste le condizioni per l'utilizzo di autenticazione a più fattori o le condizioni per l'utilizzo.   |
| Solo report: non applicato | Non sono state soddisfatte tutte le condizioni dei criteri configurati. Ad esempio, l'utente viene escluso dal criterio o il criterio si applica solo a determinati percorsi denominati attendibili. |

## <a name="conditional-access-insights-workbook"></a>Cartella di lavoro di Insights per l'accesso condizionale

Gli amministratori hanno la possibilità di creare più criteri in modalità solo report, quindi è necessario comprendere sia il singolo effetto di ogni criterio sia l'effetto combinato di più criteri valutati insieme. La nuova cartella di lavoro di Insights per l'accesso condizionale consente agli amministratori di visualizzare le query di accesso condizionale e di monitorare l'effetto di un criterio per un intervallo di tempo specifico, un set di applicazioni e utenti. 
 
## <a name="next-steps"></a>Passaggi successivi

[Configurare la modalità solo report in un criterio di accesso condizionale](howto-conditional-access-report-only.md)
