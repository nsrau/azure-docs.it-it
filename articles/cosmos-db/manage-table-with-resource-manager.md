---
title: Resource Manager templates for Azure Cosmos DB Table API
description: Usare i modelli di Azure Resource Manager per creare e configurare l'API tabella dati Cosmos di Azure.Use Azure Resource Manager templates to create and configure Azure Cosmos DB Table API.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 6ab54e56368e7e26e807e4d1dc0592536dc9374a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246708"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Gestire le risorse API della tabella del database di Azure Cosmos usando i modelli di Azure Resource ManagerManage Azure Cosmos DB Table API resources using Azure Resource Manager templates

Questo articolo descrive come eseguire diverse operazioni per automatizzare la gestione degli account, dei database e dei contenitori di Azure Cosmos usando i modelli di Azure Resource Manager.This article describes how to perform different operations to automate management of your Azure Cosmos DB accounts, databases and containers using Azure Resource Manager templates. Questo articolo contiene esempi solo per gli account API Table, per trovare esempi per altri account di tipo API, vedere: Usare i modelli di Azure Resource Manager con l'API di Azure Cosmos DB per [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [articoli SQL.](manage-sql-with-resource-manager.md)

## <a name="create-azure-cosmos-account-and-table"></a>Creare un account e una tabella di Azure CosmosCreate Azure Cosmos account and table<a id="create-resource"></a>

Creare risorse di Azure Cosmos DB usando un modello di Azure Resource Manager.Create Azure Cosmos DB resources using an Azure Resource Manager template. Questo modello creerà un account Azure Cosmos per l'API Table con una tabella con una velocità effettiva di 400 RU/s. Copiare il modello e distribuirlo come illustrato di seguito oppure visitare la raccolta di guide rapide di Azure e distribuirlo dal portale di Azure.Copy the template and deploy as shown below or visit [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) and deploy from the Azure portal. È inoltre possibile scaricare il modello nel computer locale o creare `--template-file` un nuovo modello e specificare il percorso locale con il parametro .

> [!NOTE]
> I nomi di account devono essere minuscoli e 44 caratteri o meno.
> Per aggiornare RU/s, inviare nuovamente il modello con i valori delle proprietà della velocità effettiva aggiornati.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

### <a name="deploy-via-powershell"></a>Distribuire tramite PowerShellDeploy via PowerShell

Per distribuire il modello di Resource Manager tramite PowerShell, **copiare** lo script e selezionare Prova per aprire Azure Cloud Shell.To deploy the Resource Manager template using PowerShell, Copy the script and select **Try it** to open Azure Cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sulla shell, quindi scegliere **Incolla:**

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Se si sceglie di usare una versione di PowerShell installata localmente anziché dalla shell del cloud di Azure, è necessario installare il modulo di Azure PowerShell.If you choose to use a locally installed version of PowerShell instead of the Azure Cloud shell, you have to [install](/powershell/azure/install-az-ps) the Azure PowerShell module. Eseguire `Get-Module -ListAvailable Az` per trovare la versione.

### <a name="deploy-via-the-azure-cli"></a>Distribuire tramite l'interfaccia della riga di comando di AzureDeploy via the Azure CLI

Per distribuire il modello di Azure Resource Manager usando l'interfaccia della riga di comando di Azure, **copiare** lo script e selezionare Prova per aprire Azure Cloud Shell.To deploy the Azure Resource Manager template using the Azure CLI, Copy the script and select **Try it** to open Azure Cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sulla shell, quindi scegliere **Incolla:**

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Il `az cosmosdb show` comando mostra l'account Azure Cosmos appena creato dopo il provisioning. Se si sceglie di usare una versione installata localmente dell'interfaccia della riga di comando di Azure anziché usare Cloud Shell, vedere l'articolo [dell'interfaccia della riga di comando](/cli/azure/) di Azure.If you choose to use a locally installed version of the Azure CLI instead of using Cloud Shell, see the Azure CLI article.

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

- [Documentazione di Azure Resource ManagerAzure Resource Manager documentation](/azure/azure-resource-manager/)
- [Schema del provider di risorse Azure Cosmos DBAzure Cosmos DB resource provider schema](/azure/templates/microsoft.documentdb/allversions)
- [Modelli di guida introduttiva di Azure Cosmos DBAzure Cosmos DB Quickstart templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Risolvere gli errori comuni di distribuzione di Azure Resource ManagerTroubleshoot common Azure Resource Manager deployment errors](../azure-resource-manager/templates/common-deployment-errors.md)