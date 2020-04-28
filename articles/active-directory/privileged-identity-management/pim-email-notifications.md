---
title: Notifiche tramite posta elettronica in PIM - Azure Active Directory Documenti Microsoft
description: Descrive le notifiche tramite posta elettronica in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/21/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 201abd24bc4056337f1ffecd2dabd002ae352c74
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866427"
---
# <a name="email-notifications-in-pim"></a>Notifiche tramite posta elettronica in PIM

Privileged Identity Management (PIM) consente di sapere quando si verificano eventi importanti nell'organizzazione di Azure Active Directory (Azure AD), ad esempio quando un ruolo viene assegnato o attivato. Privileged Identity Management ti tiene informato inviando notifiche e-mail a te e agli altri partecipanti. I messaggi di posta elettronica possono includere collegamenti ad attività importanti, quali l'attivazione o il rinnovo di un ruolo. Questo articolo descrive l'aspetto dei messaggi di posta elettronica, quando vengono inviati e chi li riceve.

## <a name="sender-email-address-and-subject-line"></a>Indirizzo e-mail del mittente e riga dell'oggetto

I messaggi di posta elettronica inviati dalla gestione delle identità con privilegi per i ruoli delle risorse di Azure AD e Azure hanno il seguente indirizzo di posta elettronica del mittente:Emails sent from Privileged Identity Management for both Azure AD and Azure resource roles have the following sender email address:

- Indirizzo di posta elettronica: **azure-noreply\@microsoft.com**
- Nome visualizzato: Microsoft Azure

Questi messaggi di posta elettronica includono un prefisso **PIM** nella riga dell'oggetto. Ad esempio:

- PIM: Ad Alain Charon è stato assegnato in modo permanente il ruolo Lettore di backup

## <a name="notifications-for-azure-ad-roles"></a>Notifiche per i ruoli di Azure ADNotifications for Azure AD roles

Gestione identità con privilegi invia messaggi di posta elettronica quando si verificano gli eventi seguenti per i ruoli di Azure AD:Privileged Identity Management sends emails when the following events occur for Azure AD roles:

- Quando l'attivazione di un ruolo con privilegi è in attesa di approvazione
- Quando la richiesta di attivazione di un ruolo con privilegi viene completata
- Quando è abilitata la gestione delle identità con privilegi di Azure ADWhen Azure AD Privileged Identity Management is enabled

I destinatari di questi messaggi di posta elettronica per i ruoli di Azure AD variano a seconda del ruolo dell'utente, dell'evento e dell'impostazione di notifica:

| Utente | Attivazione del ruolo in attesa di approvazione | Richiesta di attivazione del ruolo completata | PIM è abilitato |
| --- | --- | --- | --- |
| Amministratore dei ruoli con privilegi</br>(Attivato/Idoneo) | Sì</br>(solo se non è specificato nessun responsabile approvazione esplicito) | Sì* | Sì |
| Amministratore della sicurezza</br>(Attivato/Idoneo) | No | Sì* | Sì |
| Amministratore globale</br>(Attivato/Idoneo) | No | Sì* | Sì |

\* Se l'impostazione di [**Notifiche**](pim-how-to-change-default-settings.md#notifications) è **Abilita**.

Di seguito viene riportato un esempio di messaggio di posta elettronica inviato quando un utente attiva un ruolo di Azure AD per l'organizzazione fittizia Contoso.

