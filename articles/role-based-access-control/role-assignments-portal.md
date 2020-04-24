---
title: Aggiungere o rimuovere assegnazioni di ruolo con RBAC e il portale di Azure
description: Informazioni su come concedere l'accesso alle risorse di Azure per utenti, gruppi, entità servizio o identità gestite usando il controllo degli accessi in base al ruolo di Azure (RBAC) e il portale di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e2458b07602bca3b6f12b2f486b509c11d705461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246357"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Aggiungere o rimuovere assegnazioni di ruolo usando RBAC di Azure e il portale di Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Questo articolo descrive come assegnare i ruoli usando il portale di Azure.

Se è necessario assegnare ruoli di amministratore in Azure Active Directory, vedere [visualizzare e assegnare ruoli di amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere o rimuovere assegnazioni di ruolo, è necessario disporre di:

- autorizzazioni `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, ad esempio [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) o [Proprietario](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Controllo di accesso (IAM)

Il **controllo di accesso (IAM)** è il pannello usato per assegnare i ruoli per concedere l'accesso alle risorse di Azure. È noto anche come gestione delle identità e degli accessi e viene visualizzato in diverse posizioni nel portale di Azure. Di seguito viene riportato un esempio del pannello Controllo di accesso (IAM) per una sottoscrizione.

![Pannello Controllo di accesso (IAM) per una sottoscrizione](./media/role-assignments-portal/access-control-subscription.png)

Per essere il più efficace con il pannello controllo di accesso (IAM), consente di rispondere alle tre domande seguenti quando si tenta di assegnare un ruolo:

1. **Chi deve accedere?**

    Che fa riferimento a un utente, un gruppo, un'entità servizio o un'identità gestita. Questa operazione è detta anche *entità di sicurezza*.

1. **Quale ruolo è necessario?**

    Le autorizzazioni vengono raggruppate in ruoli. È possibile scegliere da un elenco di diversi [ruoli predefiniti](built-in-roles.md) oppure è possibile usare ruoli personalizzati.

1. **Dove è necessario accedere?**

    Dove si riferisce al set di risorse a cui viene applicato l'accesso. Dove può essere un gruppo di gestione, una sottoscrizione, un gruppo di risorse o una singola risorsa, ad esempio un account di archiviazione. Questa operazione viene definita *ambito*.

## <a name="add-a-role-assignment"></a>Aggiungi un'assegnazione di ruolo

In controllo degli accessi in base al ruolo di Azure per concedere l'accesso a una risorsa di Azure è necessario aggiungere un'assegnazione di ruolo Per assegnare un ruolo, seguire questa procedura.

1. Nella portale di Azure fare clic su **tutti i servizi** e quindi selezionare l'ambito a cui si vuole concedere l'accesso. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica per tale ambito.

1. Fare clic su **controllo di accesso (IAM)**.

1. Fare clic sulla scheda **assegnazioni di ruolo** per visualizzare le assegnazioni di ruolo in questo ambito.

    ![Scheda controllo di accesso (IAM) e assegnazioni di ruolo](./media/role-assignments-portal/role-assignments.png)

1. Fare clic su **Aggiungi** > **Aggiungi assegnazione ruolo**.

   Se non si dispone delle autorizzazioni per assegnare ruoli, l'opzione Aggiungi assegnazione di ruolo verrà disabilitata.

   ![Menu Aggiungi](./media/role-assignments-portal/add-menu.png)

    Verrà aperto il riquadro Aggiungi assegnazione di ruolo.

   ![Riquadro Aggiungi assegnazione di ruolo](./media/role-assignments-portal/add-role-assignment.png)

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore Macchina virtuale**.

1. Nell'elenco **Seleziona** selezionare un utente, gruppo, entità servizio o identità gestita. Se l'entità di sicurezza non è visualizzata nell'elenco, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati, gli indirizzi e-mail e gli identificatori di oggetto.

1. Fare clic su **Salva** per assegnare un ruolo.

   Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito selezionato.

    ![Aggiunta dell'assegnazione di ruolo salvata](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Assegnare un utente come amministratore di una sottoscrizione

Per rendere un utente amministratore per una sottoscrizione di Azure, assegnargli il ruolo di [Proprietario](built-in-roles.md#owner) nell'ambito della sottoscrizione. Il ruolo proprietario concede all'utente l'accesso completo a tutte le risorse nella sottoscrizione, inclusa l'autorizzazione per concedere l'accesso ad altri utenti. Questi passaggi sono gli stessi di qualsiasi altra assegnazione di ruoli.

1. Nel portale di Azure fare clic su **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Fare clic sulla sottoscrizione in cui si intende concedere l'accesso.

1. Fare clic su **controllo di accesso (IAM)**.

1. Fare clic sulla scheda **assegnazioni di ruolo** per visualizzare le assegnazioni di ruolo per la sottoscrizione.

    ![Scheda controllo di accesso (IAM) e assegnazioni di ruolo](./media/role-assignments-portal/role-assignments.png)

1. Fare clic su **Aggiungi** > **Aggiungi assegnazione ruolo**.

   Se non si dispone delle autorizzazioni per assegnare ruoli, l'opzione Aggiungi assegnazione di ruolo verrà disabilitata.

   ![Menu Aggiungi](./media/role-assignments-portal/add-menu.png)

    Verrà aperto il riquadro Aggiungi assegnazione di ruolo.

   ![Riquadro Aggiungi assegnazione di ruolo](./media/role-assignments-portal/add-role-assignment.png)

1. Nell’elenco a discesa **Ruolo**, selezionare il ruolo **Proprietario.**

1. Nell'elenco **Selezione**, selezionare un utente. Se nell'elenco l'utente non è visualizzato, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati e gli indirizzi e-mail.

1. Fare clic su **Salva** per assegnare un ruolo.

   Dopo qualche istante, all’utente viene assegnato il ruolo di Proprietario per l'ambito della sottoscrizione.

## <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

In controllo degli accessi in base al ruolo di Azure è possibile rimuovere un'assegnazione di ruolo. Per rimuovere un'assegnazione di ruolo, attenersi alla seguente procedura.

1. Aprire **Controllo di accesso (IAM)** sull'ambito, come gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui si intende rimuovere l'accesso.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questa sottoscrizione.

1. Nell'elenco delle assegnazioni di ruolo aggiungere un segno di spunta accanto all'entità di sicurezza con l'assegnazione di ruolo che si vuole rimuovere.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Scegliere **Rimuovi**.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment.png)

1. Nella finestra con il messaggio di rimozione dell'assegnazione di ruolo fare clic su **Sì**.

    Se viene visualizzato un messaggio in cui non è possibile rimuovere le assegnazioni di ruolo ereditate, si sta tentando di rimuovere un'assegnazione di ruolo in un ambito figlio. È necessario aprire il controllo di accesso (IAM) nell'ambito in cui è stato assegnato il ruolo e riprovare. Un modo rapido per aprire il controllo di accesso (IAM) nell'ambito corretto consiste nell'esaminare la colonna **ambito** e fare clic sul collegamento accanto a **(ereditato)**.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Passaggi successivi

- [Elencare le assegnazioni di ruolo usando RBAC di Azure e il portale di Azure](role-assignments-list-portal.md)
- [Esercitazione: concedere a un utente l'accesso alle risorse di Azure usando RBAC e il portale di Azure](quickstart-assign-role-user-portal.md)
- [Risolvere i problemi del controllo degli accessi in base al ruolo per le risorse di Azure](troubleshooting.md)
- [Organizzare le risorse con i gruppi di gestione di Azure](../governance/management-groups/overview.md)
