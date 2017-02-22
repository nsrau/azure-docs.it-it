---

title: Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte di information worker | Microsoft Docs
description: Collaborazione B2B di Azure Active Directory consente agli information worker di aggiungere utenti dall&quot;organizzazione ad Azure AD per l&quot;accesso alle applicazioni aziendali.
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
ms.date: 02/02/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d9ffd4176e87b6b5ada882ff09f507665bda7b1d
ms.openlocfilehash: 7bad8269c3756979fe48d130a0cdfb02ceafad86


---

# <a name="how-do-information-workers-add-b2b-collaboration-users-to-azure-active-directory"></a>Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte di information worker

Gli information worker possono usare il [pannello di accesso alle applicazioni](http://myapps.microsoft.com) per aggiungere utenti di Collaborazione B2B ai gruppi e alle applicazioni che gestiscono.

## <a name="information-workers-adding-b2b-collaboration-users-to-an-application"></a>Aggiunta di utenti di Collaborazione B2B a un'applicazione da parte di information worker
Assegnare gli utenti di Collaborazione B2B a un'app come information worker in un'organizzazione partner, come illustrato nel video seguente:

  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/information-worker-assign-to-apps]

  Se il video non è incorporato, è possibile guardarlo [qui](https://channel9.msdn.com/Blogs/Azure/information-worker-assign-to-apps).

## <a name="information-workers-adding-b2b-collaboration-users-to-a-group"></a>Aggiunta di utenti di Collaborazione B2B a un gruppo da parte di information worker

Gli information worker possono aggiungere in modo simile gli utenti di Collaborazione B2B a un gruppo assegnato abilitato per la gestione gruppi self-service.
> [!NOTE]
Non è possibile aggiungere utenti di Collaborazione B2B a un gruppo dinamico o a un gruppo sincronizzato con Active Directory locale.

## <a name="add-without-invitation"></a>Eseguire un'aggiunta senza invitato

Se il mittente dell'invito appartiene a un ruolo con privilegi di enumerazione nella directory dell'organizzazione partner, da cui sta aggiungendo gli utenti, gli utenti invitati vengono aggiunti nell'organizzazione che invia l'invito senza bisogno di inviti.

Questo è lo scenario in cui è più utile:
1. Un utente dell'organizzazione host (ad esempio, WoodGrove) invita un utente dell'organizzazione partner (ad esempio, Sam@litware.com) come guest.
2. L'amministratore dell'organizzazione host configura criteri che consentono a Sam di identificare e aggiungere altri utenti dall'organizzazione partner (Litware).
4. Ora Sam può aggiungere altri utenti da Litware alla directory, ai gruppi o alle applicazioni di WoodGrove senza bisogno che gli inviti vengano riscattati. Se Sam ha i privilegi di enumerazione appropriati in Litware, questa operazione viene eseguita automaticamente.


* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
* [Elementi del messaggio di posta elettronica di invito per la Collaborazione B2B](active-directory-b2b-invitation-email.md)
* [Riscatto dell'invito di Collaborazione B2B di Azure Active Directory](active-directory-b2b-redemption-experience.md)
* [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md)
* [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalizzazione per Collaborazione B2B di Azure Active Directory](active-directory-b2b-api.md)
* [Autenticazione a più fattori per utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO1-->


