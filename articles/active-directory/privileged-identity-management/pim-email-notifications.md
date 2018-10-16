---
title: Notifiche tramite posta elettronica in PIM - Azure | Microsoft Docs
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
ms.component: pim
ms.date: 09/07/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: de1d29d3ab1b370257c3a2d6b6ff9f677197fc2a
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303065"
---
# <a name="email-notifications-in-pim"></a>Notifiche tramite posta elettronica in PIM

Quando si verificano eventi chiave in Azure AD Privileged Identity Management (PIM), vengono inviate notifiche tramite posta elettronica. Ad esempio, PIM invia e-mail per gli eventi seguenti:

- Quando l'attivazione di un ruolo con privilegi è in attesa di approvazione
- Quando la richiesta di attivazione di un ruolo con privilegi viene completata
- Quando viene attivato un ruolo con privilegi
- Quando viene assegnato un ruolo con privilegi
- Quando viene abilitato Azure AD Privileged Identity Management

Le notifiche di posta elettronica vengono inviate ai seguenti amministratori:

- Amministratore dei ruoli con privilegi
- Amministratore della sicurezza

Notifiche tramite posta elettronica vengono inviate anche all'utente finale che dispone di ruolo con privilegi, per gli eventi seguenti:

- Quando la richiesta di attivazione di un ruolo con privilegi viene completata
- Quando viene assegnato un ruolo con privilegi

Dalla fine di luglio 2018, le notifiche tramite posta elettronica inviate con PIM hanno un nuovo indirizzo di posta elettronica del mittente e un nuovo design visivo. Questo aggiornamento influirà sia su PIM per Azure AD che su PIM per le risorse di Azure. Tutti gli eventi che hanno precedentemente attivato una notifica tramite posta elettronica continueranno a inviare un messaggio di posta elettronica. Alcuni messaggi di posta elettronica avranno contenuto aggiornato con informazioni più mirate.

## <a name="sender-email-address"></a>Indirizzo di posta elettronica del mittente

A partire dalla fine di luglio 2018, le notifiche tramite posta elettronica avranno l'indirizzo seguente:

- Indirizzo di posta elettronica: **azure-noreply@microsoft.com**
- Nome visualizzato: Microsoft Azure

In precedenza, le notifiche tramite posta elettronica avevano l'indirizzo seguente:

- Indirizzo di posta elettronica: **azureadnotifications@microsoft.com**
- Nome visualizzato: servizio di notifica di Microsoft Azure AD

## <a name="email-subject-line"></a>Riga dell'oggetto del messaggio di posta elettronica

A partire dalla fine di luglio 2018, le notifiche tramite posta elettronica per Azure AD e i ruoli delle risorse di Azure avranno un prefisso **PIM** nella riga dell'oggetto. Ad esempio:

- PIM: Ad Alain Charon è stato assegnato in modo permanente il ruolo Lettore di backup.

## <a name="pim-emails-for-azure-ad-roles"></a>Messaggi di posta elettronica PIM per i ruoli di Azure AD

A partire dalla fine di luglio 2018, le notifiche tramite posta elettronica PIM per i ruoli di Azure AD avranno un design aggiornato. Di seguito viene riportato un esempio di messaggio di posta elettronica inviato quando un utente attiva un ruolo privilegiato per l'organizzazione fittizia Contoso.

![Nuovi messaggi di posta elettronica PIM per i ruoli di Azure AD](./media/pim-email-notifications/email-directory-new.png)

In precedenza, quando un utente attivava un ruolo privilegiato, il messaggio di posta elettronica era simile al seguente.

![Vecchi messaggi di posta elettronica PIM per i ruoli di Azure AD](./media/pim-email-notifications/email-directory-old.png)

## <a name="pim-emails-for-azure-resource-roles"></a>Messaggi di posta elettronica PIM per i ruoli delle risorse di Azure

A partire dalla fine di luglio 2018, le notifiche di posta elettronica PIM per i ruoli delle risorse di Azure avranno un design aggiornato. Di seguito viene riportato un esempio di messaggio di posta elettronica inviato quando a un utente viene assegnato un ruolo privilegiato per l'organizzazione fittizia Contoso.

![Nuovo messaggio di posta elettronica PIM per i ruoli delle risorse di Azure](./media/pim-email-notifications/email-resources-new.png)

In precedenza, quando a un utente veniva assegnato un ruolo privilegiato il messaggio di posta elettronica era simile al seguente.

![Vecchio messaggio di posta elettronica PIM per i ruoli delle risorse di Azure](./media/pim-email-notifications/email-resources-old.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni dei ruoli della directory di Azure AD in PIM](pim-how-to-change-default-settings.md)
- [Approvare o rifiutare le richieste per i ruoli della directory di Azure AD in PIM](azure-ad-pim-approval-workflow.md)
