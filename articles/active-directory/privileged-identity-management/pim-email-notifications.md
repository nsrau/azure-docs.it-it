---
title: Notifiche tramite posta elettronica in PIM-Azure Active Directory | Microsoft Docs
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
ms.date: 04/28/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02fbfc83c16cb13376cce820f19b247a7cd7db59
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232309"
---
# <a name="email-notifications-in-pim"></a>Notifiche tramite posta elettronica in PIM

Privileged Identity Management (PIM) consente di stabilire quando si verificano eventi importanti nell'organizzazione di Azure Active Directory (Azure AD), ad esempio quando un ruolo viene assegnato o attivato. Privileged Identity Management vengono mantenuti informati inviando all'utente e ad altri partecipanti notifiche tramite posta elettronica. I messaggi di posta elettronica possono includere collegamenti ad attività importanti, quali l'attivazione o il rinnovo di un ruolo. Questo articolo descrive l'aspetto dei messaggi di posta elettronica, quando vengono inviati e chi li riceve.

## <a name="sender-email-address-and-subject-line"></a>Indirizzo e-mail del mittente e riga dell'oggetto

I messaggi di posta elettronica inviati da Privileged Identity Management per Azure AD e i ruoli delle risorse di Azure hanno l'indirizzo di posta elettronica del mittente seguente:

- Indirizzo di posta elettronica: **Azure-\@noreply Microsoft.com**
- Nome visualizzato: Microsoft Azure

Questi messaggi di posta elettronica includono un prefisso **PIM** nella riga dell'oggetto. Ad esempio:

- PIM: Alain Caronte è stato assegnato in modo permanente al ruolo di lettore di backup

## <a name="notifications-for-azure-ad-roles"></a>Notifiche per i ruoli di Azure AD

Privileged Identity Management invia messaggi di posta elettronica quando si verificano gli eventi seguenti per i ruoli Azure AD:

- Quando l'attivazione di un ruolo con privilegi è in attesa di approvazione
- Quando la richiesta di attivazione di un ruolo con privilegi viene completata
- Quando Azure AD Privileged Identity Management è abilitato

I destinatari di questi messaggi di posta elettronica per i ruoli di Azure AD variano a seconda del ruolo dell'utente, dell'evento e dell'impostazione di notifica:

| Utente | Attivazione del ruolo in attesa di approvazione | Richiesta di attivazione del ruolo completata | PIM è abilitato |
| --- | --- | --- | --- |
| Amministratore dei ruoli con privilegi</br>(Attivato/Idoneo) | Sì</br>(solo se non è specificato nessun responsabile approvazione esplicito) | Sì* | Sì |
| Amministratore della sicurezza</br>(Attivato/Idoneo) | No | Sì* | Sì |
| Amministratore globale</br>(Attivato/Idoneo) | No | Sì* | Sì |

\* Se l'impostazione di [**Notifiche**](pim-how-to-change-default-settings.md#notifications) è **Abilita**.

Di seguito viene riportato un esempio di messaggio di posta elettronica inviato quando un utente attiva un ruolo di Azure AD per l'organizzazione fittizia Contoso.

