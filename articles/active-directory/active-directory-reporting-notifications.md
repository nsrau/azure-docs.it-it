---
title: Notifiche di Report di Azure Active Directory
description: Come utilizzare le notifiche di report di Azure Active Directory per gli accessi sospetti.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.openlocfilehash: 7aacb31f708e8c3221a5b8cf4223c65160ccb019
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-reporting-notifications"></a>Notifiche di Report di Azure Active Directory
## <a name="what-reports-generate-email-notifications"></a>Quali report generano notifiche tramite posta elettronica
A questo punto, solo il report dell’attività di accesso irregolare attiva le notifiche di posta elettronica.

## <a name="what-is-an-irregular-sign-in"></a>Che cos'è un "Accesso irregolare"?
Gli accessi irregolari sono quelli individuati dagli algoritmi di Machine Learning in base a una condizione di tipo "tempo di spostamento impossibile" combinata con una posizione e un dispositivo di accesso anomali. È possibile che un pirata informatico abbia tentato di accedere con questo account.

## <a name="who-receives-the-email-notifications"></a>A chi vengono inviate le notifiche tramite posta elettronica?
Il messaggio di posta elettronica viene inviato a tutti gli amministratori globali ai quali è stata assegnata una licenza di Active Directory Premium. Per garantire il recapito, il messaggio viene inviato anche all'indirizzo di posta elettronica alternativo degli amministratori . Per essere certi di ricevere il messaggio, gli amministratori devono aggiungere aad-alerts-noreply@mail.windowsazure.com nei rispettivi elenchi di mittenti attendibili.

## <a name="how-often-are-these-emails-sent"></a>Con quale frequenza vengono inviati i messaggi di posta elettronica?
Il messaggio di posta elettronica viene inviato se si verificano 10 nuove attività di accesso irregolare negli ultimi 30 giorni o dopo l'ultimo messaggio di posta elettronica inviato, a seconda del periodo più breve.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>Come si accede al report indicato nel messaggio di posta elettronica?
Quando si fa clic sul collegamento, si verrà reindirizzati alla pagina del report nel portale di Azure classico. Per accedere al report, è necessario essere:

* Un amministratore o un coamministratore della sottoscrizione di Azure.
* Un amministratore globale nella directory e assegnata una licenza di Active Directory Premium. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

## <a name="can-i-turn-off-these-emails"></a>È possibile disattivare questi messaggi di posta elettronica?
Sì, per disattivare le notifiche correlate ad accessi anomali all'interno del portale di Azure classico, fare clic su **Configura** e quindi selezionare **Disabilitato** nella sezione **Notifiche**.

## <a name="whats-next"></a>Passaggi successivi
* Per informazioni sui report di sicurezza, controllo e attività disponibili, Vedere [Report di sicurezza, controllo e attività di Azure AD](active-directory-view-access-usage-reports.md)
* [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](customize-branding.md)

