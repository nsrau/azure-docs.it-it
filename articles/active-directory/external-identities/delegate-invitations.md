---
title: Abilitare le impostazioni di collaborazione esterna B2B - Azure AD
description: Informazioni su come abilitare la collaborazione esterna B2B di Active Directory e gestire chi può invitare utenti guest. Usare il ruolo Mittente dell'invito guest per delegare gli inviti.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6a2c1a9b908503ee5afc2687ebef473ffed626a
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909542"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Abilitare la collaborazione esterna B2B e gestire chi può invitare utenti guest

Questo articolo descrive come abilitare la collaborazione B2B di Azure Active Directory (Azure AD) e determinare chi può invitare utenti guest. Per impostazione predefinita, tutti gli utenti e gli utenti guest nella directory possono invitare utenti guest anche se non sono assegnati a un ruolo di amministratore. Le impostazioni di collaborazione esterna consentono di attivare o disattivare gli inviti guest per diversi tipi di utenti dell'organizzazione. È anche possibile delegare gli inviti a singoli utenti assegnando ruoli che consentono loro di invitare utenti guest.

## <a name="configure-b2b-external-collaboration-settings"></a>Configurare le impostazioni di collaborazione esterna B2B

Con Collaborazione B2B di Azure AD, un amministratore tenant può impostare i criteri di invito seguenti:

- Disattivare gli inviti
- Solo gli amministratori e gli utenti nel ruolo Mittente dell'invito guest possono inviare inviti
- Gli amministratori, il ruolo Mittente dell'invito guest e i membri possono inviare inviti
- Possono inviare inviti tutti gli utenti, inclusi gli utenti guest

Per impostazione predefinita, tutti gli utenti, inclusi gli utenti guest, possono inviare inviti a utenti guest.

### <a name="to-configure-external-collaboration-settings"></a>Per configurare le impostazioni di collaborazione esterna:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore tenant.
2. Selezionare **Azure Active Directory**.
3. Selezionare **Identità esterne** > **Impostazioni di collaborazione esterna**.
6. Nella pagina **Impostazioni di collaborazione esterna** selezionare i criteri che si intende abilitare.

   ![Impostazioni di collaborazione esterna](./media/delegate-invitations/control-who-to-invite.png)

  - **Le autorizzazioni degli utenti guest sono limitate**: questo criterio determina le autorizzazioni per gli utenti guest nella directory. Selezionare **Sì** per non consentire agli utenti guest di eseguire determinate attività sulla directory, ad esempio l'enumerazione di utenti, gruppi o altre risorse della directory. Selezionare **No** per concedere agli utenti guest lo stesso accesso degli utenti normali ai dati della directory.
   - **Amministratori e utenti nel ruolo mittente dell'invito guest possono invitare**: per consentire solo agli amministratori e agli utenti del ruolo "Mittente dell'invito guest" di invitare utenti guest, impostare questo criterio su **Sì**.
   - **I membri possono invitare**: per consentire ai membri non amministratori della directory di invitare utenti guest, impostare questo criterio su **Sì**.
   - **Gli utenti guest possono invitare**: per consentire agli utenti guest di invitare altri utenti guest, impostare questo criterio su **Sì**.
   - **Abilita passcode monouso tramite posta elettronica per gli utenti guest (anteprima)** : Per altre informazioni sulla funzionalità passcode monouso, vedere [Autenticazione con passcode monouso tramite posta elettronica (anteprima)](one-time-passcode.md).
   - **Restrizioni di collaborazione**: per altre informazioni su come consentire o bloccare gli inviti a specifici domini, vedere [Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](allow-deny-list.md).
   
   > [!NOTE]
   > Se **i membri possono invitare** è impostato su **No** e **gli amministratori e gli utenti nel ruolo mittente dell'invito Guest possono invitare** è impostato su **Sì**, gli utenti del ruolo di **invito Guest** saranno comunque in grado di invitare i guest.

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Assegnare il ruolo Mittente dell'invito guest a un utente

Con il ruolo Mittente dell'invito guest è possibile concedere a singoli utenti la possibilità di invitare utenti guest senza assegnare loro il ruolo di amministratore globale o un altro ruolo di amministratore. Assegnare il ruolo Mittente dell'invito guest a singoli utenti. Assicurarsi che l'opzione **Amministratori e utenti nel ruolo mittente dell'invito guest possono invitare** sia impostata su **Sì**.

Ecco un esempio che illustra l'aggiunta di un utente al ruolo Mittente dell'invito guest con PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Aggiungere utenti guest di Collaborazione B2B senza un invito](add-user-without-invite.md)
- [Aggiunta di un utente di Collaborazione B2B a un ruolo](add-guest-to-role.md)

