---
title: Abilitare le impostazioni di collaborazione esterna B2B - Azure ADEnable B2B external collaboration settings - Azure AD
description: Informazioni su come abilitare la collaborazione esterna B2B di Active Directory e gestire chi può invitare utenti guest. Utilizzare il ruolo Invitatore ospite per delegare gli inviti.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fb10863334392b207c7cfd2172dc9260cf15e2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272891"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Abilitare la collaborazione esterna B2B e gestire gli utenti che possono invitare gli ospiti

Questo articolo descrive come abilitare la collaborazione B2B di Azure Active Directory (Azure AD) e determinare chi può invitare gli ospiti. Per impostazione predefinita, tutti gli utenti e gli ospiti della directory possono invitare gli ospiti anche se non sono assegnati a un ruolo di amministratore. Le impostazioni di collaborazione esterne consentono di attivare o disattivare gli inviti guest per diversi tipi di utenti nell'organizzazione. È inoltre possibile delegare gli inviti a singoli utenti assegnando ruoli che consentono loro di invitare gli ospiti.

## <a name="configure-b2b-external-collaboration-settings"></a>Configurare le impostazioni di collaborazione esterna B2BConfigure B2B external collaboration settings

Con Collaborazione B2B di Azure AD, un amministratore tenant può impostare i criteri di invito seguenti:

- Disattivare gli inviti
- Solo gli amministratori e gli utenti nel ruolo Mittente dell'invito guest possono inviare inviti
- Gli amministratori, il ruolo Mittente dell'invito guest e i membri possono inviare inviti
- Possono inviare inviti tutti gli utenti, inclusi gli utenti guest

Per impostazione predefinita, tutti gli utenti, inclusi gli ospiti, possono invitare utenti guest.

### <a name="to-configure-external-collaboration-settings"></a>Per configurare le impostazioni di collaborazione esterna:

1. Accedere al [portale](https://portal.azure.com) di Azure come amministratore tenant.
2. Selezionare**Impostazioni** > **utente**utenti **di Azure Active Directory** > .
3. In **Utenti esterni**selezionare Gestisci impostazioni di collaborazione **esterna.**
   > [!NOTE]
   > Le **Impostazioni di collaborazione esterna** sono inoltre disponibili dalla pagina **Relazioni aziendali**. In Azure Active Directory, da **Gestisci** passare a **Relazioni aziendali** > **Impostazioni**.
4. Nella pagina **Impostazioni di collaborazione esterna** scegliere i criteri da abilitare.

   ![Impostazioni di collaborazione esterna](./media/delegate-invitations/control-who-to-invite.png)

  - **Le autorizzazioni per gli utenti guest sono limitate:** questo criterio determina le autorizzazioni per i guest nella directory. Selezionare **Sì** per bloccare gli ospiti di determinate attività di directory, ad esempio l'enumerazione di utenti, gruppi o altre risorse di directory. Selezionare **No** per concedere agli ospiti lo stesso accesso ai dati della directory degli utenti normali nella directory.
   - **Gli amministratori e gli utenti con il ruolo di invitato ospite possono invitare:** per consentire agli amministratori e agli utenti con il ruolo "Invitatore ospite" di invitare ospiti, impostare questo criterio su **Sì**.
   - **I membri possono invitare**: Per consentire ai membri non amministratori della directory di invitare ospiti, impostare questo criterio su **Sì**.
   - **Gli ospiti possono invitare**: Per consentire agli ospiti di invitare altri ospiti, impostare questa politica su **Sì**.
   - **Abilita passcode monouso e-mail per gli ospiti (anteprima)**: Per ulteriori informazioni sulla funzionalità di passcode monouso, vedere [Autenticazione monouso (anteprima) via e-mail.](one-time-passcode.md)
   - Restrizioni di **collaborazione:** per ulteriori informazioni sull'autorizzazione o il blocco degli inviti a domini specifici, vedere [Consentire o bloccare gli inviti a utenti B2B di organizzazioni specifiche.](allow-deny-list.md)

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Assegnare il ruolo Invitatore guest a un utenteAssign the Guest Inviter role to a user

Con il ruolo Invitatore ospite, è possibile concedere ai singoli utenti la possibilità di invitare gli ospiti senza assegnare loro un ruolo di amministratore globale o di altro amministratore. Assegnare il ruolo di invitato guest ai singoli utenti. Assicurarsi quindi di impostare **Amministratori e utenti nel ruolo di invitato guest possono invitare** su **Sì**.

Ecco un esempio che illustra l'aggiunta di un utente al ruolo Mittente dell'invito guest con PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è la collaborazione B2B di Azure AD?](what-is-b2b.md)
- [Aggiungere utenti guest di Collaborazione B2B senza un invito](add-user-without-invite.md)
- [Aggiunta di un utente di Collaborazione B2B a un ruolo](add-guest-to-role.md)


