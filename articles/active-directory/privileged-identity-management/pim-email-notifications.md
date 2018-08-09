---
title: Notifiche tramite posta elettronica in Azure AD PIM | Microsoft Docs
description: Descrive le notifiche tramite posta elettronica in Azure AD Privileged Identity Management (PIM)
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
ms.date: 07/24/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: 7943b4fb8c2027b50ce04c30d21f1b0a58f98ace
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621583"
---
# <a name="email-notifications-in-azure-ad-pim"></a>Notifiche tramite posta elettronica in Azure AD PIM

Quando si verificano eventi chiave in Azure AD Privileged Identity Management (PIM), le notifiche tramite posta elettronica vengono inviate all'amministratore o all'utente pertinente. Ad esempio, PIM invia e-mail per gli eventi seguenti:

- Quando l'attivazione di un ruolo con privilegi è in attesa di approvazione
- Quando la richiesta di attivazione di un ruolo con privilegi viene approvata
- Quando viene attivato un ruolo con privilegi
- Quando viene assegnato un ruolo con privilegi
- Quando viene abilitato Azure AD Privileged Identity Management

A partire dalla fine di luglio 2018, le notifiche tramite posta elettronica inviate con PIM avranno un nuovo indirizzo di posta elettronica del mittente e un nuovo design visivo. Questo aggiornamento influirà sia su PIM per Azure AD che su PIM per le risorse di Azure. Tutti gli eventi che hanno precedentemente attivato una notifica tramite posta elettronica continueranno a inviare un messaggio di posta elettronica. Alcuni messaggi di posta elettronica avranno contenuto aggiornato con informazioni più mirate.

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

- [Come gestire le impostazioni di attivazione del ruolo in Azure AD PIM](pim-how-to-change-default-settings.md)
- [Approvazioni in Azure AD PIM](azure-ad-pim-approval-workflow.md)
