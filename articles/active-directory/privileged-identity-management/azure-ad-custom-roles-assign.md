---
title: Assegnare un ruolo personalizzato di Azure AD in Privileged Identity Management (PIM) | Microsoft Docs
description: Come assegnare un ruolo personalizzato di Azure AD per l'assegnazione di Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a3f6eb815677133f3d7fe6ce07d6abf23db1f04
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947517"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Assegnare un ruolo personalizzato di Azure AD in Privileged Identity Management

Questo articolo illustra come usare Privileged Identity Management (PIM) per creare l'assegnazione JIT e con vincoli di tempo ai ruoli personalizzati creati per gestire le applicazioni nell'esperienza di amministrazione di Azure Active Directory (Azure AD).

- Per altre informazioni su come creare ruoli personalizzati per delegare la gestione delle applicazioni in Azure AD, vedere [Ruoli di amministratore personalizzati in Azure Active Directory (anteprima)](../users-groups-roles/roles-custom-overview.md).
- Se si usa Privileged Identity Management per la prima volta, vedere [Iniziare a usare PIM](pim-getting-started.md) per altre informazioni.
- Per informazioni su come concedere a un altro amministratore l'accesso per la gestione di Privileged Identity Management, vedere [Concedere l'accesso ad altri amministratori per gestire PIM](pim-how-to-give-access-to-pim.md).

> [!NOTE]
> I ruoli personalizzati di Azure AD non sono integrati nei ruoli della directory predefiniti durante la fase di anteprima. Non appena la funzionalità sarà disponibile a livello generale, la gestione dei ruoli verrà eseguita nell'esperienza dei ruoli predefiniti.

## <a name="assign-a-role"></a>Assegnare un ruolo

Privileged Identity Management può gestire i ruoli personalizzati creati in Gestione applicazioni di Azure Active Directory (Azure AD).  La procedura seguente consente di creare un'assegnazione idonea a un ruolo della directory personalizzato.

1. Accedere a [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) nel portale di Azure usando un account utente assegnato al ruolo di amministratore ruolo con privilegi.
1. Selezionare **Ruoli personalizzati di Azure AD (anteprima)** .

    ![Selezionare Ruoli personalizzati di Azure AD (anteprima) per visualizzare le assegnazioni di ruolo idonee](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Selezionare **Ruoli** per visualizzare un elenco di ruoli personalizzati per le applicazioni Azure AD.

    ![Selezionare Ruoli per visualizzare l'elenco delle assegnazioni di ruolo idonee](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Selezionare **Aggiungi membro** per aprire la pagina di assegnazione.
1. Per limitare l'ambito dell'assegnazione di ruolo a un'applicazione singola, selezionare **Ambito** per specificare un ambito dell'applicazione.

    ![limitare l'ambito delle assegnazioni di ruolo idonee in Azure AD](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Fare clic su **Selezionare un ruolo** per aprire l'elenco **Selezionare un ruolo**.

    ![selezionare il ruolo idoneo da assegnare a un utente](./media/azure-ad-custom-roles-assign/select-role.png)

1. Selezionare un ruolo da assegnare e quindi fare clic su **Seleziona**. Verrà visualizzato l'elenco **Selezionare un membro**.

    ![selezionare l'utente a cui verrà assegnato il ruolo](./media/azure-ad-custom-roles-assign/select-member.png)

1. Selezionare l'utente da assegnare al ruolo e quindi fare clic su **Seleziona**. Verrà visualizzato l'elenco **Impostazioni di appartenenza**.

    ![Impostare il tipo di assegnazione di ruolo su idoneo o attivo](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. Nella pagina **Impostazioni di appartenenza** selezionare **Idoneo** o **Attivo**:

    - Le assegnazioni di tipo **Idoneo** richiedono all'utente assegnato al ruolo di eseguire un'azione prima di poter usare il ruolo. Le azioni possono includere il superamento di un controllo di autenticazione a più fattori, l'indicazione di una motivazione aziendale o la richiesta di approvazione da parte di responsabili approvazione designati.
    - Le assegnazioni di tipo **Attivo** non richiedono all'utente assegnato di eseguire alcuna azione per poter usare il ruolo. Per gli utenti attivi i privilegi sono sempre assegnati al ruolo.

1. Se la casella di controllo **Permanente** è presente e disponibile, a seconda delle impostazioni del ruolo, è possibile specificare se l'assegnazione è permanente. Selezionare la casella di controllo per rendere l'assegnazione con idoneità permanente o con assegnazione permanente. Deselezionare la casella di controllo per specificare una durata per l'assegnazione.
1. Per creare la nuova assegnazione di ruolo, fare clic su **Salva** e quindi su **Aggiungi**. Verrà visualizzata una notifica di stato per il processo di assegnazione.

Per verificare l'assegnazione di ruolo, in un ruolo aperto selezionare **Assegnazioni** > **Assegna** e verificare che l'assegnazione di ruolo sia identificata correttamente come idonea o attiva.

 ![Controllare se l'assegnazione di ruolo è visibile come idonea o attiva](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Passaggi successivi

- [Attivare un ruolo personalizzato di Azure AD](azure-ad-custom-roles-assign.md)
- [Rimuovere o aggiornare un'assegnazione di ruolo personalizzato di Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurare un'assegnazione di ruolo personalizzato di Azure AD](azure-ad-custom-roles-configure.md)
- [Definizioni dei ruoli in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
