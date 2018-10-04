---
title: Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure | Microsoft Docs
description: Informazioni su come gestire l'accesso per utenti, gruppi e applicazioni tramite il controllo degli accessi in base al ruolo e il portale di Azure. Vengono presentate anche la visualizzazione, la concessione e la rimozione dell'accesso.
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
ms.date: 09/05/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1cac4e4cee408e5208d2d5d84f81b8ad7a89f03b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033992"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure

[Il controllo degli accessi in base al ruolo](overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Questo articolo descrive come gestire l'accesso per utenti, gruppi e applicazioni tramite il controllo degli accessi in base al ruolo e il portale di Azure.

## <a name="list-roles"></a>Elenco dei ruoli

Una definizione di ruolo è una raccolta di autorizzazioni usata per l'assegnazione dei ruoli. Azure ha oltre 70 [ruoli predefiniti](built-in-roles.md). Per elencare i ruoli nel portale, eseguire questa procedura.

1. Nel portale di Azure scegliere **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Scegliere la propria sottoscrizione.

1. Scegliere **Controllo di accesso (IAM)**.

   ![Opzione Ruoli](./media/role-assignments-portal/list-subscription-access-control.png)

1. Scegliere **Ruoli** per visualizzare l'elenco di tutti i ruoli predefiniti e personalizzati.

   ![Opzione Ruoli](./media/role-assignments-portal/roles-option.png)

   È possibile visualizzare il numero di utenti e gruppi assegnati a ogni ruolo.

   ![Elenco dei ruoli](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>Elencare l'accesso

Quando si gestisce l'accesso, si ha l'esigenza di sapere chi ha accesso, quali sono le sue autorizzazioni e il relativo livello. Per visualizzare le informazioni sull'accesso, elencare le assegnazioni dei ruoli. Eseguire la procedura seguente per elencare l'accesso per gli utenti e l'accesso ad ambiti diversi.

### <a name="list-role-assignments-for-a-user"></a>Elencare i ruoli assegnati a un utente

1. Nell'elenco di spostamento scegliere **Azure Active Directory**.

1. Scegliere **Utenti** e aprire **Tutti gli utenti**.

   ![Pannello Tutti gli utenti di Azure Active Directory](./media/role-assignments-portal/aad-all-users.png)

1. Scegliere un singolo utente nell'elenco.

1. Nella sezione **Gestisci** scegliere **Risorse di Azure**.

   ![Risorse di Azure dell'utente di Azure Active Directory](./media/role-assignments-portal/aad-user-azure-resources.png)

   Nel pannello delle risorse di Azure sono visualizzate le assegnazioni dei ruoli per l'utente e la sottoscrizione selezionati. L'elenco include solo le assegnazioni di ruoli per le risorse per le quali si ha l'autorizzazione di lettura. Ad esempio, se l'utente ha anche assegnazioni di ruoli a cui non si ha accesso in lettura, tali assegnazioni non verranno visualizzate nell'elenco.

1. Se si hanno più sottoscrizioni, è possibile scegliere l'elenco a discesa **Sottoscrizione** per visualizzare le assegnazioni di ruoli in un'altra sottoscrizione.

### <a name="list-role-assignments-for-a-resource-group"></a>Elencare le assegnazioni di ruolo per un gruppo di risorse

1. Nell'elenco di spostamento scegliere **Gruppi di risorse**.

1. Scegliere un gruppo di risorse, quindi scegliere **Controllo di accesso (IAM)**.

   Nel pannello Controllo di accesso (IAM), usato per la gestione delle identità e degli accessi, è possibile vedere chi ha accesso a questo gruppo di risorse. Si noterà che l'ambito di alcuni ruoli è **Questa risorsa**, mentre quello di altri è **(Ereditato)** da un altro ambito. L'accesso viene assegnato in modo specifico al gruppo di risorse oppure ereditato da un'assegnazione nella sottoscrizione padre.

   ![Gruppi di risorse](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-subscription"></a>Elencare i ruoli assegnati a una sottoscrizione

1. Nel portale di Azure scegliere **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Scegliere la propria sottoscrizione.

1. Scegliere **Controllo di accesso (IAM)**.

    Nel pannello Controllo di accesso (IAM) è possibile vedere chi ha accesso a questa sottoscrizione e il ruolo corrispondente.

    ![Pannello Controllo di accesso (IAM) per una sottoscrizione](./media/role-assignments-portal/subscription-access-control.png)

    I tipici utenti con i ruoli di Amministratore di sottoscrizione e i Coamministratori sono considerati proprietari della sottoscrizione nel modello RBAC.

### <a name="list-role-assignments-for-a-management-group"></a>Elencare le assegnazioni di ruolo per un gruppo di gestione

1. Nel portale di Azure scegliere **Tutti i servizi** e quindi **Gruppi di gestione**.

1. Scegliere un gruppo di gestione.

1. Scegli **(dettagli)** per il gruppo di gestione selezionato.

    ![Gruppi di gestione](./media/role-assignments-portal/management-groups-list.png)

1. Scegliere **Controllo di accesso (IAM)**.

    Nel pannello Controllo di accesso (IAM) è possibile vedere chi ha accesso a questo gruppo di gestione e il ruolo corrispondente.

    ![Pannello Controllo di accesso (IAM) per un gruppo di gestione](./media/role-assignments-portal/management-groups-access-control.png)

## <a name="grant-access"></a>Concedere l'accesso

Per concedere l'accesso in RBAC, si crea un'assegnazione di ruolo. Eseguire questa procedura per concedere l'accesso ad ambiti diversi.

### <a name="assign-a-role-at-a-resource-group-scope"></a>Assegnare un ruolo nell'ambito di un gruppo di risorse

1. Nell'elenco di spostamento scegliere **Gruppi di risorse**.

1. Scegliere un gruppo di risorse.

1. Scegliere **Controllo di accesso (IAM)** per visualizzare l'elenco corrente delle assegnazioni di ruoli per l'ambito del gruppo di risorse.

   ![Pannello Controllo di accesso (IAM) per un gruppo di risorse](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. Scegliere **Aggiungi** per aprire il riquadro **Aggiungi autorizzazioni**.

   Se non si hanno le autorizzazioni per assegnare i ruoli, l'opzione **Aggiungi** non verrà visualizzata.

   ![Riquadro Aggiungi autorizzazioni](./media/role-assignments-portal/add-permissions.png)

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore Macchina virtuale**.

1. Nell'elenco **Selezione** selezionare un utente, un gruppo o un'applicazione. Se l'entità di sicurezza non è visualizzata nell'elenco, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati, gli indirizzi e-mail e gli identificatori di oggetto.

1. Scegliere **Salva** per assegnare un ruolo.

   Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito del gruppo di risorse.

### <a name="assign-a-role-at-a-subscription-scope"></a>Assegnare un ruolo nell’ambito della sottoscrizione

1. Nel portale di Azure scegliere **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Scegliere la propria sottoscrizione.

1. Scegliere **Controllo di accesso (IAM)** per visualizzare l'elenco corrente delle assegnazioni di ruoli per l'ambito della sottoscrizione.

   ![Pannello Controllo di accesso (IAM) per una sottoscrizione](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Scegliere **Aggiungi** per aprire il riquadro **Aggiungi autorizzazioni**.

   Se non si hanno le autorizzazioni per assegnare i ruoli, l'opzione **Aggiungi** non verrà visualizzata.

   ![Riquadro Aggiungi autorizzazioni](./media/role-assignments-portal/add-permissions.png)

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore Macchina virtuale**.

1. Nell'elenco **Selezione** selezionare un utente, un gruppo o un'applicazione. Se l'entità di sicurezza non è visualizzata nell'elenco, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati, gli indirizzi e-mail e gli identificatori di oggetto.

1. Scegliere **Salva** per assegnare un ruolo.

   Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito della sottoscrizione.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Assegnare un utente come amministratore di una sottoscrizione

Per rendere un utente amministratore per una sottoscrizione di Azure, assegnargli il ruolo di [Proprietario](built-in-roles.md#owner) nell'ambito della sottoscrizione. Questo ruolo garantisce all'utente l'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. Questi passaggi sono gli stessi di qualsiasi altra assegnazione di ruoli.

1. Nel portale di Azure scegliere **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Scegliere la propria sottoscrizione.

1. Scegliere **Controllo di accesso (IAM)** per visualizzare l'elenco corrente delle assegnazioni di ruoli per l'ambito della sottoscrizione.

   ![Pannello Controllo di accesso (IAM) per una sottoscrizione](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Scegliere **Aggiungi** per aprire il riquadro **Aggiungi autorizzazioni**.

   Se non si hanno le autorizzazioni per assegnare i ruoli, l'opzione **Aggiungi** non verrà visualizzata.

   ![Riquadro Aggiungi autorizzazioni](./media/role-assignments-portal/add-permissions.png)

1. Nell’elenco a discesa **Ruolo**, selezionare il ruolo **Proprietario.**

1. Nell'elenco **Selezione**, selezionare un utente. Se nell'elenco l'utente non è visualizzato, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati e gli indirizzi e-mail.

1. Scegliere **Salva** per assegnare un ruolo.

   Dopo qualche istante, all’utente viene assegnato il ruolo di Proprietario per l'ambito della sottoscrizione.

### <a name="assign-a-role-at-a-management-group-scope"></a>Assegnare un ruolo per l'ambito di un gruppo di gestione

1. Nel portale di Azure scegliere **Tutti i servizi** e quindi **Gruppi di gestione**.

1. Scegliere un gruppo di gestione.

1. Scegli **(dettagli)** per il gruppo di gestione selezionato.

    ![Gruppi di gestione](./media/role-assignments-portal/management-groups-list.png)

1. Scegliere **Controllo di accesso (IAM)** per visualizzare l'elenco corrente delle assegnazioni di ruoli per l'ambito della sottoscrizione.

   ![Pannello Controllo di accesso (IAM) per un gruppo di gestione](./media/role-assignments-portal/grant-management-groups-access-control.png)

1. Scegliere **Aggiungi** per aprire il riquadro **Aggiungi autorizzazioni**.

   Se non si hanno le autorizzazioni per assegnare i ruoli, l'opzione **Aggiungi** non verrà visualizzata.

   ![Riquadro Aggiungi autorizzazioni](./media/role-assignments-portal/add-permissions-management-groups.png)

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore gruppo di gestione**.

    Per informazioni sulle azioni supportate nei gruppi di gestione per i vari ruoli, vedere [Organizzare le risorse con i gruppi di gestione di Azure](../governance/management-groups/index.md#management-group-access).

1. Nell'elenco **Selezione** selezionare un utente, un gruppo o un'applicazione. Se l'entità di sicurezza non è visualizzata nell'elenco, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati, gli indirizzi e-mail e gli identificatori di oggetto.

1. Scegliere **Salva** per assegnare un ruolo.

   Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito del gruppo di gestione.

## <a name="remove-access"></a>Rimuovere un accesso

Per rimuovere un accesso mediante il controllo degli accessi in base al ruolo, si rimuove un'assegnazione di ruolo. Eseguire la procedura seguente per rimuovere l'accesso.

### <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

1. Aprire il pannello **Controllo di accesso (IAM)** per il gruppo di gestione, la sottoscrizione, il gruppo di risorse o la risorsa con l'assegnazione di ruolo che si vuole rimuovere.

1. Nell'elenco delle assegnazioni di ruolo aggiungere un segno di spunta accanto all'entità di sicurezza con l'assegnazione di ruolo che si vuole rimuovere.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Scegliere **Rimuovi**.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment.png)

1. Nella finestra con il messaggio di rimozione dell'assegnazione di ruolo scegliere **Sì**.

    Le assegnazioni di ruolo ereditate non possono essere rimosse. Per rimuovere un'assegnazione di ruolo ereditata, è necessario eseguire questa operazione nell'ambito di creazione dell'assegnazione di ruolo. Nella colonna **Ambito**, accanto a **(ereditato)** è presente un collegamento che consente di passare all'ambito in cui è stato assegnato questo ruolo. Passare all'ambito indicato nell'elenco per rimuovere l'assegnazione di ruolo.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Passaggi successivi

* [Quickstart: Grant access for a user using RBAC and the Azure portal](quickstart-assign-role-user-portal.md) (Guida introduttiva: Concedere l'accesso a un utente tramite il controllo degli accessi in base al ruolo e il portale di Azure)
* [Esercitazione: Concedere l'accesso a un utente tramite il controllo degli accessi in base al ruolo e Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Ruoli predefiniti](built-in-roles.md)
* [Organizzare le risorse con i gruppi di gestione di Azure ](../azure-resource-manager/management-groups-overview.md)
