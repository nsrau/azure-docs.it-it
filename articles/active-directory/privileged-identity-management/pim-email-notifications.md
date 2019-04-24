---
title: Notifiche in PIM - Azure Active Directory tramite posta elettronica | Microsoft Docs
description: Descrive le notifiche tramite posta elettronica in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa9da83a7a6e97f5b721dad550831fe2645ffd3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60289192"
---
# <a name="email-notifications-in-pim"></a>Notifiche tramite posta elettronica in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) consente di sapere quando si verificano eventi importanti, ad esempio quando un ruolo viene assegnato o attivato. PIM informa l'utente e gli altri partecipanti mediante l'invio di notifiche di posta elettronica. I messaggi di posta elettronica possono includere collegamenti ad attività importanti, quali l'attivazione o il rinnovo di un ruolo. Questo articolo descrive l'aspetto dei messaggi di posta elettronica, quando vengono inviati e chi li riceve.

## <a name="sender-email-address-and-subject-line"></a>Indirizzo e-mail del mittente e riga dell'oggetto

I messaggi di posta elettronica inviati da PIM per i ruoli di Azure e Azure AD hanno il seguente indirizzo e-mail del mittente:

- Indirizzo di posta elettronica: **azure-noreply\@microsoft.com**
- Nome visualizzato: Microsoft Azure

Questi messaggi di posta elettronica includono un prefisso **PIM** nella riga dell'oggetto. Ad esempio:

- PIM: Ad Alain Charon è stato assegnato in modo permanente il ruolo Lettore di backup

## <a name="pim-emails-for-azure-ad-roles"></a>Messaggi di posta elettronica PIM per i ruoli di Azure AD

PIM invia messaggi di posta elettronica quando si verificano gli eventi seguenti per i ruoli di Azure AD:

- Quando l'attivazione di un ruolo con privilegi è in attesa di approvazione
- Quando la richiesta di attivazione di un ruolo con privilegi viene completata
- Quando viene abilitato Azure AD Privileged Identity Management

I destinatari di questi messaggi di posta elettronica per i ruoli di Azure AD variano a seconda del ruolo dell'utente, dell'evento e dell'impostazione di notifica:

| Utente | Attivazione del ruolo in attesa di approvazione | Richiesta di attivazione del ruolo completata | PIM è abilitato |
| --- | --- | --- | --- |
| Amministratore dei ruoli con privilegi</br>(Attivato/Idoneo) | Sì</br>(solo se non è specificato nessun responsabile approvazione esplicito) | Sì* | Sì |
| Amministratore della sicurezza</br>(Attivato/Idoneo) | No  | Sì* | Sì |
| Amministratore globale</br>(Attivato/Idoneo) | No  | Sì* | Sì |

\* Se l'impostazione di [**Notifiche**](pim-how-to-change-default-settings.md#notifications) è **Abilita**.

Di seguito viene riportato un esempio di messaggio di posta elettronica inviato quando un utente attiva un ruolo di Azure AD per l'organizzazione fittizia Contoso.

![Nuovi messaggi di posta elettronica PIM per i ruoli di Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Messaggio di posta elettronica digest PIM settimanale per i ruoli di Azure AD

Un messaggio di posta elettronica riepilogativo PIM settimanale per i ruoli di Azure AD viene inviato ad amministratori dei ruoli con privilegi, amministratori della sicurezza e amministratori globali che hanno attivato PIM. Questo messaggio di posta elettronica settimanale offre uno snapshot delle attività PIM per la settimana, oltre alle assegnazioni di ruolo con privilegi. È disponibile solo per i tenant nel cloud pubblico. Di seguito è illustrato un messaggio di posta elettronica di esempio:

![Messaggio di posta elettronica digest PIM settimanale per i ruoli di Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

Il messaggio di posta elettronica include quattro sezioni:

| Riquadro | DESCRIZIONE |
| --- | --- |
| **Users activated** (Utenti attivati) | Numero di volte per cui gli utenti hanno attivato il proprio ruolo idoneo nel tenant. |
| **Users made permanent** (Utenti resi permanenti) | Numero di volte per cui un utente con un'assegnazione idonea viene reso permanente. |
| **Role assignments in PIM** (Assegnazioni di ruoli in PIM) | Numero di volte per cui viene assegnato un ruolo idoneo a un utente all'interno di PIM. |
| **Role assignments outside of PIM** (Assegnazioni di ruoli all'esterno di PIM) | Numero di volte per cui viene assegnato agli utenti un ruolo permanente all'esterno di PIM (ma all'interno di Azure AD). |

La sezione **Overview of your top roles** (Panoramica dei ruoli principali) elenca i cinque ruoli principali nel tenant in base al numero totale di amministratori permanenti e idonei per ogni ruolo. Il collegamento **Intervieni** apre la [procedura guidata PIM](pim-security-wizard.md), che consente di convertire gli amministratori permanenti in amministratori idonei in batch.

## <a name="pim-emails-for-azure-resource-roles"></a>Messaggi di posta elettronica PIM per i ruoli delle risorse di Azure

PIM invia messaggi di posta elettronica a proprietari e amministratori degli accessi utente quando si verificano gli eventi seguenti per i ruoli delle risorse di Azure:

- Quando un'assegnazione di ruolo è in attesa di approvazione
- Quando un ruolo viene assegnato
- Quando un ruolo sta per scadere
- Quando un ruolo è idoneo per l'estensione
- Quando un ruolo viene rinnovato da un utente finale
- Quando una richiesta di attivazione del ruolo viene completata

PIM invia messaggi di posta elettronica agli utenti finali quando si verificano gli eventi seguenti per i ruoli delle risorse di Azure:

- Quando viene assegnato un ruolo all'utente
- Quando un ruolo utente è scaduto
- Quando un ruolo utente è stato esteso
- Quando la richiesta di attivazione del ruolo di un utente viene completata

Di seguito viene riportato un esempio di messaggio di posta elettronica inviato quando a un utente viene assegnato un ruolo di risorsa di Azure per l'organizzazione fittizia Contoso.

![Nuovo messaggio di posta elettronica PIM per i ruoli delle risorse di Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni di ruolo di Azure AD in PIM](pim-how-to-change-default-settings.md)
- [Approvare o rifiutare le richieste per i ruoli di Azure AD in PIM](azure-ad-pim-approval-workflow.md)
