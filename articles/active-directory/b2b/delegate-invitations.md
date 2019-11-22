---
title: Abilitare le impostazioni di collaborazione esterna B2B-Azure AD
description: Informazioni su come abilitare Active Directory collaborazione esterna B2B e gestire chi può invitare utenti guest. Utilizzare il ruolo mittente dell'invito Guest per delegare gli inviti.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272891"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Abilitare la collaborazione esterna B2B e gestire gli utenti che possono invitare i Guest

Questo articolo descrive come abilitare la collaborazione B2B Azure Active Directory (Azure AD) e determinare chi può invitare Guest. Per impostazione predefinita, tutti gli utenti e i guest nella directory possono invitare i guest anche se non sono assegnati a un ruolo di amministratore. Le impostazioni di collaborazione esterna consentono di attivare o disattivare gli inviti Guest per diversi tipi di utenti dell'organizzazione. È anche possibile delegare gli inviti a singoli utenti assegnando ruoli che consentono loro di invitare i guest.

## <a name="configure-b2b-external-collaboration-settings"></a>Configurare le impostazioni di collaborazione esterna B2B

Con Collaborazione B2B di Azure AD, un amministratore tenant può impostare i criteri di invito seguenti:

- Disattivare gli inviti
- Solo gli amministratori e gli utenti nel ruolo Mittente dell'invito guest possono inviare inviti
- Gli amministratori, il ruolo Mittente dell'invito guest e i membri possono inviare inviti
- Possono inviare inviti tutti gli utenti, inclusi gli utenti guest

Per impostazione predefinita, tutti gli utenti, inclusi i guest, possono invitare gli utenti guest.

### <a name="to-configure-external-collaboration-settings"></a>Per configurare le impostazioni di collaborazione esterna:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore tenant.
2. Selezionare **Azure Active Directory** > **Utenti** > **Impostazioni utente**.
3. In **Utenti esterni** selezionare **Manage external collaboration settings** (Gestisci impostazioni di collaborazione esterna).
   > [!NOTE]
   > Le **Impostazioni di collaborazione esterna** sono inoltre disponibili dalla pagina **Relazioni aziendali**. In Azure Active Directory, da **Gestisci** passare a **Relazioni aziendali** > **Impostazioni**.
4. Nella pagina **impostazioni di collaborazione esterna** selezionare i criteri che si desidera abilitare.

   ![Impostazioni di collaborazione esterna](./media/delegate-invitations/control-who-to-invite.png)

  - Le **autorizzazioni degli utenti Guest sono limitate**: questi criteri determinano le autorizzazioni per i guest nella directory. Selezionare **Sì** per bloccare i guest da determinate attività di directory, ad esempio l'enumerazione di utenti, gruppi o altre risorse di directory. Selezionare **No** per concedere ai Guest lo stesso accesso ai dati della directory come utenti normali nella directory.
   - Gli **amministratori e gli utenti nel ruolo mittente dell'invito Guest possono invitare**: per consentire agli amministratori e agli utenti del ruolo "invitato Guest" di invitare i guest, impostare questo criterio su **Sì**.
   - **I membri possono invitare**: per consentire ai membri non amministratori della directory di invitare i guest, impostare questo criterio su **Sì**.
   - I **Guest possono invitare**: per consentire ai guest di invitare altri Guest, impostare questo criterio su **Sì**.
   - **Abilita il codice di accesso monouso per i Guest (anteprima)** : per altre informazioni sulla funzionalità di accesso monouso, vedere la pagina relativa all' [autenticazione del codice di posta elettronica monouso (anteprima)](one-time-passcode.md).
   - **Restrizioni di collaborazione**: per altre informazioni su come consentire o bloccare gli inviti a specifici domini, vedere [consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Assegnare il ruolo di invito Guest a un utente

Con il ruolo invito Guest è possibile concedere a singoli utenti la possibilità di invitare i guest senza assegnare loro un amministratore globale o un altro ruolo di amministratore. Assegnare il ruolo di invito Guest a singoli utenti. Assicurarsi quindi che **gli amministratori e gli utenti nel ruolo mittente dell'invito Guest possano invitare** a **Sì**.

Ecco un esempio che illustra l'aggiunta di un utente al ruolo Mittente dell'invito guest con PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Aggiungere utenti guest di Collaborazione B2B senza un invito](add-user-without-invite.md)
- [Aggiunta di un utente di Collaborazione B2B a un ruolo](add-guest-to-role.md)


