---

title: Aggiungere utenti di Collaborazione B2B ad Azure Active Directory senza invito | Documentazione Microsoft
description: Collaborazione B2B di Azure Active Directory consente agli information worker di aggiungere utenti dall&quot;organizzazione ad Azure AD per l&quot;accesso alle applicazioni aziendali
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/10/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0eef684115f9c21ea61502a10576f74cac0ace8e
ms.openlocfilehash: 8fa8ba169ca85c33e52eee4a7cd5b84aa4012673


---

# <a name="add-b2b-collaboration-users-without-an-invitation"></a>Aggiungere utenti di Collaborazione B2B senza invito

Se il mittente dell'invito appartiene a un ruolo con privilegi di enumerazione nella directory dell'organizzazione partner, da cui sta aggiungendo gli utenti, gli utenti invitati vengono aggiunti nell'organizzazione che invia l'invito senza bisogno di inviti.

Ecco lo scenario in cui questo è più utile:

1. Un utente dell'organizzazione host (ad esempio WoodGrove) invita un utente dell'organizzazione partner (ad esempio Sam@litware.com)) come guest.
2. L'amministratore dell'organizzazione host configura criteri che consentono a Sam di identificare e aggiungere altri utenti dall'organizzazione partner (Litware).
3. Ora Sam può aggiungere altri utenti da Litware alla directory, ai gruppi o alle applicazioni di WoodGrove senza bisogno che gli inviti vengano riscattati. Se Sam ha i privilegi di enumerazione appropriati in Litware, questa operazione viene eseguita automaticamente.

### <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
* [Procedura per aggiungere utenti di Collaborazione B2B da parte di information worker](active-directory-b2b-iw-add-users.md)
* [Elementi del messaggio di posta elettronica di invito per la Collaborazione B2B](active-directory-b2b-invitation-email.md)
* [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
* [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md)
* [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalizzazione per Collaborazione B2B di Azure Active Directory](active-directory-b2b-api.md)
* [Autenticazione a più fattori per utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


