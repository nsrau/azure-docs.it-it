---
title: Delegare gli inviti per Collaborazione B2B di Azure Active Directory | Documentazione Microsoft
description: "Le proprietà di un utente di Collaborazione B2B di Azure Active Directory sono configurabili"
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 6c6d757a770613498bedca0f8f3a965241d692eb


---

# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegare gli inviti per Collaborazione B2B di Azure Active Directory

Con l'aggiornamento dell'anteprima pubblica di Collaborazione B2B di Azure Active Directory (Azure AD), non è necessario essere un amministratore globale per invitare gli utenti. È possibile controllare chi può inviare gli inviti tramite criteri e delegare gli inviti agli utenti nei ruoli a cui è consentito l'invito. Un nuovo modo importante per delegare gli inviti di utenti guest consiste nell'usare il ruolo Mittente dell'invito guest.

## <a name="guest-inviter-role"></a>Ruolo Mittente dell'invito guest
È possibile assegnare l'utente al ruolo Mittente dell'invito guest per inviare gli inviti. Non è necessario essere membro degli amministratori globali per inviare gli inviti. Per impostazione predefinita, i normali utenti possono anche richiamare l'API di invito, a meno che l'amministratore globale non abbia disabilitato gli inviti per gli utenti normali. È possibile eseguire questa operazione nel portale di Azure e anche in PowerShell.

Ecco un esempio che illustra l'aggiunta di un utente al ruolo Mittente dell'invito guest con PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress >
```

## <a name="controlling-who-can-invite"></a>Controllo dei mittenti degli inviti

![Controllare come inviare un invito](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

Con Collaborazione B2B di Azure AD, viene consentito all'amministratore tenant di impostare i criteri di invito seguenti:

1. Disattivare gli inviti
2. Possono inviare inviti solo gli amministratori e gli utenti nel ruolo Mittente dell'invito guest
3. Possono inviare inviti gli amministratori, il ruolo Mittente dell'invito guest e i membri
4. Possono inviare inviti tutti gli utenti, inclusi gli utenti guest

Per impostazione predefinita, i tenant sono impostati sulla quarta opzione (tutti gli utenti, inclusi gli utenti guest, possono invitare gli utenti di B2B).

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Proprietà dell'utente di Collaborazione B2B](active-directory-b2b-user-properties.md)
* [Aggiunta di un utente di Collaborazione B2B a un ruolo](active-directory-b2b-add-guest-to-role.md)
* [Gruppi dinamici e Collaborazione B2B](active-directory-b2b-dynamic-groups.md)
* [Codici ed esempi di PowerShell per Collaborazione B2B](active-directory-b2b-code-samples.md)
* [Configurare app SaaS per Collaborazione B2B](active-directory-b2b-configure-saas-apps.md)
* [Token utente per Collaborazione B2B](active-directory-b2b-user-token.md)
* [Mapping delle attestazioni utente per Collaborazione B2B](active-directory-b2b-claims-mapping.md)
* [Condivisione esterna di Office 365](active-directory-b2b-o365-external-user.md)
* [Limitazioni correnti di Collaborazione B2B](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


