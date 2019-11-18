---
title: Gestisci gruppi di risorse-portale di Azure
description: Usare portale di Azure per gestire i gruppi di risorse tramite Azure Resource Manager. Viene illustrato come creare, elencare ed eliminare gruppi di risorse.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: e3402f496371fe89c832dc22c8288a1b94378c82
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147164"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Gestire i gruppi di risorse Azure Resource Manager usando il portale di Azure

Informazioni su come usare la [portale di Azure](https://portal.azure.com) con [Azure Resource Manager](resource-group-overview.md) per gestire i gruppi di risorse di Azure. Per la gestione delle risorse di Azure, vedere [gestire le risorse di Azure usando il portale di Azure](./manage-resources-portal.md).

Altri articoli sulla gestione dei gruppi di risorse:

- [Gestire i gruppi di risorse di Azure usando l'interfaccia della riga di comando](./manage-resources-cli.md)
- [Gestire i gruppi di risorse di Azure usando Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Che cos'è un gruppo di risorse

Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione. È in genere consigliabile aggiungere risorse che condividono lo stesso ciclo di vita allo stesso gruppo di risorse per poterle distribuire, aggiornare ed eliminare facilmente come gruppo.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica il percorso di archiviazione dei metadati. Per motivi di conformità potrebbe essere necessario assicurarsi che i dati siano archiviati in una determinata area.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica una posizione per il gruppo di risorse, si specifica dove vengono archiviati tali metadati.

## <a name="create-resource-groups"></a>Creare gruppi di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezione **gruppi di risorse**

    ![aggiungere un gruppo di risorse](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selezionare **Aggiungi**.
4. Immettere i valori seguenti:

   - **Sottoscrizione**: selezionare una sottoscrizione di Azure. 
   - **Gruppo di risorse**: immettere un nuovo nome per il gruppo di risorse. 
   - **Area**: selezionare una località di Azure, ad esempio **Stati Uniti centrali**.

     ![creare un gruppo di risorse](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Selezionare **Verifica + crea**
6. Selezionare **Create**. La creazione di un gruppo di risorse richiede alcuni secondi.
7. Selezionare **Aggiorna** dal menu superiore per aggiornare l'elenco dei gruppi di risorse e quindi selezionare il gruppo di risorse appena creato per aprirlo. In alternativa, selezionare **notifica**(icona a campana) nella parte superiore e quindi selezionare **Vai al gruppo di risorse** per aprire il gruppo di risorse appena creato

    ![Vai al gruppo di risorse](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Elencare i gruppi di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Per elencare i gruppi di risorse, selezionare **gruppi di risorse**

    ![esplorare i gruppi di risorse](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Per personalizzare le informazioni visualizzate per i gruppi di risorse, selezionare **modifica colonne**. Lo screenshot seguente mostra le colonne di aggiunta che è possibile aggiungere alla visualizzazione:

## <a name="open-resource-groups"></a>Apri gruppi di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Gruppi di risorse**.
3. Selezionare il gruppo di risorse che si vuole aprire.

## <a name="delete-resource-groups"></a>Elimina gruppi di risorse

1. Aprire il gruppo di risorse che si vuole eliminare.  Vedere [aprire gruppi di risorse](#open-resource-groups).
2. Selezionare **Elimina gruppo di risorse**.

    ![Elimina gruppo di risorse di Azure](./media/manage-resource-groups-portal/delete-group.png)

Per altre informazioni su come Azure Resource Manager Ordina l'eliminazione delle risorse, vedere [Azure Resource Manager eliminazione del gruppo di risorse](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Distribuire le risorse in un gruppo di risorse

Dopo aver creato un modello di Gestione risorse, è possibile usare il portale di Azure per distribuire le risorse di Azure. Per la creazione di un modello, vedere [Guida introduttiva: creare e distribuire modelli di Azure Resource Manager usando il portale di Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Per la distribuzione di un modello tramite il portale, vedere [distribuire risorse con modelli e portale di Azure di gestione risorse](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Passare a un altro gruppo di risorse o a una sottoscrizione

È possibile spostare le risorse del gruppo in un altro gruppo di risorse. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Blocca gruppi di risorse

Il blocco impedisce ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente le risorse critiche, ad esempio la sottoscrizione di Azure, il gruppo di risorse o la risorsa. 

1. Aprire il gruppo di risorse che si vuole eliminare.  Vedere [aprire gruppi di risorse](#open-resource-groups).
2. Nel riquadro sinistro selezionare **blocchi**.
3. Per aggiungere un blocco al gruppo di risorse, selezionare **Aggiungi**.
4. Immettere il **nome del blocco**, il tipo di **blocco**e le **Note**. I tipi di blocco sono di sola **lettura**ed **Elimina**.

    ![blocca il gruppo di risorse di Azure](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Per altre informazioni, vedere [bloccare le risorse per impedire modifiche impreviste](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Contrassegnare i gruppi di risorse

È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni, vedere [uso dei tag per organizzare le risorse di Azure](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Esportare gruppi di risorse nei modelli

Per informazioni sull'esportazione di modelli, vedere [esportazione a più risorse e a modello-portale](export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gestire l'accesso ai gruppi di risorse

[Il controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](./resource-group-overview.md).
- Per informazioni sulla sintassi del modello di Gestione risorse, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](./resource-group-authoring-templates.md).
- Per informazioni su come sviluppare modelli, vedere le [esercitazioni dettagliate](/azure/azure-resource-manager/).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere informazioni di [riferimento sui modelli](/azure/templates/).