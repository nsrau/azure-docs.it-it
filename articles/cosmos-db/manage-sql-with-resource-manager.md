---
title: Creare e gestire Azure Cosmos DB usando i modelli di Azure Resource Manager
description: Usare i modelli di Azure Resource Manager per creare e configurare Azure Cosmos DB SQL API (Core)
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: a3798ac0c73c7bc6c4012dbb089275254f4c3504
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968845"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Gestire le risorse API SQL di Azure Cosmos DB (Core) con modelli di Resource Manager di Azure

## Creare un account, database e un contenitore di Azure Cosmos <a id="create-resource"></a>

Creare risorse di Azure Cosmos DB usando un modello di Azure Resource Manager. Questo modello creerà un account Azure Cosmos con due contenitori che condividono 400 UR/s di velocità effettiva a livello di database. Copiare il modello e distribuire come illustrato di seguito o visita [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) e la distribuzione dal portale di Azure. È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Distribuire tramite PowerShell

Per distribuire il modello di Resource Manager usando PowerShell **copia** lo script e selezionare **Provalo** per aprire Azure Cloud shell. Per incollare lo script, la shell e quindi scegliere **incollare**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$container1Name = Read-Host -Prompt "Enter the first container name"
$container2Name = Read-Host -Prompt "Enter the second container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -container1Name $container1Name `
    -container2Name $container2Name

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Se si sceglie di usare una versione installata in locale di PowerShell invece che da Azure Cloud shell, si dovrà [installare](/powershell/azure/install-az-ps) il modulo Azure PowerShell. Eseguire `Get-Module -ListAvailable Az` per trovare la versione.

### <a name="deploy-via-azure-cli"></a>Distribuire tramite il comando di Azure

Per distribuire il modello di Resource Manager tramite la CLI di Azure, selezionare **Provalo** per aprire Azure Cloud shell. Per incollare lo script, la shell e quindi scegliere **incollare**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first container name: ' container1Name
read -p 'Enter the second container name: ' container2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   container1Name=$container1Name container2Name=$container2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Il `az cosmosdb show` comando Mostra l'account Azure Cosmos appena creato dopo averne effettuato. Se si sceglie di usare una versione installata in locale della riga di comando di Azure invece di usare cloud shell, vedere [interfaccia della riga di comando di Azure](/cli/azure/) articolo.

## Aggiornamento della velocità effettiva (UR/sec) in un database <a id="database-ru-update"></a>

Il modello seguente aggiornerà la velocità effettiva di un database. Copiare il modello e distribuire come illustrato di seguito o visita [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-database-ru-update/) e la distribuzione dal portale di Azure. È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.

[!code-json[cosmosdb-sql-database-ru-update](~/quickstart-templates/101-cosmosdb-sql-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-powershell"></a>Distribuire il modello di database tramite PowerShell

Per distribuire il modello di Resource Manager usando PowerShell **copia** lo script e selezionare **Provalo** per aprire Azure Cloud shell. Per incollare lo script, la shell e quindi scegliere **incollare**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$throughput = Read-Host -Prompt "Enter new throughput for database"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -throughput $throughput
```

### <a name="deploy-database-template-via-azure-cli"></a>Distribuire il modello di database tramite la CLI di Azure

Per distribuire il modello di Resource Manager tramite la CLI di Azure, selezionare **Provalo** per aprire Azure Cloud shell. Per incollare lo script, la shell e quindi scegliere **incollare**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Aggiornamento della velocità effettiva (UR/sec) in un contenitore <a id="container-ru-update"></a>

Il modello seguente aggiornerà la velocità effettiva di un contenitore. Copiare il modello e distribuire come illustrato di seguito o visita [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-ru-update/) e la distribuzione dal portale di Azure. È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.

[!code-json[cosmosdb-sql-container-ru-update](~/quickstart-templates/101-cosmosdb-sql-container-ru-update/azuredeploy.json)]

### <a name="deploy-container-template-via-powershell"></a>Distribuire il modello di contenitori tramite PowerShell

Per distribuire il modello di Resource Manager usando PowerShell **copia** lo script e selezionare **Provalo** per aprire Azure Cloud shell. Per incollare lo script, la shell e quindi scegliere **incollare**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"
$throughput = Read-Host -Prompt "Enter new throughput for container"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -containerName $containerName `
    -throughput $throughput
```

### <a name="deploy-container-template-via-azure-cli"></a>Distribuire il modello di contenitori tramite la CLI di Azure

Per distribuire il modello di Resource Manager tramite la CLI di Azure, selezionare **Provalo** per aprire Azure Cloud shell. Per incollare lo script, la shell e quindi scegliere **incollare**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName containerName=$containerName throughput=$throughput
```

## <a name="next-steps"></a>Fasi successive

Altre risorse:

- [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
- [Schema di provider di risorse di Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelli di Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Risolvere errori comuni durante la distribuzione Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)