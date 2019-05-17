---
title: Abilitare le impostazioni di collaborazione esterna B2B - Azure Active Directory | Microsoft Docs
description: Informazioni su come abilitare Active Directory B2B collaboration esterni e gestire utenti autorizzati a invitare utenti guest. Usare il ruolo mittente dell'invito Guest per delegare gli inviti.
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
ms.openlocfilehash: 11dda7fc3760f468c094fb4cf4484a27895f83b9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812679"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Ambiente di collaborazione B2B esterni e la gestione di utenti autorizzati a invitare utenti guest

Questo articolo descrive come abilitare la collaborazione B2B di Azure Active Directory (Azure AD) e determinare chi possono invitare utenti guest. Per impostazione predefinita, tutti gli utenti e gli utenti guest nella directory possono invitare utenti guest anche se non sono state assegnate a un ruolo di amministratore. Le impostazioni di collaborazione esterna consentono di attivare gli inviti guest attiva o disattiva per diversi tipi di utenti nell'organizzazione. È anche possibile delegare gli inviti a singoli utenti assegnando i ruoli che consentono loro di invitare utenti guest.

## <a name="configure-b2b-external-collaboration-settings"></a>Configurare le impostazioni di collaborazione esterna B2B

Con Collaborazione B2B di Azure AD, un amministratore tenant può impostare i criteri di invito seguenti:

- Disattivare gli inviti
- Solo gli amministratori e gli utenti nel ruolo Mittente dell'invito guest possono inviare inviti
- Gli amministratori, il ruolo Mittente dell'invito guest e i membri possono inviare inviti
- Possono inviare inviti tutti gli utenti, inclusi gli utenti guest

Per impostazione predefinita, tutti gli utenti, inclusi gli utenti Guest, possono invitare utenti guest.

### <a name="to-configure-external-collaboration-settings"></a>Per configurare le impostazioni di collaborazione esterna:

1. Accedi per il [portale di Azure](https://portal.azure.com) come amministratore tenant.
2. Selezionare **Azure Active Directory** > **Utenti** > **Impostazioni utente**.
3. In **Utenti esterni** selezionare **Manage external collaboration settings** (Gestisci impostazioni di collaborazione esterna).
   > [!NOTE]
   > Le **Impostazioni di collaborazione esterna** sono inoltre disponibili dalla pagina **Relazioni aziendali**. In Azure Active Directory, da **Gestisci** passare a **Relazioni aziendali** > **Impostazioni**.
4. Nel **impostazioni di collaborazione esterna** pagina, scegliere i criteri si desidera abilitare.

   ![Impostazioni di collaborazione esterna](./media/delegate-invitations/control-who-to-invite.png)

  - **Le autorizzazioni agli utenti guest sono limitate**: Questi criteri determinano le autorizzazioni per gli utenti guest nella directory. Selezionare **Sì** ai guest di blocco da determinate attività di directory, come l'enumerazione degli utenti, gruppi o altre risorse della directory. Selezionare **No** per assegnare gli utenti guest lo stesso accesso ai dati della directory come utenti nella directory.
   - **Gli amministratori e gli utenti nel ruolo mittente dell'invito guest possono invitare**: Per consentire agli amministratori e utenti al ruolo "Mittente dell'invito Guest" per invitare utenti Guest, impostare questo criterio **Sì**.
   - **I membri possono invitare**: Per consentire ai membri senza privilegi di amministratore della directory invitare utenti Guest, impostare questo criterio **Sì**.
   - **Gli utenti guest possono invitare**: Per consentire agli utenti guest di invitare altri utenti Guest, impostare questo criterio **Sì**.
   - **Abilitare i Passcode monouso tramite posta elettronica per gli utenti guest (anteprima)**: Per altre informazioni sulla funzionalità di passcode monouso, vedere [autenticazione di passcode monouso inviato tramite posta elettronica (anteprima)](one-time-passcode.md).
   - **Restrizioni di collaborazione**: Per altre informazioni per consentire o bloccare gli inviti ai domini specifici, vedere [bloccare gli inviti agli utenti B2B da organizzazioni specifiche](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Assegnare il ruolo mittente dell'invito Guest a un utente

Con il ruolo mittente dell'invito Guest, è possibile assegnare singoli utenti la possibilità di invitare utenti guest senza assegnarle un amministratore globale o altro ruolo di amministratore. Assegnare il ruolo di mittente dell'invito Guest a singoli utenti. Quindi è opportuno assicurarsi che **gli amministratori e gli utenti nel ruolo mittente dell'invito guest possono invitare** al **Yes**.

Ecco un esempio che illustra l'aggiunta di un utente al ruolo Mittente dell'invito guest con PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Aggiungere utenti guest di Collaborazione B2B senza un invito](add-user-without-invite.md)
- [Aggiunta di un utente di Collaborazione B2B a un ruolo](add-guest-to-role.md)


