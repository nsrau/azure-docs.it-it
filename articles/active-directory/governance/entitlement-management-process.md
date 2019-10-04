---
title: Richiedere il processo e le notifiche tramite posta elettronica in Azure AD gestione dei diritti (anteprima)-Azure Active Directory
description: Informazioni sul processo di richiesta per un pacchetto di accesso e quando le notifiche di posta elettronica vengono inviate in Azure Active Directory gestione dei diritti (anteprima).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb5f322d8bc974274f7f2da7811b124499224635
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678138"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Richiedere il processo e le notifiche tramite posta elettronica in Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quando un utente invia una richiesta a un pacchetto di accesso, viene avviato un processo per recapitare la richiesta. Azure AD la gestione dei diritti consente inoltre di inviare notifiche tramite posta elettronica ai responsabili approvazione e ai richiedenti quando si verificano eventi chiave durante il processo.

Questo articolo descrive il processo di richiesta e le notifiche di posta elettronica inviate.

## <a name="request-process"></a>Richiedi processo

Un utente che deve accedere a un pacchetto di accesso può inviare una richiesta di accesso. A seconda della configurazione dei criteri, la richiesta potrebbe richiedere un'approvazione. Quando una richiesta viene approvata, un processo inizia a assegnare l'accesso utente a ogni risorsa nel pacchetto di accesso. Nel diagramma seguente viene illustrata una panoramica del processo e dei diversi Stati.

![Diagramma del processo di approvazione](./media/entitlement-management-process/request-process.png)

| Stato | Descrizione |
| --- | --- |
| Inviato | L'utente invia una richiesta. |
| In attesa di approvazione | Se il criterio per un pacchetto di accesso richiede l'approvazione, una richiesta passa a approvazione in sospeso. |
| Scaduto | Se nessun approvatore approva una richiesta entro il timeout della richiesta di approvazione, la richiesta scade. Per riprovare, l'utente dovrà inviare di nuovo la richiesta. |
| Negata | Il responsabile approvazione nega una richiesta. |
| Approvata | Il responsabile approvazione approva una richiesta. |
| Distribuzione | All'utente **non** è stato assegnato l'accesso a tutte le risorse nel pacchetto di accesso. Se si tratta di un utente esterno, è possibile che l'utente non abbia ancora eseguito l'accesso alla directory delle risorse e abbia accettato la richiesta di consenso. |
| Recapitati | All'utente è stato assegnato l'accesso a tutte le risorse nel pacchetto di accesso. |
| Accesso esteso | Se nel criterio sono consentite le estensioni, l'utente ha esteso l'assegnazione. |
| Accesso scaduto | L'accesso dell'utente al pacchetto di accesso è scaduto. Per ottenere nuovamente l'accesso, l'utente dovrà inviare una richiesta. |

## <a name="email-notifications"></a>Notifiche tramite posta elettronica

Se si è un responsabile approvazione, vengono inviate notifiche tramite posta elettronica quando è necessario approvare una richiesta di accesso e quando una richiesta di accesso è stata completata. Se si è un richiedente, viene inviata una notifica tramite posta elettronica che indica lo stato della richiesta. Il diagramma seguente illustra quando vengono inviate queste notifiche tramite posta elettronica.

![Processo di posta elettronica di gestione dei diritti](./media/entitlement-management-process/email-notifications.png)

Nella tabella seguente vengono fornite informazioni più dettagliate su ognuna di queste notifiche di posta elettronica.

