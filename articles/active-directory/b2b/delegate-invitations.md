---
title: Delegare gli inviti per Collaborazione B2B di Azure Active Directory | Documentazione Microsoft
description: Le proprietà di un utente di Collaborazione B2B di Azure Active Directory sono configurabili
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 45bb53e5ebd72631c81e6ee516413fcc10771470
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431910"
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegare gli inviti per Collaborazione B2B di Azure Active Directory

Con Collaborazione B2B (Business-to-Business) di Azure Active Directory (Azure AD) non è necessario essere un amministratore globale per inviare inviti. È possibile invece usare i criteri e delegare gli inviti agli utenti nei ruoli a cui è consentito inviare l'invito. Un nuovo modo importante per delegare gli inviti di utenti guest consiste nell'usare il ruolo Mittente dell'invito guest.

## <a name="guest-inviter-role"></a>Ruolo Mittente dell'invito guest
È possibile assegnare l'utente al ruolo Mittente dell'invito guest per inviare gli inviti. Non è necessario essere membro del ruolo di amministratore globale per inviare gli inviti. Per impostazione predefinita, i normali utenti possono anche richiamare l'API di invito, a meno che l'amministratore globale non abbia disabilitato gli inviti per i normali utenti. Un utente può chiamare l'API anche usando il portale di Azure o PowerShell.

Ecco un esempio che illustra l'aggiunta di un utente al ruolo Mittente dell'invito guest con PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Controllare i mittenti degli inviti

In Azure Active Directory selezionare **Impostazioni utente**. In **Utenti esterni** selezionare **Manage external collaboration settings** (Gestisci impostazioni di collaborazione esterna).

> [!NOTE]
> Le **Impostazioni di collaborazione esterna** sono inoltre disponibili dalla pagina **Relazioni aziendali**. In Azure Active Directory, da **Gestisci** passare a **Relazioni aziendali** > **Impostazioni**.

![Impostazioni di collaborazione esterna](./media/delegate-invitations/control-who-to-invite.png)

Con Collaborazione B2B di Azure AD, un amministratore tenant può impostare i criteri di invito seguenti:

- Disattivare gli inviti
- Solo gli amministratori e gli utenti nel ruolo Mittente dell'invito guest possono inviare inviti
- Gli amministratori, il ruolo Mittente dell'invito guest e i membri possono inviare inviti
- Possono inviare inviti tutti gli utenti, inclusi gli utenti guest

Per impostazione predefinita, i tenant sono impostati su 4. Tutti gli utenti, inclusi gli utenti guest, possono inviare inviti a utenti B2B.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Aggiungere utenti guest di Collaborazione B2B senza un invito](add-user-without-invite.md)
- [Aggiunta di un utente di Collaborazione B2B a un ruolo](add-guest-to-role.md)


