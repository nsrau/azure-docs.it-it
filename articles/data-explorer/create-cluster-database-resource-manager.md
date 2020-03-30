---
title: Creare un cluster e un database di Azure Data Explorer usando un modello di Azure Resource ManagerCreate an Azure Data Explorer cluster and database by using an Azure Resource Manager template
description: Informazioni su come creare un cluster e un database di Azure Data Explorer usando un modello di Azure Resource Manager
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 56639d8a29ad8eac465845c8d354d04b31ba6093
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911954"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Creare un cluster e un database di Azure Data Explorer usando un modello di Azure Resource ManagerCreate an Azure Data Explorer cluster and database by using an Azure Resource Manager template

> [!div class="op_single_selector"]
> * [Portale](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modello di Azure Resource ManagerAzure Resource Manager template](create-cluster-database-resource-manager.md)

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in un database per poter eseguire query. 

In questo articolo si crea un cluster e un database di Azure Data Explorer usando un modello di [Azure Resource Manager.](../azure-resource-manager/management/overview.md) L'articolo descrive come definire le risorse da distribuire e i parametri specificati quando viene eseguita la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze. Per informazioni sulla creazione di modelli, vedere Creazione di modelli di [Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates). Per le proprietà e la sintassi JSON da usare in un modello, vedere [Tipi di risorsa Microsoft.Kusto](/azure/templates/microsoft.kusto/allversions).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Modello di Azure Resource Manager per la creazione di cluster e databaseAzure Resource Manager template for cluster and database creation

In questo articolo si usa un [modello di guida introduttiva esistenteIn](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json) this article, you use an existing quickstart template

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

## <a name="deploy-the-template-and-verify-template-deployment"></a>Distribuire il modello e verificare la distribuzione del modelloDeploy the template and verify template deployment

È possibile distribuire il modello di Azure Resource Manager usando il portale di Azure o Powershell.You can deploy the Azure Resource Manager template by [using the Azure portal](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) or using [powershell](#use-powershell-to-deploy-the-template-and-verify-template-deployment).

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Usare il portale di Azure per distribuire il modello e verificare la distribuzione del modelloUse the Azure portal to deploy the template and verify template deployment

1. Per creare un cluster e un database, utilizzare il pulsante seguente per avviare la distribuzione. Fare clic con il pulsante destro del mouse e selezionare **Apri in una nuova finestra** per poter seguire il resto dei passaggi di questo articolo.

    [![Distribuire in AzureDeploy to Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Il pulsante **Distribuzione in Azure** consente di passare al portale di Azure per compilare un modulo di distribuzione.

    ![Distribuisci in Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    È possibile [modificare e distribuire il modello nel portale](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) di Azure usando il modulo.

1. Sezioni **BASICS** e **SETTINGS** complete. Selezionare nomi univoci di cluster e database.
La creazione di un cluster e di un database di Azure Data Explorer richiede alcuni minuti.

1. Per verificare la distribuzione, aprire il gruppo di risorse nel portale di [Azure](https://portal.azure.com) per trovare il nuovo cluster e database. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Usare PowerShell per distribuire il modello e verificare la distribuzione del modelloUse powershell to deploy the template and verify template deployment

#### <a name="deploy-the-template-using-powershell"></a>Distribuire il modello usando PowerShellDeploy the template using powershell

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
La creazione di un cluster e di un database di Azure Data Explorer richiede alcuni minuti.

#### <a name="verify-the-deployment-using-powershell"></a>Verificare la distribuzione tramite PowerShellVerify the deployment using PowerShell

Per verificare la distribuzione, usare lo script di Azure PowerShell seguente.  Se Cloud Shell è ancora aperto, non è necessario copiare/eseguire la prima riga (Read-Host). Per altre informazioni sulla gestione delle risorse di Azure Data Explorer in PowerShell, vedere [Az.Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

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

[Inserire i dati nel cluster e nel database di Azure Data ExplorerIngest data into Azure Data Explorer cluster and database](ingest-data-overview.md)