| # | Oggetto del messaggio di posta elettronica | Quando viene inviato | Inviato a |
| --- | --- | --- | --- |
| 1 | Azione richiesta: Verifica la richiesta di accesso da *[richiedente]* a *[pacchetto di accesso]* di *[DATE]* | Quando un richiedente invia una richiesta di un pacchetto di accesso | Tutti i responsabili approvazione |
| 2 | Azione richiesta: Verifica la richiesta di accesso da *[richiedente]* a *[pacchetto di accesso]* di *[DATE]* | X giorni prima del timeout della richiesta di approvazione | Tutti i responsabili approvazione |
| 3 | Notifica di stato: la richiesta di accesso *[richiedente]* a *[Access Package]* è scaduta | Quando i responsabili approvazione non approvano o negano una richiesta di accesso entro la durata della richiesta | Richiedente |
| 4 | Notifica di stato: la richiesta di accesso *[richiedente]* a *[pacchetto di accesso]* è stata completata | Quando il primo responsabile approvazione approva o nega una richiesta di accesso | Tutti i responsabili approvazione |
| 5 | Accesso negato a *[pacchetto di accesso]* | Quando un richiedente ha negato l'accesso al pacchetto di accesso | Richiedente |
| 6 | È ora possibile accedere a *[pacchetto di accesso]*  | Quando a un richiedente è stato concesso l'accesso a ogni risorsa nel pacchetto di accesso | Richiedente |
| 7 | L'accesso a *[pacchetto di accesso]* scade tra X giorno/i | X giorni prima della scadenza dell'accesso del richiedente al pacchetto di accesso | Richiedente |
| 8 | L'accesso a *[pacchetto di accesso]* è scaduto | Quando l'accesso del richiedente a un pacchetto di accesso scade | Richiedente |

### <a name="access-request-emails"></a>Messaggi di posta elettronica di richiesta di accesso

Quando un richiedente invia una richiesta di accesso per un pacchetto di accesso configurato per richiedere l'approvazione, tutti i responsabili approvazione configurati nel criterio ricevono una notifica tramite posta elettronica con i dettagli della richiesta. I dettagli includono il nome, l'organizzazione, la data di inizio e di fine dell'accesso, se specificato, la motivazione aziendale, il momento in cui la richiesta è stata inviata e la scadenza della richiesta. Il messaggio di posta elettronica include un collegamento in cui i responsabili approvazione possono approvare o rifiutare la richiesta di accesso. Ecco una notifica di posta elettronica di esempio inviata a un responsabile approvazione quando un richiedente invia una richiesta di accesso.

![Verifica indirizzo di posta elettronica richiesta di accesso](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Email approvate o negate

I richiedenti ricevono una notifica quando la richiesta di accesso è approvata e disponibile per l'accesso o quando la richiesta di accesso viene negata. Quando un responsabile approvazione riceve una richiesta di accesso inviata da un richiedente, può approvare o rifiutare la richiesta di accesso. Il responsabile approvazione deve aggiungere una motivazione aziendale per la decisione.

Quando viene approvata una richiesta di accesso, la gestione dei diritti avvia il processo di concessione dell'accesso del richiedente a ogni risorsa nel pacchetto di accesso. Dopo che il richiedente ha concesso l'accesso a ogni risorsa nel pacchetto di accesso, viene inviata una notifica tramite posta elettronica al richiedente che la richiesta di accesso è stata approvata e che ora ha accesso al pacchetto di accesso. Ecco una notifica di posta elettronica di esempio inviata a un richiedente quando viene concesso l'accesso a un pacchetto di accesso.

Quando una richiesta di accesso viene negata, viene inviata una notifica tramite posta elettronica al richiedente. Ecco una notifica di posta elettronica di esempio inviata a un richiedente quando la richiesta di accesso viene negata.

### <a name="expired-access-request-emails"></a>Messaggi di posta elettronica di richiesta di accesso scaduti

I richiedenti ricevono una notifica quando la richiesta di accesso è scaduta. Quando un richiedente invia una richiesta di accesso, la richiesta ha una durata della richiesta dopo la quale scade. Se non sono presenti responsabili approvazione che inviano una decisione approva/nega, la richiesta continua a rimanere in uno stato di approvazione in sospeso. Quando la richiesta raggiunge la durata di scadenza configurata, la richiesta scade e non può più essere approvata o negata dai responsabili approvazione. In questo caso, la richiesta entra in uno stato scaduto. Non è più possibile approvare o negare una richiesta scaduta. Una notifica di posta elettronica viene inviata al richiedente che la richiesta di accesso è scaduta e che è necessario inviare nuovamente la richiesta di accesso. Ecco una notifica di posta elettronica di esempio inviata a un richiedente quando la richiesta di accesso è scaduta.

![Messaggio di richiesta di accesso scaduto](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'accesso a un pacchetto di accesso](entitlement-management-request-access.md)
- [Approva o rifiuta le richieste di accesso](entitlement-management-request-approve.md)
