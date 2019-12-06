---
title: Creare un cluster e un database di Azure Esplora dati usando un modello di Azure Resource Manager
description: Informazioni su come creare un cluster e un database di Azure Esplora dati usando un modello di Azure Resource Manager
author: orspod
ms.author: orspodek
ms.reviewer: oflipman
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 4a95804bcff3653df42186907f03d0bc27a603ea
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74869715"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Creare un cluster e un database di Azure Esplora dati usando un modello di Azure Resource Manager

> [!div class="op_single_selector"]
> * [di Microsoft Azure](create-cluster-database-portal.md)
> * [Interfaccia della riga di comando](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modello di Azure Resource Manager](create-cluster-database-resource-manager.md)

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in un database per poter eseguire query. 

In questo articolo viene creato un cluster e un database di Azure Esplora dati usando un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). L'articolo descrive come definire le risorse da distribuire e i parametri specificati quando viene eseguita la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze. Per informazioni sulla creazione di modelli, vedere Creazione di [modelli di Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates). Per la sintassi e le proprietà JSON da usare in un modello, vedere [tipi di risorse Microsoft. kusto](/azure/templates/microsoft.kusto/allversions).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Modello di Azure Resource Manager per la creazione di cluster e database

In questo articolo viene usato un [modello di avvio rapido esistente](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

Per altri esempi di modello, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/).

## <a name="deploy-the-template-and-verify-template-deployment"></a>Distribuire il modello e verificare la distribuzione del modello

È possibile distribuire il modello di Azure Resource Manager [usando il portale di Azure](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) o [tramite PowerShell](#use-powershell-to-deploy-the-template-and-verify-template-deployment).

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Usare il portale di Azure per distribuire il modello e verificare la distribuzione del modello

1. Per creare un cluster e un database, usare il pulsante seguente per avviare la distribuzione. Fare clic con il pulsante destro del mouse e selezionare **Apri in una nuova finestra** per poter seguire il resto dei passaggi di questo articolo.

    [![Distribuisci in Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Il pulsante **Distribuzione in Azure** consente di passare al portale di Azure per compilare un modulo di distribuzione.

    ![Distribuire in Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    È possibile [modificare e distribuire il modello nel portale di Azure](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) usando il modulo.

1. Sezioni di **base** e **Impostazioni** complete. Selezionare i nomi di cluster e database univoci.
Sono necessari alcuni minuti per creare un cluster e un database di Azure Esplora dati.

1. Per verificare la distribuzione, aprire il gruppo di risorse nel [portale di Azure](https://portal.azure.com) per trovare il nuovo cluster e il nuovo database. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Usare PowerShell per distribuire il modello e verificare la distribuzione del modello

#### <a name="deploy-the-template-using-powershell"></a>Distribuire il modello tramite PowerShell

1. Selezionare **Prova** nel blocco di codice seguente e quindi seguire le istruzioni per accedere ad Azure Cloud Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Selezionare **Copia** per copiare lo script di PowerShell.
1. Fare clic con il pulsante destro del mouse sulla console della shell e quindi scegliere **Incolla**.
Sono necessari alcuni minuti per creare un cluster e un database di Azure Esplora dati.

#### <a name="verify-the-deployment-using-powershell"></a>Verificare la distribuzione tramite PowerShell

Per verificare la distribuzione, usare lo script di Azure PowerShell seguente.  Se il Cloud Shell è ancora aperto, non è necessario copiare/eseguire la prima riga (read-host). Per altre informazioni sulla gestione delle risorse di Azure Esplora dati in PowerShell, vedere [AZ. kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

[Inserire i dati nel cluster e nel database di Azure Esplora dati](ingest-data-overview.md)
