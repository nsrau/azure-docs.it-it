---
title: Modelli di Azure Resource Manager per Azure Cosmos DB API Tabella
description: Usare i modelli di Azure Resource Manager per creare e configurare API Tabella di Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: bd30f240700ea4dcf9c9ec20b8007a4445ae35e5
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960570"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Gestire Azure Cosmos DB API Tabella risorse tramite modelli Azure Resource Manager

Questo articolo descrive come eseguire diverse operazioni per automatizzare la gestione degli account Azure Cosmos DB, dei database e dei contenitori usando i modelli Azure Resource Manager. Questo articolo contiene esempi solo per gli account API Tabella. per trovare esempi per altri account di tipo API, vedere: usare Azure Resource Manager modelli con l'API Azure Cosmos DB per [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), articoli di [SQL](manage-sql-with-resource-manager.md) .

## Creare un account e una tabella di Azure Cosmos<a id="create-resource"></a>

Creare Azure Cosmos DB risorse usando un modello di Azure Resource Manager. Questo modello creerà un account Azure Cosmos per API Tabella con una tabella con una velocità effettiva di 400 ur/s. Copiare il modello e distribuirlo come illustrato di seguito oppure visitare la [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) e distribuire dal portale di Azure. È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il parametro `--template-file`.

> [!NOTE]
> I nomi degli account devono essere minuscoli e 44 o un numero inferiore di caratteri.
> Per aggiornare ur/s, inviare nuovamente il modello con i valori di proprietà della velocità effettiva aggiornati.

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Eseguire la distribuzione tramite PowerShell

Per distribuire il modello di Gestione risorse usando PowerShell, **copiare** lo script e selezionare **prova** per aprirlo Azure cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sulla shell, quindi scegliere **Incolla**:

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

Se si sceglie di usare una versione installata localmente di PowerShell anziché da Azure cloud Shell, è necessario [installare](/powershell/azure/install-az-ps) il modulo Azure PowerShell. Eseguire `Get-Module -ListAvailable Az` per trovare la versione.

### <a name="deploy-via-the-azure-cli"></a>Distribuire tramite l'interfaccia della riga di comando di Azure

Per distribuire il modello di Azure Resource Manager usando l'interfaccia della riga di comando di Azure, **copiare** lo script e selezionare **prova** per aprirlo Azure cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sulla shell, quindi scegliere **Incolla**:

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

Il comando `az cosmosdb show` Mostra l'account Azure Cosmos appena creato dopo che è stato effettuato il provisioning. Se si sceglie di usare una versione installata localmente dell'interfaccia della riga di comando di Azure invece di usare Cloud Shell, vedere l'articolo dell'interfaccia della riga di comando di [Azure](/cli/azure/) .

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

- [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
- [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelli di avvio rapido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Risolvere gli errori comuni di distribuzione di Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)