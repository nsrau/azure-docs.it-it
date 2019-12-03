---
title: Aggiungere o rimuovere assegnazioni di ruolo usando RBAC di Azure e il portale di Azure
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 17a325e96e9709b60da2f23d1dc68e3300fde80c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707861"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Aggiungere o rimuovere assegnazioni di ruolo usando RBAC di Azure e il portale di Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] questo articolo descrive come assegnare i ruoli usando il portale di Azure.

Se è necessario assegnare ruoli di amministratore in Azure Active Directory, vedere [visualizzare e assegnare ruoli di amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere o rimuovere assegnazioni di ruolo, è necessario disporre di:

- autorizzazioni `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, ad esempio [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) o [Proprietario](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Panoramica di controllo di accesso (IAM)

Il **controllo di accesso (IAM)** è il pannello usato per assegnare i ruoli. È noto anche come gestione delle identità e degli accessi e viene visualizzato in diverse posizioni nel portale di Azure. Di seguito viene riportato un esempio del pannello Controllo di accesso (IAM) per una sottoscrizione.

![Pannello Controllo di accesso (IAM) per una sottoscrizione](./media/role-assignments-portal/access-control-subscription.png)

Per essere il più efficace con il pannello controllo di accesso (IAM), consente di rispondere alle tre domande seguenti quando si tenta di assegnare un ruolo:

1. **Chi deve accedere?**

    Che fa riferimento a un utente, un gruppo, un'entità servizio o un'identità gestita. Questa operazione è detta anche *entità di sicurezza*.

1. **Quale ruolo è necessario?**

    Le autorizzazioni vengono raggruppate in ruoli. È possibile scegliere da un elenco di diversi [ruoli predefiniti](built-in-roles.md) oppure usare ruoli personalizzati.

1. **Dove è necessario accedere?**

    Dove si riferisce al set di risorse a cui viene applicato l'accesso. Dove può essere un gruppo di gestione, una sottoscrizione, un gruppo di risorse o una singola risorsa, ad esempio un account di archiviazione. Questa operazione viene definita *ambito*.

## <a name="add-a-role-assignment"></a>Aggiungi un'assegnazione di ruolo

Attenersi alla procedura seguente per assegnare un ruolo a ambiti diversi.

1. Nella portale di Azure fare clic su **tutti i servizi** e quindi selezionare l'ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)** .

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

1. Fare clic su **Aggiungi** > **Aggiungi assegnazione di ruolo** per aprire il riquadro Aggiungi assegnazione di ruolo.

   Se non si dispone delle autorizzazioni per assegnare ruoli, l'opzione Aggiungi assegnazione di ruolo verrà disabilitata.

   ![Menu Aggiungi](./media/role-assignments-portal/add-menu.png)

   ![Riquadro Aggiungi assegnazione di ruolo](./media/role-assignments-portal/add-role-assignment.png)

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore Macchina virtuale**.

1. Nell'elenco **Seleziona** selezionare un utente, gruppo, entità servizio o identità gestita. Se l'entità di sicurezza non è visualizzata nell'elenco, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati, gli indirizzi e-mail e gli identificatori di oggetto.

1. Fare clic su **Salva** per assegnare un ruolo.

   Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito selezionato.

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Assegnare un utente come amministratore di una sottoscrizione

Per rendere un utente amministratore per una sottoscrizione di Azure, assegnargli il ruolo di [Proprietario](built-in-roles.md#owner) nell'ambito della sottoscrizione. Il ruolo proprietario concede all'utente l'accesso completo a tutte le risorse nella sottoscrizione, inclusa l'autorizzazione per concedere l'accesso ad altri utenti. Questi passaggi sono gli stessi di qualsiasi altra assegnazione di ruoli.

1. Nel portale di Azure fare clic su **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Fare clic sulla sottoscrizione in cui si desidera aggiungere un'assegnazione di ruolo.

1. Fare clic su **Controllo di accesso (IAM)** .

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questa sottoscrizione.

1. Fare clic su **Aggiungi** > **Aggiungi assegnazione di ruolo** per aprire il riquadro Aggiungi assegnazione di ruolo.

   Se non si dispone delle autorizzazioni per assegnare ruoli, l'opzione Aggiungi assegnazione di ruolo verrà disabilitata.

   ![Menu Aggiungi](./media/role-assignments-portal/add-menu.png)

   ![Riquadro Aggiungi assegnazione di ruolo](./media/role-assignments-portal/add-role-assignment.png)

1. Nell’elenco a discesa **Ruolo**, selezionare il ruolo **Proprietario.**

1. Nell'elenco **Selezione**, selezionare un utente. Se nell'elenco l'utente non è visualizzato, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati e gli indirizzi e-mail.

1. Fare clic su **Salva** per assegnare un ruolo.

   Dopo qualche istante, all’utente viene assegnato il ruolo di Proprietario per l'ambito della sottoscrizione.

## <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

Per rimuovere un accesso mediante il controllo degli accessi in base al ruolo, si rimuove un'assegnazione di ruolo. Per rimuovere un'assegnazione di ruolo, attenersi alla seguente procedura.

1. Aprire **Controllo di accesso (IAM)** sull'ambito, come gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui si intende rimuovere l'accesso.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questa sottoscrizione.

1. Nell'elenco delle assegnazioni di ruolo aggiungere un segno di spunta accanto all'entità di sicurezza con l'assegnazione di ruolo che si vuole rimuovere.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Fare clic su **Rimuovi**.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment.png)

1. Nella finestra con il messaggio di rimozione dell'assegnazione di ruolo fare clic su **Sì**.

    Le assegnazioni di ruolo ereditate non possono essere rimosse. Per rimuovere un'assegnazione di ruolo ereditata, è necessario eseguire questa operazione nell'ambito di creazione dell'assegnazione di ruolo. Nella colonna **Ambito**, accanto a **(ereditato)** è presente un collegamento che consente di passare all'ambito in cui è stato assegnato questo ruolo. Passare all'ambito indicato nell'elenco per rimuovere l'assegnazione di ruolo.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Passaggi successivi

- [Elencare le assegnazioni di ruolo usando RBAC di Azure e il portale di Azure](role-assignments-list-portal.md)
- [Esercitazione: concedere a un utente l'accesso alle risorse di Azure usando RBAC e il portale di Azure](quickstart-assign-role-user-portal.md)
- [Risolvere i problemi del controllo degli accessi in base al ruolo per le risorse di Azure](troubleshooting.md)
- [Organizzare le risorse con i gruppi di gestione di Azure ](../governance/management-groups/overview.md)
