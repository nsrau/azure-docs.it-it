---
title: Gestire le risorse - Portale di AzureManage resources - Azure portal
description: Usare il portale di Azure e Azure Resource Manager per gestire le risorse. Viene illustrato come distribuire ed eliminare risorse.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 00d260a74807774d5bf226c3ec00a6b84f93b8d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248346"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Gestire le risorse di Azure tramite il portale di AzureManage Azure resources by using the Azure portal

Informazioni su come usare il [portale di Azure](https://portal.azure.com) con Azure Resource Manager per gestire le risorse di Azure.Learn how to use the Azure portal with [Azure Resource Manager](overview.md) to manage your Azure resources. Per la gestione dei gruppi di risorse, vedere Gestire i gruppi di risorse di Azure tramite il portale di Azure.For managing resource groups, see [Manage Azure resource groups by using the Azure portal.](manage-resource-groups-portal.md)

Altri articoli sulla gestione delle risorse:

- [Gestire le risorse di Azure usando l'interfaccia della riga di comando di AzureManage Azure resources by using Azure CLI](manage-resources-cli.md)
- [Gestire le risorse di Azure tramite Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Distribuire risorse in un gruppo di risorseDeploy resources to a resource group

Dopo aver creato un modello di Resource Manager, è possibile usare il portale di Azure per distribuire le risorse di Azure.After you have created a Resource Manager template, you can use the Azure portal to deploy your Azure resources. Per la creazione di un modello, vedere Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager tramite il portale di Azure.For creating a template, see [Quickstart: Create and deploy Azure Resource Manager templates by using the Azure portal](../templates/quickstart-create-templates-use-the-portal.md). Per la distribuzione di un modello tramite il portale, vedere Distribuire risorse con i modelli di Resource Manager e il portale di [Azure.](../templates/deploy-portal.md)

## <a name="open-resources"></a>Aprire le risorse

Le risorse di Azure sono organizzate per servizi di Azure e per gruppi di risorse. Nelle procedure seguenti viene illustrato come aprire un account di archiviazione denominato **mystorage0207**. La macchina virtuale risiede in un gruppo di risorse denominato **mystorage0207rg**.

Per aprire una risorsa in base al tipo di servizio:

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Nel riquadro sinistro selezionare il servizio Azure.In the left pane, select the Azure service. In questo caso, **Account di archiviazione**.  Se il servizio non è presente nell'elenco, selezionare **Tutti i servizi**e quindi selezionare il tipo di servizio.

    ![aprire la risorsa azure nel portale](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Selezionare la risorsa che si desidera aprire.

    ![aprire la risorsa azure nel portale](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Un account di archiviazione è simile al aspetto di un account di archiviazione:A storage account looks like:

    ![aprire la risorsa azure nel portale](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Per aprire una risorsa in base al gruppo di risorse:To open a resource by resource group:

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Nel riquadro sinistro selezionare **Gruppi di risorse** per elencare la risorsa all'interno del gruppo.
3. Selezionare la risorsa che si desidera aprire. 

## <a name="manage-resources"></a>Gestione di risorse

Quando si visualizza una risorsa nel portale, si vedono le opzioni per la gestione di quella particolare risorsa.

![gestire le risorse di AzureManage Azure resources](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

La schermata mostra le opzioni di gestione per una macchina virtuale di Azure.The screenshot shows the management options for an Azure virtual machine. È possibile eseguire operazioni quali l'avvio, il riavvio e l'arresto di una macchina virtuale.

## <a name="delete-resources"></a>Eliminare le risorse

1. Aprire la risorsa nel portale. Per la procedura, vedere [Risorse aperte](#open-resources).
2. Selezionare **Elimina**. La schermata seguente mostra le opzioni di gestione per una macchina virtuale.

    ![eliminare una risorsa azureDelete azure resource](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Digitare il nome della risorsa per confermare l'eliminazione e quindi selezionare **Elimina**.

Per altre informazioni su come Azure Resource Manager ordina l'eliminazione delle risorse, vedere Eliminazione del gruppo di risorse di Azure Resource Manager.For more information about how Azure Resource Manager orders the deletion of resources, see [Azure Resource Manager resource group deletion.](delete-resource-group.md)

## <a name="move-resources"></a>Spostare le risorse

1. Aprire la risorsa nel portale. Per la procedura, vedere [Risorse aperte](#open-resources).
2. Selezionare **Sposta**. La schermata seguente mostra le opzioni di gestione per un account di archiviazione.

    ![spostare la risorsa azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Selezionare **Sposta in un altro gruppo** di risorse o Sposta in **un'altra sottoscrizione** a seconda delle esigenze.

Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Bloccare le risorse

Il blocco impedisce ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente risorse critiche, ad esempio una sottoscrizione di Azure, un gruppo di risorse o una risorsa. 

1. Aprire la risorsa nel portale. Per la procedura, vedere [Risorse aperte](#open-resources).
2. Selezionare **Blocchi**. La schermata seguente mostra le opzioni di gestione per un account di archiviazione.

    ![bloccare la risorsa azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Selezionare **Aggiungi**, quindi specificare le proprietà di blocco.

Per altre informazioni, vedere [Bloccare le risorse con Azure Resource Manager.For](lock-resources.md)more information, see Lock resources with Azure Resource Manager.

## <a name="tag-resources"></a>Aggiungere tag alle risorse

L'assegnazione di tag consente di organizzare il gruppo di risorse e le risorse in modo logico. 

1. Aprire la risorsa nel portale. Per la procedura, vedere [Risorse aperte](#open-resources).
2. Selezionare **Tag**. La schermata seguente mostra le opzioni di gestione per un account di archiviazione.

    ![risorsa azure tag](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Specificare le proprietà del tag, quindi selezionare **Salva**.

Per informazioni, vedere Uso dei tag per organizzare le risorse di Azure.For information, see [Using tags to organize your Azure resources](tag-resources.md#portal).

## <a name="monitor-resources"></a>Monitorare le risorse

Quando si apre una risorsa, il portale presenta grafici e tabelle predefiniti per il monitoraggio di tale tipo di risorsa. La schermata seguente mostra i grafici per una macchina virtuale:The following screenshot shows the graphs for a virtual machine:

![monitorare la risorsa azureMonitor azure resource](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

È possibile selezionare l'icona a forma di puntina nell'angolo superiore destro dei grafici per aggiungere il grafico al dashboard. Per informazioni sull'uso dei dashboard, vedere [Creating and sharing dashboards in the Azure portal](../../azure-portal/azure-portal-dashboards.md)(Creazione e condivisione di dashboard nel portale di Azure).

## <a name="manage-access-to-resources"></a>Gestire l'accesso alle risorse

Il controllo degli accessi in base al ruolo è il modo in cui si gestisce l'accesso alle risorse in [Azure.Role-based access control (RBAC)](../../role-based-access-control/overview.md) is the way that you manage access to resources in Azure. Per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi

- Per apprendere Azure Resource Manager, vedere Panoramica di Azure Resource Manager.To learn Azure [Resource Manager,](overview.md)see Azure Resource Manager overview .
- Per informazioni sulla sintassi dei modelli di Resource Manager, vedere Informazioni sulla struttura e la [sintassi dei modelli di Azure Resource Manager.](../templates/template-syntax.md)
- Per informazioni su come sviluppare modelli, consultate le [esercitazioni dettagliate.](/azure/azure-resource-manager/)
- Per visualizzare gli schemi di modello di Azure Resource Manager, vedere [riferimento al modello](/azure/templates/).
