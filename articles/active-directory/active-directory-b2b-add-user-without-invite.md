---
title: Aggiungere utenti di Collaborazione B2B ad Azure Active Directory senza invito | Microsoft Docs
description: È possibile consentire a un utente guest di aggiungere altri utenti guest ad Azure Active Directory senza riscattare un invito in Collaborazione B2B di Azure Active Directory.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5a37a5a14dcb07db7e078558072f7edad7432d9b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075624"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Aggiungere utenti guest di Collaborazione B2B senza un invito

> [!NOTE]
> A questo punto, gli utenti guest non hanno più bisogno del messaggio di posta elettronica di invito, se non in alcuni casi speciali. Per altre informazioni, vedere [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md).  

È possibile consentire a un utente, ad esempio un rappresentante dei partner, di aggiungere utenti dal partner all'organizzazione senza la necessità di riscattare gli inviti. È sufficiente concedere all'utente i privilegi di enumerazione nella directory in uso per l'organizzazione del partner. 

Concedere i privilegi nei casi seguenti:

1. Un utente dell'organizzazione host (ad esempio WoodGrove) invita un utente dell'organizzazione partner (ad esempio Sam@litware.com) come guest.
2. L'amministratore dell'organizzazione host [configura criteri](active-directory-b2b-delegate-invitations.md) che consentono a Sam di identificare e aggiungere altri utenti dall'organizzazione partner (Litware).
3. Ora Sam può aggiungere altri utenti da Litware alla directory, ai gruppi o alle applicazioni di WoodGrove senza la necessità di riscattare gli inviti. Se Sam dispone dei privilegi di enumerazione appropriati in Litware, l'operazione viene eseguita automaticamente.

### <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Procedura per aggiungere utenti di Collaborazione B2B da parte di Information Worker](active-directory-b2b-iw-add-users.md)
- [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
- [Delegare gli inviti per Collaborazione B2B di Azure Active Directory](active-directory-b2b-delegate-invitations.md)

