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
ms.openlocfilehash: 589d9a417dae5c40d24d25c4ef864ce903fbfbe3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259578"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Aggiungere utenti guest di Collaborazione B2B senza un invito

> [!NOTE]
> A questo punto, gli utenti guest non hanno più bisogno del messaggio di posta elettronica di invito, se non in alcuni casi speciali. Per altre informazioni, vedere [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md).  

È possibile consentire a un utente, ad esempio un rappresentante dei partner, di aggiungere utenti dal partner all'organizzazione senza la necessità di riscattare gli inviti. È sufficiente concedere all'utente i privilegi di enumerazione nella directory in uso per l'organizzazione del partner. 

Concedere i privilegi nei casi seguenti:

1. Un utente dell'organizzazione host (ad esempio WoodGrove) invita un utente dell'organizzazione partner (ad esempio Sam@litware.com) come guest.
2. L'amministratore dell'organizzazione host [configura criteri](delegate-invitations.md) che consentono a Sam di identificare e aggiungere altri utenti dall'organizzazione partner (Litware).
3. Ora Sam può aggiungere altri utenti da Litware alla directory, ai gruppi o alle applicazioni di WoodGrove senza la necessità di riscattare gli inviti. Se Sam dispone dei privilegi di enumerazione appropriati in Litware, l'operazione viene eseguita automaticamente.

### <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Procedura per aggiungere utenti di Collaborazione B2B da parte di Information Worker](add-users-information-worker.md)
- [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md)
- [Delegare gli inviti per Collaborazione B2B di Azure Active Directory](delegate-invitations.md)

