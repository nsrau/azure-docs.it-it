---
title: Aggiungere utenti guest B2B senza un collegamento di invito o un messaggio di posta elettronica - Azure Active Directory | Microsoft Docs
description: È possibile consentire a un utente guest di aggiungere altri utenti guest ad Azure Active Directory senza riscattare un invito in Collaborazione B2B di Azure Active Directory.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dca03f84b821d20ee6fecbaec24a1aa840836b5
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294978"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Aggiungere utenti guest di collaborazione B2B senza un collegamento di invito o un messaggio di posta elettronica

È adesso possibile invitare un utente guest inviando un collegamento diretto a un'app condivisa. Tramite questo metodo, gli utenti guest non necessitano più del messaggio di posta elettronica di invito, se non in alcuni casi speciali. Un utente guest fa clic sul collegamento app, esamina e accetta le condizioni sulla privacy e quindi accede con facilità all'app. Per altre informazioni, vedere [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md).   

Prima che questo nuovo metodo fosse disponibile, era possibile invitare gli utenti guest senza messaggio di posta elettronica di invito tramite l'aggiunta di un mittente dell'invito (dall'organizzazione o da un'organizzazione partner) al ruolo della directory **Mittente dell'invito guest**, quindi facendo aggiungere al mittente dell'invito gli utenti guest alla directory, ai gruppi o alle applicazioni tramite l'interfaccia utente o PowerShell (in caso di uso di PowerShell, è possibile eliminare completamente il messaggio di posta elettronica di invito). Ad esempio: 

1. Un utente dell'organizzazione host (ad esempio WoodGrove) invita un utente dell'organizzazione partner (ad esempio Sam@litware.com) come guest.
2. L'amministratore dell'organizzazione host [configura criteri](delegate-invitations.md) che consentono a Sam di identificare e aggiungere altri utenti dall'organizzazione partner (Litware) (Sam deve essere aggiunto per il ruolo di **mittente dell'invito guest**).
3. Ora Sam può aggiungere altri utenti da Litware alla directory, ai gruppi o alle applicazioni di WoodGrove senza la necessità di riscattare gli inviti. Se Sam dispone dei privilegi di enumerazione appropriati in Litware, l'operazione viene eseguita automaticamente.
 
Questo metodo originale continua a essere efficace. Tuttavia, vi è una piccola differenza nel comportamento. Se si usa PowerShell, si noterà che un account guest invitati include ora uno stato **PendingAcceptance** anziché mostrare immediatamente quello di **accettato**. Anche se lo stato è in sospeso, l'utente guest può effettuare l'accesso e accedere all'app senza fare clic su un collegamento di invito tramite posta elettronica. Lo stato in sospeso significa che l'utente non ha concluso l'[esperienza di consenso](redemption-experience.md#privacy-policy-agreement), in cui accetta le condizioni sulla privacy dell'organizzazione invitante. L'utente guest visualizza questa schermata di consenso al primo accesso. 

Se si invita un utente nella directory, l'utente guest deve accedere direttamente all'URL del portale di Azure specifico per il tenant della risorsa (ad esempio https://portal.azure.com/*resourcetenant*.onmicrosoft.com) per visualizzare e accettare le condizioni sulla privacy.

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md)
- [Delegare gli inviti per Collaborazione B2B di Azure Active Directory](delegate-invitations.md)
- [Procedura per aggiungere utenti di Collaborazione B2B da parte di Information Worker](add-users-information-worker.md)