![Nuovo messaggio di posta elettronica di gestione delle identità con privilegi per i ruoli di Azure ADNew Privileged Identity Management email for Azure AD roles](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Posta elettronica del digest di Gestione identità con privilegi settimanale per i ruoli di Azure ADWeekly Privileged Identity Management digest email for Azure AD roles

Un messaggio di posta elettronica di riepilogo della gestione delle identità con privilegi per i ruoli di Azure AD viene inviato agli amministratori dei ruoli con privilegi, agli amministratori di sicurezza e agli amministratori globali che hanno abilitato la gestione delle identità con privilegi. Questo messaggio di posta elettronica settimanale fornisce un'istantanea delle attività di Gestione identità privilegiate per la settimana, nonché assegnazioni di ruolo con privilegi. È disponibile solo per i tenant nel cloud pubblico. Di seguito è illustrato un messaggio di posta elettronica di esempio:

![Posta elettronica del digest di Gestione identità con privilegi settimanale per i ruoli di Azure ADWeekly Privileged Identity Management digest email for Azure AD roles](./media/pim-email-notifications/email-directory-weekly.png)

Il messaggio di posta elettronica include quattro sezioni:

| Riquadro | Descrizione |
| --- | --- |
| **Users activated** (Utenti attivati) | Numero di volte per cui gli utenti hanno attivato il proprio ruolo idoneo nel tenant. |
| **Users made permanent** (Utenti resi permanenti) | Numero di volte per cui un utente con un'assegnazione idonea viene reso permanente. |
| **Assegnazioni di ruolo in Gestione identità con privilegiRole assignments in Privileged Identity Management** | Numero di volte in cui agli utenti viene assegnato un ruolo idoneo all'interno di Gestione identità con privilegi. |
| **Role assignments outside of PIM** (Assegnazioni di ruoli all'esterno di PIM) | Numero di volte in cui agli utenti viene assegnato un ruolo permanente all'esterno di Gestione identità con privilegi (all'interno di Azure AD). |

La sezione **Overview of your top roles** (Panoramica dei ruoli principali) elenca i cinque ruoli principali nel tenant in base al numero totale di amministratori permanenti e idonei per ogni ruolo. Il collegamento **Intervieni** apre la [procedura guidata PIM](pim-security-wizard.md), che consente di convertire gli amministratori permanenti in amministratori idonei in batch.

## <a name="email-timing-for-activation-approvals"></a>Tempi di posta elettronica per le approvazioni di attivazione

Quando gli utenti attivano il proprio ruolo e l'impostazione del ruolo richiede l'approvazione, gli approvatori riceveranno tre messaggi di posta elettronica per ogni approvazione:When users activates their role and the role setting requires approval, approvers will receive three emails for each approval:

- Richiesta di approvare o rifiutare la richiesta di attivazione dell'utente (inviata dal motore di approvazione delle richieste)
- La richiesta dell'utente viene approvata (inviata dal motore di approvazione delle richieste)
- Il ruolo dell'utente è attivato (inviato da Privileged Identity Management)

Le prime due e-mail inviate dal motore di approvazione delle richieste possono essere ritardate. Attualmente, il 90% delle e-mail richiede da tre a dieci minuti, ma per l'1% dei clienti può essere molto più lungo, fino a quindici minuti.

Se una richiesta di approvazione viene approvata nel portale di Azure prima dell'invio del primo messaggio di posta elettronica, il primo messaggio di posta elettronica non verrà più attivato e gli altri approvatori non riceveranno alcuna notifica tramite posta elettronica della richiesta di approvazione. Potrebbe sembrare come se non hanno ottenuto un'e-mail, ma è il comportamento previsto.

## <a name="pim-emails-for-azure-resource-roles"></a>Messaggi di posta elettronica PIM per i ruoli delle risorse di Azure

Gestione identità con privilegi invia messaggi di posta elettronica ai proprietari e agli amministratori di accesso utente quando si verificano gli eventi seguenti per i ruoli delle risorse di Azure:Privileged Identity Management sends emails to Owners and User Access Administrators when the following events occur for Azure resource roles:

- Quando un'assegnazione di ruolo è in attesa di approvazione
- Quando un ruolo viene assegnato
- Quando un ruolo sta per scadere
- Quando un ruolo è idoneo per l'estensione
- Quando un ruolo viene rinnovato da un utente finale
- Quando una richiesta di attivazione del ruolo viene completata

Gestione identità con privilegi invia messaggi di posta elettronica agli utenti finali quando si verificano gli eventi seguenti per i ruoli delle risorse di Azure:Privileged Identity Management sends emails to end users when the following events occur for Azure resource roles:

- Quando viene assegnato un ruolo all'utente
- Quando un ruolo utente è scaduto
- Quando un ruolo utente è stato esteso
- Quando la richiesta di attivazione del ruolo di un utente viene completata

Di seguito viene riportato un esempio di messaggio di posta elettronica inviato quando a un utente viene assegnato un ruolo di risorsa di Azure per l'organizzazione fittizia Contoso.

![Nuova posta elettronica di gestione delle identità con privilegi per i ruoli delle risorse di AzureNew Privileged Identity Management email for Azure resource roles](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni dei ruoli di Azure AD in Gestione delle identità con privilegiConfigure Azure AD role settings in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Approvare o rifiutare le richieste per i ruoli di Azure AD in Gestione delle identità con privilegiApprove or deny requests for Azure AD roles in Privileged Identity Management](azure-ad-pim-approval-workflow.md)
