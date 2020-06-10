---
title: "Avvio rapido: Creare un'area di lavoro di Azure Databricks con il modello Azure Resource Manager"
description: Questo argomento di avvio rapido illustra come usare il modello di Azure Resource Manager per creare un'area di lavoro di Azure Databricks.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs
ms.date: 05/27/2020
ms.openlocfilehash: 8435704963e832020ecff27a11d00793ad28890c
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171039"
---
# <a name="quickstart-create-an-azure-databricks-workspace-by-using-the-azure-resource-manager-template"></a>Avvio rapido: Creare un'area di lavoro di Azure Databricks con il modello Azure Resource Manager

In questo argomento di avvio rapido si usa un modello di Azure Resource Manager per creare un'area di lavoro di Azure Databricks. Una volta creata l'area di lavoro, è possibile convalidare la distribuzione.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)

## <a name="create-an-azure-databricks-workspace"></a>Creare un'area di lavoro di Azure Databricks

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-databricks-workspace).

:::code language="json" source="~/quickstart-templates/101-databricks-workspace/azuredeploy.json" range="1-53":::

La risorsa di Azure definita nel modello è Microsoft.Databricks/workspaces: creare un'area di lavoro Azure Databricks. 

## <a name="deploy-the-template"></a>Distribuire il modello

In questa sezione viene creata un'area di lavoro di Azure Databricks usando il modello di Azure Resource Manager.

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un'area di lavoro di Azure Databricks.

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-databricks-workspace%2Fazuredeploy.json)

2. Specificare i valori richiesti per creare l'area di lavoro di Azure Databricks

   ![Creare un'area di lavoro di Azure Databricks con un modello di Azure Resource Manager](./media/quickstart-create-databricks-workspace-resource-manager-template/create-databricks-workspace-using-resource-manager-template.png "Creare un'area di lavoro di Azure Databricks con un modello di Azure Resource Manager")

   Specificare i valori seguenti:

   |Proprietà  |Descrizione  |
   |---------|---------|
   |**Sottoscrizione**     | Selezionare la sottoscrizione di Azure nell'elenco a discesa.        |
   |**Gruppo di risorse**     | Specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Per altre informazioni, vedere [Panoramica di Gestione risorse di Microsoft Azure](../azure-resource-manager/management/overview.md). |
   |**Posizione**     | Selezionare **Stati Uniti orientali 2**. Per le altre aree disponibili, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).        |
   |**Nome area di lavoro**     | Specificare un nome per l'area di lavoro di Databricks        |
   |**Piano tariffario**     |  Scegliere tra **Standard** e **Premium**. Per altre informazioni su questi piani tariffari, vedere la [pagina dei prezzi di Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Selezionare **Rivedi e crea** e quindi **Crea**.

4. La creazione dell'area di lavoro richiede alcuni minuti, Quando la distribuzione di un'area di lavoro non riesce, l'area di lavoro viene creata comunque anche se in uno stato di errore. Eliminare l'area di lavoro in errore e crearne una nuova per risolvere gli errori di distribuzione. Quando si elimina l'area di lavoro in errore, vengono eliminati anche il gruppo di risorse gestite e tutte le eventuali risorse distribuite correttamente.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

È possibile usare il portale di Azure per controllare l'area di lavoro di Azure Databricks oppure lo script dell'interfaccia della riga di comando di Azure o di Azure PowerShell seguente per elencare la risorsa.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
echo "Enter your Azure Databricks workspace name:" &&
read databricksWorkspaceName &&
echo "Enter the resource group where the Azure Databricks workspace exists:" &&
read resourcegroupName &&
az databricks workspace show -g $resourcegroupName -n $databricksWorkspaceName
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Databricks workspace exists"
(Get-AzResource -ResourceType "Microsoft.Databricks/workspaces" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di usare le esercitazioni successive, è consigliabile non cancellare le risorse create. Quando non è più necessario, eliminare il gruppo di risorse per eliminare l'area di lavoro di Azure Databricks e le risorse gestite correlate. Per eliminare il gruppo di risorse con l'interfaccia della riga di comando di Azure oppure con Azure PowerShell:

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata un'area di lavoro di Azure Databricks usando un modello di Azure Resource Manager ed è stata convalidata la distribuzione. Passare all'articolo successivo per informazioni su come eseguire un'operazione ETL (estrazione, trasformazione e caricamento dati) tramite Azure Databricks.

> [!div class="nextstepaction"]
> [Estrarre, trasformare e caricare dati tramite Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
