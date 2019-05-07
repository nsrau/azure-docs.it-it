---
title: Modelli di Azure Resource Manager per l'API Table di Azure Cosmos DB
description: Usare i modelli Azure Resource Manager per creare e configurare l'API Table di Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 33e47d67365e76142d5b584d49d8e7265445bf03
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077606"
---
# <a name="create-azure-cosmos-db-table-api-resources-from-a-resource-manager-template"></a>Creare le risorse API Table di Azure Cosmos DB da un modello di Resource Manager

Informazioni su come creare le risorse di un'API Table di Azure Cosmos DB usando un modello di Azure Resource Manager. L'esempio seguente crea un'API Table di Azure Cosmos DB da un' [modello di avvio rapido di Azure](https://aka.ms/table-arm-qs). Questo modello creerà un account Azure Cosmos per l'API di tabella con una tabella con una velocità effettiva di 400 UR/sec.

Di seguito è riportata una copia del modello:

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

## <a name="deploy-via-powershell"></a>Distribuire tramite PowerShell

Per distribuire il modello di Resource Manager usando PowerShell **copia** lo script e selezionare **Provalo** per aprire Azure Cloud shell. Per incollare lo script, la shell e quindi scegliere **incollare**:

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

Se si sceglie di usare una versione installata in locale di PowerShell invece che da Azure Cloud shell, si dovrà [installare](/powershell/azure/install-az-ps) il modulo Azure PowerShell. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. 

Nell'esempio precedente, si è fatto riferimento un modello archiviato in GitHub. È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.


## <a name="deploy-via-azure-cli"></a>Distribuire tramite il comando di Azure

Per distribuire il modello di Resource Manager tramite la CLI di Azure, **copia** lo script e selezionare **Provalo** per aprire Azure Cloud shell. Per incollare lo script, la shell e quindi scegliere **incollare**:

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

Il `az cosmosdb show` comando Mostra l'account Azure Cosmos appena creato dopo averne effettuato. Se si sceglie di usare una versione installata in locale della riga di comando di Azure invece di usare cloud shell, vedere [interfaccia della riga di comando di Azure](/cli/azure/) articolo.

Nell'esempio precedente, si è fatto riferimento un modello archiviato in GitHub. È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.


## <a name="next-steps"></a>Fasi successive

Altre risorse:

- [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
- [Schema di provider di risorse di Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelli di Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Risolvere errori comuni durante la distribuzione Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)