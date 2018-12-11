---
title: "Esercitazione: Concedere l'accesso a un utente usando il controllo degli accessi in base al ruolo e il portale di Azure | Microsoft Docs"
description: Usare il controllo di accesso in base al ruolo (RBAC) per concedere autorizzazioni a un utente tramite l'assegnazione di un ruolo nel portale di Azure.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.openlocfilehash: 8caa5c3b33ac1b483429251e0c1256636c4ece1a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634473"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-the-azure-portal"></a>Esercitazione: Concedere l'accesso a un utente usando il controllo degli accessi in base al ruolo e il portale di Azure

[Il controllo degli accessi in base al ruolo](overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Questa esercitazione illustra come concedere un accesso a un utente in modo che possa creare e gestire macchine virtuali in un gruppo di risorse.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Concedere l'accesso per un utente nell'ambito di un gruppo di risorse
> * Rimuovere un accesso

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

1. Nell'elenco di spostamento scegliere **Gruppi di risorse**.

1. Scegliere **Aggiungi** per aprire il pannello **Gruppo di risorse**.

   ![Aggiungere un nuovo gruppo di risorse](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Per il **nome del gruppo di risorse**, immettere **rbac-resource-group**.

1. Selezionare una sottoscrizione e una posizione.

1. Scegliere **Crea** per creare il gruppo di risorse.

1. Scegliere **Aggiorna** per aggiornare l'elenco dei gruppi di risorse.

   Il nuovo gruppo di risorse viene visualizzato nell'elenco dei gruppi di risorse.

   ![Elenco del gruppo di risorse](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Concedere l'accesso

Per concedere l'accesso mediante il controllo degli accessi in base al ruolo, si crea un'assegnazione di ruolo.

1. Nell'elenco **Gruppi di risorse** scegliere il nuovo gruppo di risorse **rbac-resource-group**.

1. Scegliere **Controllo di accesso (IAM)**.

1. Scegliere la scheda **Assegnazioni di ruolo** per visualizzare l'elenco corrente di assegnazioni di ruolo.

   ![Pannello Controllo di accesso (IAM) per il gruppo di risorse](./media/quickstart-assign-role-user-portal/access-control.png)

1. Scegliere **Aggiungi assegnazione di ruolo** per aprire il riquadro Aggiungi assegnazione di ruolo.

   Se non si dispone delle autorizzazioni per assegnare ruoli, l'opzione Aggiungi assegnazione di ruolo verrà disabilitata.

   ![Riquadro Aggiungi assegnazione di ruolo](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore macchina virtuale**.

1. Nell'elenco **Selezionare** selezionare sé stessi o un altro utente.

1. Scegliere **Salva** per creare l'assegnazione di ruolo.

   Dopo qualche istante, all'utente verrà assegnato il ruolo Collaboratore macchina virtuale per il gruppo di risorse rbac-resource-group.

   ![Assegnazione del ruolo Collaboratore macchina virtuale](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Rimuovere un accesso

Per rimuovere un accesso mediante il controllo degli accessi in base al ruolo, si rimuove un'assegnazione di ruolo.

1. Nell'elenco delle assegnazioni di ruolo, aggiungere un segno di spunta accanto all'utente con il ruolo di Collaboratore macchina virtuale.

1. Scegliere **Rimuovi**.

   ![Messaggio di rimozione assegnazione di ruolo](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Nella finestra con il messaggio di rimozione dell'assegnazione di ruolo scegliere **Sì**.

## <a name="clean-up"></a>Eseguire la pulizia

1. Nell'elenco di spostamento scegliere **Gruppi di risorse**.

1. Scegliere **rbac-resource-group** per aprire il gruppo di risorse.

1. Scegliere **Elimina gruppo di risorse** per eliminare il gruppo di risorse.

   ![Eliminare un gruppo di risorse](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. Nel pannello **Eliminare** digitare il nome del gruppo di risorse: **rbac-resource-group**.

1. Scegliere **Elimina** per eliminare il gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: concedere l'accesso a un utente tramite il controllo degli accessi in base al ruolo e PowerShell](tutorial-role-assignments-user-powershell.md)

