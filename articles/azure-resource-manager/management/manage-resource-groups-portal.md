---
title: Gestire i gruppi di risorse - Portale di AzureManage resource groups - Azure portal
description: Usare il portale di Azure per gestire i gruppi di risorse tramite Azure Resource Manager.Use Azure portal to manage your resource groups through Azure Resource Manager. Viene illustrato come creare, elencare ed eliminare gruppi di risorse.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 622c0cb8d58dddcdb13c7dc9412ada3f2e2d93e4
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632981"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Gestire i gruppi di risorse di Azure Resource Manager tramite il portale di AzureManage Azure Resource Manager resource groups by using the Azure portal

Informazioni su come usare il [portale di Azure](https://portal.azure.com) con Azure Resource Manager per gestire i gruppi di risorse di Azure.Learn how to use the Azure portal with [Azure Resource Manager](overview.md) to manage your Azure resource groups. Per la gestione delle risorse di Azure, vedere Gestire le risorse di Azure tramite il portale di Azure.For managing Azure resources, see [Manage Azure resources by using the Azure portal.](manage-resources-portal.md)

Altri articoli sulla gestione dei gruppi di risorse:Other articles about managing resource groups:

- [Gestire i gruppi di risorse di Azure usando l'interfaccia della riga di comando di AzureManage Azure resource groups by using Azure CLI](manage-resources-cli.md)
- [Gestire i gruppi di risorse di Azure tramite Azure PowerShellManage Azure resource groups by using Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Che cos'è un gruppo di risorseWhat is a resource group

Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione. È in genere consigliabile aggiungere risorse che condividono lo stesso ciclo di vita allo stesso gruppo di risorse per poterle distribuire, aggiornare ed eliminare facilmente come gruppo.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica il percorso di archiviazione dei metadati. Per motivi di conformità potrebbe essere necessario assicurarsi che i dati siano archiviati in una determinata area.


## <a name="create-resource-groups"></a>Creare gruppi di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Seleziona **gruppi di risorse**

    ![aggiungere un gruppo di risorse](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selezionare **Aggiungi**.
4. Immettere i valori seguenti:

   - **Sottoscrizione:** selezionare la sottoscrizione di Azure.Subscription : Select your Azure subscription. 
   - **Gruppo di risorse**: Immettere un nuovo nome del gruppo di risorse. 
   - **Area**: selezionare una posizione di Azure, ad esempio **Stati Uniti centrali**.

     ![Creare un gruppo di risorse](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Selezionare **Revisione e creazione**
6. Selezionare **Create** (Crea). La creazione di un gruppo di risorse richiede alcuni secondi.
7. Selezionare **Aggiorna** dal menu in alto per aggiornare l'elenco dei gruppi di risorse, quindi selezionare il gruppo di risorse appena creato per aprirlo. In alternativa, selezionare **Notifica**(l'icona a campana) dall'alto e quindi selezionare **Vai al gruppo** di risorse per aprire il gruppo di risorse appena creato

    ![passare al gruppo di risorse](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Elencare i gruppi di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Per elencare i gruppi di risorse, selezionare **Gruppi di risorseTo** list the resource groups, select Resource groups

    ![esplorare i gruppi di risorse](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Per personalizzare le informazioni visualizzate per i gruppi di risorse, selezionare **Modifica colonne**. La schermata seguente mostra le colonne di addizione che è possibile aggiungere alla visualizzazione:The following screenshot shows the addition columns you could add to the display:

## <a name="open-resource-groups"></a>Aprire i gruppi di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Gruppi di risorse**.
3. Selezionare il gruppo di risorse da aprire.

## <a name="delete-resource-groups"></a>Eliminare i gruppi di risorse

1. Aprire il gruppo di risorse da eliminare.  Vedere [Aprire gruppi di risorse](#open-resource-groups).
2. Selezionare **Elimina gruppo di risorse**.

    ![eliminare il gruppo di risorse di azuredelete azure resource group](./media/manage-resource-groups-portal/delete-group.png)

Per altre informazioni su come Azure Resource Manager ordina l'eliminazione delle risorse, vedere Eliminazione del gruppo di risorse di Azure Resource Manager.For more information about how Azure Resource Manager orders the deletion of resources, see [Azure Resource Manager resource group deletion.](delete-resource-group.md)

## <a name="deploy-resources-to-a-resource-group"></a>Distribuire risorse in un gruppo di risorseDeploy resources to a resource group

Dopo aver creato un modello di Resource Manager, è possibile usare il portale di Azure per distribuire le risorse di Azure.After you have created a Resource Manager template, you can use the Azure portal to deploy your Azure resources. Per la creazione di un modello, vedere Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager tramite il portale di Azure.For creating a template, see [Quickstart: Create and deploy Azure Resource Manager templates by using the Azure portal](../templates/quickstart-create-templates-use-the-portal.md). Per la distribuzione di un modello tramite il portale, vedere Distribuire risorse con i modelli di Resource Manager e il portale di [Azure.](../templates/deploy-portal.md)

## <a name="move-to-another-resource-group-or-subscription"></a>Passare a un altro gruppo di risorse o sottoscrizione

È possibile spostare le risorse del gruppo in un altro gruppo di risorse. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Bloccare i gruppi di risorse

Il blocco impedisce ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente risorse critiche, ad esempio una sottoscrizione di Azure, un gruppo di risorse o una risorsa. 

1. Aprire il gruppo di risorse da eliminare.  Vedere [Aprire gruppi di risorse](#open-resource-groups).
2. Nel riquadro sinistro selezionare **Blocchi**.
3. Per aggiungere un blocco al gruppo di risorse, selezionare **Aggiungi**.
4. Immettere **Blocca nome**, **Tipo di blocco**e **Note**. I tipi di blocco includono **Sola lettura**ed **Elimina**.

    ![blocco del gruppo di risorse azure](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Per ulteriori informazioni, vedere [Bloccare le risorse per impedire modifiche impreviste.](lock-resources.md)

## <a name="tag-resource-groups"></a>Contrassegnare i gruppi di risorse

È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni, vedere Uso dei tag per organizzare le risorse di Azure.For information, see [Using tags to organize your Azure resources](tag-resources.md#portal).

## <a name="export-resource-groups-to-templates"></a>Esportare gruppi di risorse in modelliExport resource groups to templates

Per informazioni sull'esportazione di modelli, vedere Esportazione di [risorse singole e multi-risorsa](../templates/export-template-portal.md)in template - Portal .

## <a name="manage-access-to-resource-groups"></a>Gestire l'accesso ai gruppi di risorseManage access to resource groups

Il controllo degli accessi in base al ruolo è il modo in cui si gestisce l'accesso alle risorse in [Azure.Role-based access control (RBAC)](../../role-based-access-control/overview.md) is the way that you manage access to resources in Azure. Per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi

- Per apprendere Azure Resource Manager, vedere Panoramica di Azure Resource Manager.To learn Azure [Resource Manager,](overview.md)see Azure Resource Manager overview .
- Per informazioni sulla sintassi dei modelli di Resource Manager, vedere Informazioni sulla struttura e la [sintassi dei modelli di Azure Resource Manager.](../templates/template-syntax.md)
- Per informazioni su come sviluppare modelli, consultate le [esercitazioni dettagliate.](/azure/azure-resource-manager/)
- Per visualizzare gli schemi di modello di Azure Resource Manager, vedere [riferimento al modello](/azure/templates/).
