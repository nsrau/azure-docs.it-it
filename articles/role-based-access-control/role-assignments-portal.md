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
ms.date: 06/13/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6544503353f085a9dd9b0fe7207bbf8d4e0bc9d0
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435618"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure

[Il controllo degli accessi in base al ruolo](overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Questo articolo descrive come gestire l'accesso per utenti, gruppi e applicazioni tramite il controllo degli accessi in base al ruolo e il portale di Azure.

## <a name="list-roles"></a>Elenco dei ruoli

Una definizione di ruolo è una raccolta di autorizzazioni usata per l'assegnazione dei ruoli. Azure offre oltre 60 [ruoli predefiniti](built-in-roles.md).

1. Nel portale di Azure scegliere **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Scegliere la propria sottoscrizione.

1. Scegliere **Controllo di accesso (IAM)**.

   ![Opzione Ruoli](./media/role-assignments-portal/list-subscription-access-control.png)

1. Scegliere **Ruoli** per visualizzare l'elenco di tutti i ruoli predefiniti e personalizzati.

   ![Opzione Ruoli](./media/role-assignments-portal/roles-option.png)

   È possibile visualizzare il numero di utenti e gruppi assegnati a ogni ruolo.

   ![Elenco dei ruoli](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>Elencare l'accesso

Quando si gestisce l'accesso, si ha l'esigenza di sapere chi ha accesso, quali sono le sue autorizzazioni e il relativo livello. Per visualizzare le informazioni sull'accesso, elencare le assegnazioni dei ruoli.

### <a name="list-role-assignments-for-a-subscription"></a>Elencare i ruoli assegnati a una sottoscrizione

1. Nel portale di Azure scegliere **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Scegliere la propria sottoscrizione.

1. Scegliere **Controllo di accesso (IAM)**.

    Nel pannello Controllo di accesso (IAM), usato per la gestione delle identità e degli accessi, è possibile vedere chi ha accesso alla sottoscrizione e il relativo ruolo.

    ![Pannello Controllo di accesso (IAM)](./media/role-assignments-portal/subscription-access-control.png)

    Gli utenti con i ruoli Amministratore sottoscrizione classico e Coamministratore sono considerati proprietari della sottoscrizione nel modello di controllo degli accessi in base al ruolo.


### <a name="list-role-assignments-for-a-resource-group"></a>Elencare le assegnazioni di ruolo per un gruppo di risorse

1. Nell'elenco di spostamento scegliere **Gruppi di risorse**.

1. Scegliere un gruppo di risorse, quindi scegliere **Controllo di accesso (IAM)**.

   Nel pannello Controllo di accesso (IAM) è possibile vedere chi ha accesso al gruppo di risorse. Si noterà che l'ambito di alcuni ruoli è **Questa risorsa**, mentre quello di altri è **(Ereditato)** da un altro ambito. L'accesso viene assegnato in modo specifico al gruppo di risorse oppure ereditato da un'assegnazione nella sottoscrizione padre.

   ![Gruppi di risorse](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-user"></a>Elencare i ruoli assegnati a un utente

1. Nell'elenco di spostamento scegliere **Azure Active Directory**.

1. Scegliere **Utenti** e aprire **Tutti gli utenti**.

   ![Pannello Tutti gli utenti di Azure Active Directory](./media/role-assignments-portal/aad-all-users.png)

1. Scegliere un singolo utente nell'elenco.

1. Nella sezione **Gestisci** scegliere **Risorse di Azure**.

   ![Risorse di Azure dell'utente di Azure Active Directory](./media/role-assignments-portal/aad-user-azure-resources.png)

   Nel pannello delle risorse di Azure sono visualizzati i ruoli assegnati all'utente selezionato. L'elenco include solo le assegnazioni di ruoli per le risorse per le quali si ha l'autorizzazione di lettura. Ad esempio, se l'utente ha anche assegnazioni di ruoli in un'altra sottoscrizione alla quale non si ha accesso in lettura, tali assegnazioni non verranno visualizzate nell'elenco.

## <a name="grant-access"></a>Concedere l'accesso

Per concedere l'accesso mediante il controllo degli accessi in base al ruolo, si crea un'assegnazione di ruolo.

### <a name="create-a-role-assignment-at-a-subscription-scope"></a>Creare un'assegnazione di ruolo per l'ambito di una sottoscrizione

1. Nel portale di Azure scegliere **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Scegliere la propria sottoscrizione.

1. Scegliere **Controllo di accesso (IAM)** per visualizzare l'elenco corrente delle assegnazioni di ruoli per l'ambito della sottoscrizione.

   ![Pannello Controllo di accesso (IAM) per il gruppo di risorse](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Scegliere **Aggiungi** per aprire il riquadro **Aggiungi autorizzazioni**.

   Se non si hanno le autorizzazioni per assegnare i ruoli, l'opzione **Aggiungi** non verrà visualizzata.

   ![Riquadro Aggiungi autorizzazioni](./media/role-assignments-portal/add-permissions.png)

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore Macchina virtuale**.

1. Nell'elenco **Selezione** selezionare un utente, un gruppo o un'applicazione. Se l'entità di sicurezza non è visualizzata nell'elenco, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati, gli indirizzi e-mail e gli identificatori di oggetto.

1. Scegliere **Salva** per creare l'assegnazione di ruolo.

   Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito della sottoscrizione.

### <a name="create-a-role-assignment-at-a-resource-group-scope"></a>Creare un'assegnazione di ruolo per l'ambito di un gruppo di risorse

1. Nell'elenco di spostamento scegliere **Gruppi di risorse**.

1. Scegliere un gruppo di risorse.

1. Scegliere **Controllo di accesso (IAM)** per visualizzare l'elenco corrente delle assegnazioni di ruoli per l'ambito del gruppo di risorse.

   ![Pannello Controllo di accesso (IAM) per il gruppo di risorse](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. Scegliere **Aggiungi** per aprire il riquadro **Aggiungi autorizzazioni**.

   Se non si hanno le autorizzazioni per assegnare i ruoli, l'opzione **Aggiungi** non verrà visualizzata.

   ![Riquadro Aggiungi autorizzazioni](./media/role-assignments-portal/add-permissions.png)

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore Macchina virtuale**.

1. Nell'elenco **Selezione** selezionare un utente, un gruppo o un'applicazione. Se l'entità di sicurezza non è visualizzata nell'elenco, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati, gli indirizzi e-mail e gli identificatori di oggetto.

1. Scegliere **Salva** per creare l'assegnazione di ruolo.

   Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito del gruppo di risorse.

## <a name="remove-access"></a>Rimuovere un accesso

Per rimuovere un accesso mediante il controllo degli accessi in base al ruolo, si rimuove un'assegnazione di ruolo.

### <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

1. Aprire il pannello **Controllo di accesso (IAM)** per la sottoscrizione, il gruppo di risorse o la risorsa con l'assegnazione di ruolo che si vuole rimuovere.

1. Nell'elenco delle assegnazioni di ruolo aggiungere un segno di spunta accanto all'entità di sicurezza con l'assegnazione di ruolo che si vuole rimuovere.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Scegliere **Rimuovi**.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment.png)

1. Nella finestra con il messaggio di rimozione dell'assegnazione di ruolo scegliere **Sì**.

Le assegnazioni di ruolo ereditate non possono essere rimosse. Per rimuovere un'assegnazione di ruolo ereditata, è necessario eseguire questa operazione nell'ambito di creazione dell'assegnazione di ruolo. Nella colonna **Ambito**, accanto a **Ereditato** è presente un collegamento che consente di visualizzare le risorse in cui è stato assegnato questo ruolo. Passare all'ambito indicato nell'elenco per rimuovere l'assegnazione di ruolo.

## <a name="other-tools-to-manage-access"></a>Altri strumenti per gestire l'accesso

È possibile assegnare i ruoli e gestire l'accesso con i comandi del Controllo degli accessi in base al ruolo di Azure in strumenti diversi dal portale di Azure. Per ulteriori informazioni, vedere i collegamenti seguenti:

* [Azure PowerShell](role-assignments-powershell.md)
* [Interfaccia della riga di comando di Azure](role-assignments-cli.md)
* [API REST](role-assignments-rest.md)

## <a name="next-steps"></a>Passaggi successivi

* [Quickstart: Grant access for a user using RBAC and the Azure portal](quickstart-assign-role-user-portal.md) (Guida introduttiva: Concedere l'accesso a un utente tramite il controllo degli accessi in base al ruolo e il portale di Azure)
* [Esercitazione: Concedere l'accesso a un utente tramite il controllo degli accessi in base al ruolo e Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Ruoli predefiniti](built-in-roles.md)
