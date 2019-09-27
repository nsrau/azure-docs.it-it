---
title: Gestire l'accesso alle risorse di Azure usando RBAC e il portale di Azure | Microsoft Docs
description: Informazioni su come gestire l'accesso alle risorse di Azure per utenti, gruppi, entità servizio e identità gestite tramite il controllo degli accessi in base al ruolo e il portale di Azure. Vengono presentate anche la visualizzazione, la concessione e la rimozione dell'accesso.
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
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e9a53c41535c17de2d56227012160c7f6eb25c6
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337614"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e il portale di Azure

Il [controllo degli accessi in base al ruolo](overview.md) è la modalità di gestione dell'accesso alle risorse di Azure. Questo articolo descrive come gestire l'accesso usando il portale di Azure. Se è necessario gestire l'accesso ai Azure Active Directory, vedere [visualizzare e assegnare ruoli di amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere e rimuovere assegnazioni di ruoli, l'utente deve disporre di:

- autorizzazioni `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, ad esempio [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) o [Proprietario](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Panoramica di controllo di accesso (IAM)

Il **controllo di accesso (IAM)** è il pannello usato per gestire l'accesso alle risorse di Azure. È noto anche come gestione delle identità e degli accessi e viene visualizzato in diverse posizioni nel portale di Azure. Di seguito viene riportato un esempio del pannello Controllo di accesso (IAM) per una sottoscrizione.

![Pannello Controllo di accesso (IAM) per una sottoscrizione](./media/role-assignments-portal/access-control-numbers.png)

Nella tabella seguente vengono descritti gli elementi utilizzati per:

| # | Elemento | Elementi usati per |
| --- | --- | --- |
| 1 | Risorsa in cui è aperto il controllo di accesso (IAM) | Identificare l'ambito (sottoscrizione in questo esempio) |
| 2 | Pulsante **Aggiungi** | Aggiungere assegnazioni di ruolo |
| 3 | Scheda **Controlla accesso** | Visualizzare le assegnazioni di ruolo per un singolo utente |
| 4 | Scheda **assegnazioni di ruolo** | Visualizzare le assegnazioni di ruolo nell'ambito corrente |
| 5 | Scheda **ruoli** | Visualizza tutti i ruoli e le autorizzazioni |

Per essere il più efficace con il pannello controllo di accesso (IAM), consente di rispondere alle tre domande seguenti quando si tenta di gestire l'accesso:

1. **Chi deve accedere?**

    Che fa riferimento a un utente, un gruppo, un'entità servizio o un'identità gestita. Questa operazione è detta anche *entità di sicurezza*.

1. **Quali sono le autorizzazioni necessarie?**

    Le autorizzazioni vengono raggruppate in ruoli. È possibile scegliere da un elenco di diversi ruoli predefiniti.

1. **Dove è necessario accedere?**

    Dove si riferisce al set di risorse a cui viene applicato l'accesso. Dove può essere un gruppo di gestione, una sottoscrizione, un gruppo di risorse o una singola risorsa, ad esempio un account di archiviazione. Questa operazione viene definita *ambito*.

## <a name="open-access-control-iam"></a>Controllo di accesso (IAM)

Per prima cosa è necessario decidere dove aprire il pannello controllo di accesso (IAM). Dipende dalle risorse per cui si desidera gestire l'accesso. Si desidera gestire l'accesso per tutti gli elementi di un gruppo di gestione, tutti gli elementi in una sottoscrizione, tutti gli elementi in un gruppo di risorse o una singola risorsa?

1. Nella portale di Azure fare clic su **tutti i servizi** e quindi selezionare l'ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)** .

    Di seguito viene riportato un esempio del pannello Controllo di accesso (IAM) per una sottoscrizione. Se si apportano modifiche al controllo di accesso, si applicano all'intera sottoscrizione.

    ![Pannello Controllo di accesso (IAM) per una sottoscrizione](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Visualizza i ruoli e le autorizzazioni

Una definizione di ruolo è una raccolta di autorizzazioni usata per l'assegnazione dei ruoli. Azure dispone di più di 70 [ruoli predefiniti per le risorse di Azure](built-in-roles.md). Per visualizzare i ruoli e le autorizzazioni disponibili, attenersi alla seguente procedura.

1. Aprire il **controllo di accesso (IAM)** in qualsiasi ambito.

1. Fare clic su **Ruoli** per visualizzare l'elenco di tutti i ruoli predefiniti e personalizzati.

   È possibile visualizzare il numero di utenti e gruppi assegnati a ogni ruolo nell'ambito corrente.

   ![Elenco dei ruoli](./media/role-assignments-portal/roles-list.png)

1. Fare clic su un singolo ruolo per visualizzare a chi è stato assegnato e le relative autorizzazioni.

   ![Assegnazioni di ruolo](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Visualizza le assegnazioni di ruolo

Quando si gestisce l'accesso, si desidera sapere chi ha accesso, quali sono le relative autorizzazioni e in quale ambito. Per elencare l'accesso per un utente, un gruppo, un'entità servizio o un'identità gestita, è possibile visualizzare le relative assegnazioni di ruolo.

### <a name="view-role-assignments-for-a-single-user"></a>Visualizzare le assegnazioni di ruolo di un singolo utente

Seguire questi passaggi per visualizzare l'accesso per un singolo utente, gruppo, entità servizio o identità gestita in un ambito specifico.

1. Aprire **Controllo di accesso (IAM)** sull'ambito, come gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui si intende visualizzare l'accesso.

1. Fare clic sulla scheda **Verifica l'accesso**.

    ![Controllo di accesso - Scheda Verifica l'accesso](./media/role-assignments-portal/access-control-check-access.png)

1. Nell'elenco **Trova**, selezionare il tipo di entità di sicurezza di cui si intende verificare l'accesso.

1. Nella casella di ricerca, immettere una stringa per eseguire ricerche nella directory in base ai nomi visualizzati, agli indirizzi di posta elettronica o agli identificatori di oggetto.

    ![Elenco di selezione per la verifica dell'accesso](./media/role-assignments-portal/check-access-select.png)

1. Fare clic sull'entità di sicurezza per aprire il riquadro **Assegnazioni**.

    ![Riquadro Assegnazioni](./media/role-assignments-portal/check-access-assignments.png)

    In questo riquadro è possibile visualizzare i ruoli assegnati all'entità di sicurezza e all'ambito selezionati. Se sono presenti assegnazioni negate in questo ambito o ereditate da questo ambito, verranno elencate.

### <a name="view-all-role-assignments-at-a-scope"></a>Visualizzare le assegnazioni di ruolo in un ambito

1. Aprire **Controllo di accesso (IAM)** sull'ambito, come gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui si intende visualizzare l'accesso.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

   ![Controllo di accesso - Scheda Assegnazioni di ruolo](./media/role-assignments-portal/access-control-role-assignments.png)

   Nella scheda Assegnazioni di ruolo è possibile visualizzare chi ha accesso a questo ambito. Si noterà che l'ambito di alcuni ruoli è **Questa risorsa**, mentre quello di altri è **(Ereditato)** da un altro ambito. L'accesso viene assegnato in modo specifico alla risorsa oppure ereditato da un'assegnazione nell'ambito padre.

## <a name="add-a-role-assignment"></a>Aggiungi un'assegnazione di ruolo

Per concedere l'accesso in RBAC, assegnare un ruolo a un utente, gruppo, entità servizio o identità gestita. Eseguire questa procedura per concedere l'accesso ad ambiti diversi.

### <a name="assign-a-role-at-a-scope"></a>Assegnare un ruolo a un ambito

1. Aprire **Controllo di accesso (IAM)** sull'ambito, come gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui si intende concedere l'accesso.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

1. Fare clic su **Aggiungi** > **Aggiungi assegnazione di ruolo** per aprire il riquadro Aggiungi assegnazione di ruolo.

   Se non si dispone delle autorizzazioni per assegnare ruoli, l'opzione Aggiungi assegnazione di ruolo verrà disabilitata.

   ![Menu Aggiungi](./media/role-assignments-portal/add-menu.png)

   ![Riquadro Aggiungi assegnazione di ruolo](./media/role-assignments-portal/add-role-assignment.png)

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore Macchina virtuale**.

1. Nell'elenco **Seleziona** selezionare un utente, gruppo, entità servizio o identità gestita. Se l'entità di sicurezza non è visualizzata nell'elenco, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati, gli indirizzi e-mail e gli identificatori di oggetto.

1. Fare clic su **Salva** per assegnare un ruolo.

   Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito selezionato.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Assegnare un utente come amministratore di una sottoscrizione

Per rendere un utente amministratore per una sottoscrizione di Azure, assegnargli il ruolo di [Proprietario](built-in-roles.md#owner) nell'ambito della sottoscrizione. Questo ruolo garantisce all'utente l'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. Questi passaggi sono gli stessi di qualsiasi altra assegnazione di ruoli.

1. Nel portale di Azure fare clic su **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Fare clic sulla sottoscrizione in cui si intende concedere l'accesso.

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

## <a name="remove-role-assignments"></a>Rimuovi assegnazioni di ruolo

Per rimuovere un accesso mediante il controllo degli accessi in base al ruolo, si rimuove un'assegnazione di ruolo. Eseguire la procedura seguente per rimuovere l'accesso.

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

* [Esercitazione: Concedere a un utente l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e il portale di Azure](quickstart-assign-role-user-portal.md)
* [Esercitazione: Concedere a un utente l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Risolvere i problemi del controllo degli accessi in base al ruolo per le risorse di Azure](troubleshooting.md)
* [Organizzare le risorse con i gruppi di gestione di Azure ](../governance/management-groups/overview.md)
