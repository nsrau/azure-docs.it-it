---
title: Aggiungere o rimuovere assegnazioni di ruolo per utenti esterni con controllo degli accessi in base al ruolo e il portale di AzureAdd or remove role assignments for external users with RBAC and the Azure portal
description: Informazioni su come concedere l'accesso alle risorse di Azure per gli utenti esterni a un'organizzazione usando il controllo degli accessi in base al ruolo di Azure.Learn how to grant access to Azure resources for users external to an organization using Azure role-based access control (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 1c440b85f792ac5bb1336f4d20f930aafc38ad7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245642"
---
# <a name="add-or-remove-role-assignments-for-external-guest-users-using-azure-rbac-and-the-azure-portal"></a>Aggiungere o rimuovere assegnazioni di ruolo per gli utenti guest esterni usando controllo degli accessi in base al ruolo di Azure e il portale di AzureAdd or remove role assignments for external guest users using Azure RBAC and the Azure portal

Il controllo degli [accessi in base](overview.md) al ruolo di Azure consente una migliore gestione della sicurezza per le organizzazioni di grandi dimensioni e per le piccole e medie imprese che lavorano con collaboratori esterni, fornitori o liberi professionisti che necessitano di accedere a risorse specifiche nell'ambiente, ma non necessariamente all'intera infrastruttura o a qualsiasi ambito correlato alla fatturazione. È possibile usare le funzionalità in [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md) per collaborare con utenti guest esterni ed è possibile usare il controllo degli accessi in base al ruolo per concedere solo le autorizzazioni necessarie agli utenti guest nell'ambiente.

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere o rimuovere assegnazioni di ruolo, è necessario disporre di:To add or remove role assignments, you must have:

- autorizzazioni `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, ad esempio [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) o [Proprietario](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>Quando inviterete gli utenti guest?

Ecco un paio di scenari di esempio in cui è possibile invitare utenti guest nell'organizzazione e concedere autorizzazioni:Here are a couple example scenarios when you might invite guest users to your organization and grant permissions:

- Consentire a un fornitore esterno indipendente che ha solo un account di posta elettronica di accedere alle risorse di Azure per un progetto.
- Consentire a un partner esterno di gestire determinate risorse o un'intera sottoscrizione.
- Consentire ai tecnici di assistenza che non fanno parte della propria organizzazione (come il supporto Microsoft) di accedere temporaneamente alla propria risorsa di Azure per la risoluzione dei problemi.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Differenze di autorizzazione tra utenti membri e utenti guestPermission differences between member users and guest users

I membri nativi di una directory (utenti membri) dispongono di autorizzazioni diverse rispetto agli utenti invitati da un'altra directory come guest di collaborazione B2B (utenti guest). Ad esempio, i membri utente possono leggere quasi tutte le informazioni della directory mentre gli utenti guest hanno limitato le autorizzazioni di directory. Per altre informazioni sugli utenti membri e gli utenti guest, vedere [Quali sono le autorizzazioni utente predefinite in Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="add-a-guest-user-to-your-directory"></a>Aggiungere un utente guest alla directory

Seguire questi passaggi per aggiungere un utente guest alla directory usando la pagina Azure Active Directory.Follow these steps to add a guest user to your directory using the Azure Active Directory page.

1. Assicurarsi che le impostazioni di collaborazione esterna dell'organizzazione siano configurate in modo che sia possibile invitare ospiti. Per ulteriori informazioni, consultate [Abilitare la collaborazione esterna B2B e gestire chi può invitare ospiti.](../active-directory/b2b/delegate-invitations.md)

1. Nel portale di Azure fare clic su**Nuovo utente guest**di Azure Active **Directory** > **Users** > .

    ![Nuova funzionalità utente guest nel portale di AzureNew guest user feature in Azure portal](./media/role-assignments-external-users/invite-guest-user.png)

1. Seguire i passaggi per aggiungere un nuovo utente guest. Per altre informazioni, vedere Aggiungere utenti di Collaborazione B2B di Azure nel portale di Azure.For more information, see [Add Azure Active Directory B2B collaboration users in the Azure portal.](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory)

In seguito all'aggiunta di un utente guest alla directory, è possibile inviare all'utente guest un collegamento diretto a un'app condivisa oppure l'utente guest può selezionare l'URL di riscatto nel messaggio di posta elettronica di invito.

![Messaggio di posta elettronica di invito utente guest](./media/role-assignments-external-users/invite-email.png)

Affinché l'utente guest sia in grado di accedere alla directory, deve completare il processo di invito.

![Autorizzazioni di revisione invito utente guest](./media/role-assignments-external-users/invite-review-permissions.png)

Per altre informazioni sul processo di invito, vedere Riscatto dell'invito alla collaborazione B2B di Azure Active Directory.For more information about the invitation process, see [Azure Active Directory B2B collaboration invitation redemption](../active-directory/b2b/redemption-experience.md).

## <a name="add-a-role-assignment-for-a-guest-user"></a>Aggiungere un'assegnazione di ruolo per un utente guestAdd a role assignment for a guest user

Per concedere l'accesso in RBAC, si crea un'assegnazione di ruolo. Per aggiungere un'assegnazione di ruolo per un utente guest, seguire [gli stessi passaggi](role-assignments-portal.md#add-a-role-assignment) necessari per un utente membro, un gruppo, un'entità servizio o un'identità gestita. Seguire questi passaggi per aggiungere un'assegnazione di ruolo per un utente guest in ambiti diversi.

1. Nel portale di Azure fare clic su **Tutti i servizi**.

1.  Selezionare il set di risorse a cui si applica l'accesso, noto anche come ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)**.

    La schermata seguente mostra un esempio del pannello Controllo di accesso (IAM) per un gruppo di risorse. Se si apportano modifiche al controllo di accesso, queste verranno applicate solo al gruppo di risorse.

    ![Pannello Controllo di accesso (IAM) per un gruppo di risorse](./media/role-assignments-external-users/access-control-resource-group.png)

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

1. Fare clic su **Aggiungi** > **assegnazione ruolo** per aprire il riquadro Aggiungi assegnazione ruolo.

    Se non si dispone delle autorizzazioni per assegnare ruoli, l'opzione Aggiungi assegnazione di ruolo verrà disabilitata.

    ![Menu Aggiungi](./media/role-assignments-external-users/add-menu.png)

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore Macchina virtuale**.

1. Nell'elenco **Seleziona** selezionare l'utente guest. Se l'utente non è presente nell'elenco, è possibile **digitare** nella casella Seleziona per cercare nella directory i nomi visualizzati, gli indirizzi di posta elettronica e gli identificatori di oggetto.

   ![Riquadro Aggiungi assegnazione di ruolo](./media/role-assignments-external-users/add-role-assignment.png)

1. Fare clic su **Salva** per assegnare il ruolo nell'ambito selezionato.

    ![Assegnazione di ruolo per collaboratore di macchine virtualiRole assignment for Virtual Machine Contributor](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>Aggiungere un'assegnazione di ruolo per un utente guest non ancora presente nella directoryAdd a role assignment for a guest user not yet in your directory

Per aggiungere un'assegnazione di ruolo per un utente guest, seguire [gli stessi passaggi](role-assignments-portal.md#add-a-role-assignment) necessari per un utente membro, un gruppo, un'entità servizio o un'identità gestita.

Se l'utente guest non è ancora presente nella directory, è possibile invitarla direttamente dal riquadro Aggiungi assegnazione ruolo.

1. Nel portale di Azure fare clic su **Tutti i servizi**.

1.  Selezionare il set di risorse a cui si applica l'accesso, noto anche come ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)**.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

1. Fare clic su **Aggiungi** > **assegnazione ruolo** per aprire il riquadro Aggiungi assegnazione ruolo.

    ![Menu Aggiungi](./media/role-assignments-external-users/add-menu.png)

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore Macchina virtuale**.

1. Nell'elenco **Seleziona** digitare l'indirizzo di posta elettronica della persona che si desidera invitare e selezionarla.

   ![Invita utente guest nel riquadro Aggiungi assegnazione ruolo](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Fare clic su **Salva** per aggiungere l'utente guest alla directory, assegnare il ruolo e inviare un invito.

    Dopo alcuni istanti, vedrai una notifica dell'assegnazione di ruolo e informazioni sull'invito.

    ![Assegnazione di ruolo e notifica utente invitato](./media/role-assignments-external-users/invited-user-notification.png)

1. Per invitare manualmente l'utente guest, fare clic con il pulsante destro del mouse e copiare il collegamento di invito nella notifica. Non fare clic sul collegamento di invito perché avvia il processo di invito.

    Il link di invito avrà il seguente formato:

    `https://invitations.microsoft.com/redeem/...`

1. Inviare il collegamento di invito all'utente guest per completare il processo di invito.

    Per altre informazioni sul processo di invito, vedere Riscatto dell'invito alla collaborazione B2B di Azure Active Directory.For more information about the invitation process, see [Azure Active Directory B2B collaboration invitation redemption](../active-directory/b2b/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Rimuovere un utente guest dalla directory

Prima di rimuovere un utente guest da una directory, è necessario rimuovere tutte le assegnazioni di ruolo per tale utente guest. Attenersi alla seguente procedura per rimuovere un utente guest da una directory.

1. Aprire il controllo di accesso **(IAM)** in un ambito, ad esempio gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui l'utente guest dispone di un'assegnazione di ruolo.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo.

1. Nell'elenco delle assegnazioni di ruolo aggiungere un segno di spunta accanto all'utente guest con l'assegnazione di ruolo che si desidera rimuovere.

   ![Rimuovere l'assegnazione di ruolo](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Scegliere **Rimuovi**.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-external-users/remove-role-assignment.png)

1. Nella finestra con il messaggio di rimozione dell'assegnazione di ruolo fare clic su **Sì**.

1. Sulla barra di spostamento sinistra fare clic su**Utenti**di **Azure Active Directory** > .

1. Fare clic sull'utente guest che si desidera rimuovere.

1. Fare clic su **Elimina**.

   ![Elimina utente guest](./media/role-assignments-external-users/delete-guest-user.png)

1. Nel messaggio di eliminazione visualizzato fare clic su **Sì**.

## <a name="troubleshoot"></a>Risolvere i problemi

### <a name="guest-user-cannot-browse-the-directory"></a>L'utente guest non può esplorare la directory

Gli utenti guest hanno autorizzazioni di directory limitate. Ad esempio, gli utenti guest non possono esplorare la directory e non possono cercare gruppi o applicazioni. Per altre informazioni, vedere [Quali sono le autorizzazioni utente predefinite in Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

![L'utente guest non può esplorare gli utenti in una directory](./media/role-assignments-external-users/directory-no-users.png)

Se un utente guest necessita di privilegi aggiuntivi nella directory, è possibile assegnare un ruolo di directory all'utente guest. Se si vuole che un utente guest disponga dell'accesso completo in lettura alla directory, è possibile aggiungere l'utente guest al ruolo [Lettori di](../active-directory/users-groups-roles/directory-assign-admin-roles.md) directory in Azure AD. Per altre informazioni, vedere Concedere autorizzazioni agli utenti delle organizzazioni partner nel tenant di Azure Active Directory.For more information, see [Grant permissions to users from partner organizations in your Azure Active Directory tenant.](../active-directory/b2b/add-guest-to-role.md)

![Ruolo Assegna lettori di directory](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>L'utente guest non può esplorare utenti, gruppi o entità servizio per assegnare ruoliGuest user cannot browse users, groups, or service principals to assign roles

Gli utenti guest hanno autorizzazioni di directory limitate. Anche se un utente guest è un [proprietario](built-in-roles.md#owner) in un ambito, se tenta di aggiungere un'assegnazione di ruolo per concedere l'accesso a un altro utente, non può esplorare l'elenco di utenti, gruppi o entità servizio.

![L'utente guest non può esplorare le entità di sicurezza per assegnare ruoliGuest user cannot browse security principals to assign roles](./media/role-assignments-external-users/directory-no-browse.png)

Se l'utente guest conosce il nome esatto di accesso di un utente nella directory, può concedere l'accesso. Se si vuole che un utente guest disponga dell'accesso completo in lettura alla directory, è possibile aggiungere l'utente guest al ruolo [Lettori di](../active-directory/users-groups-roles/directory-assign-admin-roles.md) directory in Azure AD. Per altre informazioni, vedere Concedere autorizzazioni agli utenti delle organizzazioni partner nel tenant di Azure Active Directory.For more information, see [Grant permissions to users from partner organizations in your Azure Active Directory tenant.](../active-directory/b2b/add-guest-to-role.md)

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>L'utente guest non può registrare le applicazioni o creare entità servizioGuest user cannot register applications or create service principals

Gli utenti guest hanno autorizzazioni di directory limitate. Se un utente guest deve essere in grado di registrare applicazioni o creare entità servizio, è possibile aggiungere l'utente guest al ruolo [Sviluppatore applicazioni](../active-directory/users-groups-roles/directory-assign-admin-roles.md) in Azure AD. Per altre informazioni, vedere Concedere autorizzazioni agli utenti delle organizzazioni partner nel tenant di Azure Active Directory.For more information, see [Grant permissions to users from partner organizations in your Azure Active Directory tenant.](../active-directory/b2b/add-guest-to-role.md)

![L'utente ospite non può registrare le applicazioni](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>L'utente guest non vede la nuova directory

Se a un utente guest è stato concesso l'accesso a una directory, ma non viene visualizzata la nuova directory elencata nel portale di Azure quando tenta di passare nel riquadro **Directory e sottoscrizione,** assicurarsi che l'utente guest abbia completato il processo di invito. Per altre informazioni sul processo di invito, vedere Riscatto dell'invito alla collaborazione B2B di Azure Active Directory.For more information about the invitation process, see [Azure Active Directory B2B collaboration invitation redemption](../active-directory/b2b/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>L'utente guest non vede le risorse

Se a un utente guest è stato concesso l'accesso a una directory, ma non vengono visualizzate le risorse a cui è stato concesso l'accesso nel portale di Azure, assicurarsi che l'utente guest abbia selezionato la directory corretta. Un utente guest potrebbe avere accesso a più directory. Per passare da una directory all'altra, in alto a sinistra, fare clic su **Directory e sottoscrizione**e quindi fare clic sulla directory appropriata.

![Riquadro Directory e sottoscrizioni nel portale di AzureDirectories - Subscriptions pane in Azure portal](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](../active-directory/b2b/add-users-administrator.md)
- [Proprietà di un utente di Collaborazione B2B di Azure Active Directory](../active-directory/b2b/user-properties.md)
- [Elementi del messaggio di posta elettronica di invito di Collaborazione B2B - Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)
- [Aggiungere un utente guest come coamministratore](classic-administrators.md#add-a-guest-user-as-a-co-administrator)