![Nuovo Privileged Identity Management di posta elettronica per i ruoli Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Posta elettronica Privileged Identity Management digest settimanale per i ruoli Azure AD

Un messaggio di posta elettronica di riepilogo settimanale Privileged Identity Management per i ruoli di Azure AD viene inviato agli amministratori del ruolo con privilegi, agli amministratori della sicurezza e agli amministratori globali che hanno abilitato Privileged Identity Management. Questo messaggio di posta elettronica settimanale fornisce uno snapshot delle attività Privileged Identity Management per la settimana, oltre alle assegnazioni di ruolo con privilegi. È disponibile solo per le organizzazioni Azure AD nel cloud pubblico. Di seguito è illustrato un messaggio di posta elettronica di esempio:

![Posta elettronica Privileged Identity Management digest settimanale per i ruoli Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

Il messaggio di posta elettronica include quattro sezioni:

| Riquadro | Descrizione |
| --- | --- |
| **Users activated** (Utenti attivati) | Numero di volte in cui gli utenti hanno attivato il proprio ruolo idoneo all'interno dell'organizzazione. |
| **Users made permanent** (Utenti resi permanenti) | Numero di volte per cui un utente con un'assegnazione idonea viene reso permanente. |
| **Assegnazioni di ruolo in Privileged Identity Management** | Numero di volte in cui agli utenti viene assegnato un ruolo idoneo all'interno Privileged Identity Management. |
| **Role assignments outside of PIM** (Assegnazioni di ruoli all'esterno di PIM) | Numero di volte in cui agli utenti viene assegnato un ruolo permanente all'esterno di Privileged Identity Management (all'interno Azure AD). |

La **Panoramica della sezione ruoli principali** elenca i primi cinque ruoli dell'organizzazione in base al numero totale di amministratori permanenti e idonei per ogni ruolo. Il collegamento **Intervieni** apre la [procedura guidata PIM](pim-security-wizard.md), che consente di convertire gli amministratori permanenti in amministratori idonei in batch.

## <a name="email-timing-for-activation-approvals"></a>Tempistica e-mail per le approvazioni di attivazione

Quando gli utenti attivano il proprio ruolo e l'impostazione del ruolo richiede l'approvazione, gli approvatori riceveranno tre messaggi di posta elettronica per ogni approvazione:

- Richiedere di approvare o negare la richiesta di attivazione dell'utente (inviata dal motore di approvazione della richiesta)
- La richiesta dell'utente è approvata (inviata dal motore di approvazione della richiesta)
- Il ruolo dell'utente è attivato (inviato dal Privileged Identity Management)

I primi due messaggi di posta elettronica inviati dal motore di approvazione della richiesta possono essere posticipati. Attualmente, il 90% dei messaggi di posta elettronica dura da 3 a 10 minuti, ma per i clienti dell'1% può essere molto più lungo, fino a 15 minuti.

Se una richiesta di approvazione viene approvata nel portale di Azure prima dell'invio del primo messaggio di posta elettronica, il primo messaggio di posta elettronica non verrà più attivato e gli altri responsabili della richiesta di approvazione non verranno informati tramite posta elettronica. Potrebbe sembrare che l'utente non abbia ricevuto un messaggio di posta elettronica, ma è il comportamento previsto.

## <a name="pim-emails-for-azure-resource-roles"></a>Messaggi di posta elettronica PIM per i ruoli delle risorse di Azure

Privileged Identity Management invia messaggi di posta elettronica ai proprietari e agli amministratori accesso utenti quando si verificano gli eventi seguenti per i ruoli delle risorse di Azure:

- Quando un'assegnazione di ruolo è in attesa di approvazione
- Quando un ruolo viene assegnato
- Quando un ruolo sta per scadere
- Quando un ruolo è idoneo per l'estensione
- Quando un ruolo viene rinnovato da un utente finale
- Quando una richiesta di attivazione del ruolo viene completata

Privileged Identity Management invia messaggi di posta elettronica agli utenti finali quando si verificano gli eventi seguenti per i ruoli delle risorse di Azure:

- Quando viene assegnato un ruolo all'utente
- Quando un ruolo utente è scaduto
- Quando un ruolo utente è stato esteso
- Quando la richiesta di attivazione del ruolo di un utente viene completata

Di seguito viene riportato un esempio di messaggio di posta elettronica inviato quando a un utente viene assegnato un ruolo di risorsa di Azure per l'organizzazione fittizia Contoso.

![Nuovo Privileged Identity Management di posta elettronica per i ruoli delle risorse di Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo Azure AD in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Approva o rifiuta le richieste per i ruoli di Azure AD in Privileged Identity Management](azure-ad-pim-approval-workflow.md)
