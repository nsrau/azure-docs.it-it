---
title: Creare e gestire Azure Cosmos DB con modelli di Azure Resource Manager
description: Usare modelli di Azure Resource Manager per creare e configurare Azure Cosmos DB per l'API SQL (Core)
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 62c04fed03ad2346d0f548a4a8028f2d7d6b3486
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850466"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Gestire Azure Cosmos DB risorse API SQL (Core) con modelli di Azure Resource Manager

Questo articolo illustra come usare i modelli di Azure Resource Manager per automatizzare la gestione degli account Azure Cosmos DB, dei database e dei contenitori.

Questo articolo illustra solo Azure Resource Manager esempi di modelli per gli account API SQL. È anche possibile trovare esempi di modelli per le API [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)e [Table](manage-table-with-resource-manager.md) .

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Creare un account, un database e un contenitore di Azure Cosmos

Il modello di Azure Resource Manager seguente consente di creare un account Azure Cosmos con:

* Due contenitori che condividono la velocità effettiva di 400 unità richiesta al secondo (UR/sec) a livello di database.
* Un contenitore con una velocità effettiva di 400 ur/sec dedicata.

Per creare le risorse di Azure Cosmos DB, copiare il modello di esempio seguente e distribuirlo come descritto tramite [PowerShell](#deploy-via-powershell) o l'interfaccia della riga di comando di [Azure](#deploy-via-azure-cli).

* Facoltativamente, è possibile visitare la [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) e distribuire il modello dal portale di Azure.
* È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il parametro `--template-file`.

> [!IMPORTANT]
>
> * Quando si aggiungono o si rimuovono percorsi in un account Azure Cosmos, non è possibile modificare contemporaneamente altre proprietà. Queste operazioni devono essere eseguite separatamente.
> * I nomi degli account sono limitati a 44 caratteri, tutti minuscoli.
> * Per modificare i valori di velocità effettiva, inviare nuovamente il modello con ur/sec aggiornati.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

> [!NOTE]
> Per creare un contenitore con una chiave di partizione di grandi dimensioni, modificare il modello precedente in modo da includere la proprietà `"version":2` all'interno dell'oggetto `partitionKey`.

### <a name="deploy-via-powershell"></a>Eseguire la distribuzione tramite PowerShell

Per usare PowerShell per distribuire il modello di Azure Resource Manager:

1. **Copiare** lo script.
2. Selezionare **prova** per aprire Azure cloud Shell.
3. Fare clic con il pulsante destro del mouse nella finestra Azure Cloud Shell e quindi scegliere **Incolla**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

È possibile scegliere di distribuire il modello con una versione installata localmente di PowerShell anziché Azure Cloud Shell. È necessario [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). Eseguire `Get-Module -ListAvailable Az` per trovare la versione richiesta.

### <a name="deploy-via-azure-cli"></a>Distribuire tramite l'interfaccia della riga di comando

Per usare l'interfaccia della riga di comando di Azure per distribuire il modello di Azure Resource Manager:

1. **Copiare** lo script.
2. Selezionare **prova** per aprire Azure cloud Shell.
3. Fare clic con il pulsante destro del mouse nella finestra Azure Cloud Shell e quindi scegliere **Incolla**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Il comando `az cosmosdb show` Mostra l'account Azure Cosmos appena creato dopo il provisioning. È invece possibile scegliere di distribuire il modello con una versione installata localmente dell'interfaccia della riga di comando di Azure Azure Cloud Shell. Per altre informazioni, vedere l'articolo dell' [interfaccia della riga di comando di Azure](/cli/azure/) .

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Creare un contenitore Azure Cosmos DB con la funzionalità lato server

È possibile usare un modello di Azure Resource Manager per creare un contenitore Azure Cosmos DB con una stored procedure, un trigger e una funzione definita dall'utente.

Copiare il modello di esempio seguente e distribuirlo come descritto, tramite [PowerShell](#deploy-with-powershell) o l'interfaccia della riga di comando di [Azure](#deploy-with-azure-cli).

* Facoltativamente, è possibile visitare la [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) e distribuire il modello dal portale di Azure.
* È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il parametro `--template-file`.

[!code-json[create-cosmosdb-sql-sprocs](~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json)]

### <a name="deploy-with-powershell"></a>Eseguire la distribuzione con PowerShell

Per usare PowerShell per distribuire il modello di Azure Resource Manager:

1. **Copiare** lo script.
1. Selezionare **prova** per aprire Azure cloud Shell.
1. Fare clic con il pulsante destro del mouse sulla finestra Azure Cloud Shell e quindi scegliere **Incolla**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

È possibile scegliere di distribuire il modello con una versione installata localmente di PowerShell anziché Azure Cloud Shell. È necessario [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). Eseguire `Get-Module -ListAvailable Az` per trovare la versione richiesta.

### <a name="deploy-with-azure-cli"></a>Eseguire la distribuzione con l'interfaccia della riga di comando di Azure

Per usare l'interfaccia della riga di comando di Azure per distribuire il modello di Azure Resource Manager:

1. **Copiare** lo script.
2. Selezionare **prova** per aprire Azure cloud Shell.
3. Fare clic con il pulsante destro del mouse nella finestra Azure Cloud Shell e quindi scegliere **Incolla**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

* [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
* [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelli di avvio rapido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Risolvere gli errori comuni di distribuzione di Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
