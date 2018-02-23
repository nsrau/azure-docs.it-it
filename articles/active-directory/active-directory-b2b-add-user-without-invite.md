---
title: Aggiungere utenti di Collaborazione B2B ad Azure Active Directory senza invito | Microsoft Docs
description: "È possibile consentire a un utente guest di aggiungere altri utenti guest ad Azure Active Directory senza riscattare un invito in Collaborazione B2B di Azure Active Directory."
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
ms.openlocfilehash: aa0d8dc9bed0a56d998e782283c84e7d2571b1f1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Aggiungere utenti guest di Collaborazione B2B senza un invito

È possibile consentire a un utente, ad esempio un rappresentante dei partner, di aggiungere utenti dal partner all'organizzazione senza la necessità di riscattare gli inviti. È sufficiente concedere all'utente i privilegi di enumerazione nella directory in uso per l'organizzazione del partner. 

Concedere i privilegi nei casi seguenti:

1. Un utente dell'organizzazione host (ad esempio WoodGrove) invita un utente dell'organizzazione partner (ad esempio Sam@litware.com) come guest.
2. L'amministratore dell'organizzazione host configura criteri che consentono a Sam di identificare e aggiungere altri utenti dall'organizzazione partner (Litware).
3. Ora Sam può aggiungere altri utenti da Litware alla directory, ai gruppi o alle applicazioni di WoodGrove senza la necessità di riscattare gli inviti. Se Sam dispone dei privilegi di enumerazione appropriati in Litware, l'operazione viene eseguita automaticamente.

### <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
* [Procedura di aggiunta di utenti di Collaborazione B2B da parte di information worker](active-directory-b2b-iw-add-users.md)
* [Elementi del messaggio di posta elettronica di invito per la Collaborazione B2B](active-directory-b2b-invitation-email.md)
* [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
* [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md)
* [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalizzazione per Collaborazione B2B di Azure Active Directory](active-directory-b2b-api.md)
* [Autenticazione a più fattori per utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)