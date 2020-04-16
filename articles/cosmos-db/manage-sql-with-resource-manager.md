---
title: Creare e gestire Azure Cosmos DB con i modelli di Resource ManagerCreate and manage Azure Cosmos DB with Resource Manager templates
description: Usare i modelli di Azure Resource Manager per creare e configurare il database Cosmos di Azure per l'API SQL (Core)Use Azure Resource Manager templates to create and configure Azure Cosmos DB for SQL (Core) API
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 3514b3e77781010fd56b43229f87854ea2d591e8
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390896"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Gestire le risorse API SQL (Core) del database di Azure Cosmos con i modelli di Azure Resource ManagerManage Azure Cosmos DB SQL (Core) API resources with Azure Resource Manager templates

In questo articolo viene illustrato come usare i modelli di Azure Resource Manager per automatizzare la gestione degli account, dei database e dei contenitori di Azure Cosmos DB.

Questo articolo mostra solo esempi di modelli di Azure Resource Manager per gli account API SQL. È inoltre possibile trovare esempi di modelli per le API [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)e [Table](manage-table-with-resource-manager.md) .

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Creare un account, un database e un contenitore di Azure CosmosCreate an Azure Cosmos account, database, and container

Il modello di Azure Resource Manager seguente crea un account Azure Cosmos con:The following Azure Resource Manager template creates an Azure Cosmos account with:

* Due contenitori che condividono 400 unità richieste al secondo (RU/s) a livello di database.
* Un contenitore con velocità effettiva dedicata di 400 RU/s.

Per creare le risorse del database Cosmos di Azure, copiare il modello di esempio seguente e distribuirlo come descritto, tramite [PowerShell](#deploy-via-powershell) o [l'interfaccia della riga di comando](#deploy-via-azure-cli)di Azure.

* Facoltativamente, è possibile visitare la raccolta di guide rapide di Azure e distribuire il modello dal portale di Azure.Optionally, you can visit the [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) and deploy the template from the Azure portal.
* È inoltre possibile scaricare il modello nel computer locale o creare `--template-file` un nuovo modello e specificare il percorso locale con il parametro .

> [!IMPORTANT]
>
> * Quando si aggiungono o rimuovono posizioni a un account Cosmos di Azure, non è possibile modificare contemporaneamente altre proprietà. Queste operazioni devono essere eseguite separatamente.
> * I nomi degli account sono limitati a 44 caratteri, tutti minuscoli.
> * Per modificare i valori di velocità effettiva, inviare nuovamente il modello con RU/s aggiornati.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> Per creare un contenitore con chiave di partizione di grandi dimensioni, modificare il modello precedente per includere la `"version":2` proprietà all'interno dell'oggetto. `partitionKey`

### <a name="deploy-via-powershell"></a>Distribuire tramite PowerShellDeploy via PowerShell

Per usare PowerShell per distribuire il modello di Azure Resource Manager:To use PowerShell to deploy the Azure Resource Manager template:

1. **Copiare** lo script.
2. Selezionare Prova per aprire Azure Cloud Shell.Select **Try it** to open Azure Cloud Shell.
3. Fare clic con il pulsante destro del mouse nella finestra di Azure Cloud Shell e quindi **scegliere Incolla**.

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

È possibile scegliere di distribuire il modello con una versione di PowerShell installata localmente anziché Azure Cloud Shell.You can choose to deploy the template with a locally installed version of PowerShell instead of Azure Cloud Shell. È necessario [installare il modulo di Azure PowerShell](/powershell/azure/install-az-ps). Esegui `Get-Module -ListAvailable Az` per trovare la versione richiesta.

### <a name="deploy-via-azure-cli"></a>Distribuzione tramite l'interfaccia della riga di comando di AzureDeploy via

Per usare l'interfaccia della riga di comando di Azure per distribuire il modello di Azure Resource Manager:To use Azure CLI to deploy the Azure Resource Manager template:

1. **Copiare** lo script.
2. Selezionare Prova per aprire Azure Cloud Shell.Select **Try it** to open Azure Cloud Shell.
3. Fare clic con il pulsante destro del mouse nella finestra di Azure Cloud Shell e quindi **scegliere Incolla**.

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

Il `az cosmosdb show` comando mostra l'account Azure Cosmos appena creato dopo il provisioning. È possibile scegliere di distribuire il modello con una versione installata localmente dell'interfaccia della riga di comando di Azure anziché con Azure Cloud Shell.You can choose to deploy the template with a locally installed version of Azure CLI instead Azure Cloud Shell. Per altre informazioni, vedere l'articolo Interfaccia della riga di comando di Azure.For more information, see the [Azure Command-Line Interface (CLI)](/cli/azure/) article.

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Creare un contenitore di database Cosmos di Azure con funzionalità lato serverCreate an Azure Cosmos DB container with server-side functionality

È possibile usare un modello di Azure Resource Manager per creare un contenitore di database Cosmos di Azure con una stored procedure, un trigger e una funzione definita dall'utente.

Copiare il modello di esempio seguente e distribuirlo come descritto, con [PowerShell](#deploy-with-powershell) o [l'interfaccia della riga](#deploy-with-azure-cli)di comando di Azure.

* Facoltativamente, è possibile visitare la raccolta di guide rapide di Azure e distribuire il modello dal portale di Azure.Optionally, you can visit [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) and deploy the template from the Azure portal.
* È inoltre possibile scaricare il modello nel computer locale o creare `--template-file` un nuovo modello e specificare il percorso locale con il parametro .

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>Eseguire la distribuzione con PowerShell

Per usare PowerShell per distribuire il modello di Azure Resource Manager:To use PowerShell to deploy the Azure Resource Manager template:

1. **Copiare** lo script.
1. Selezionare Prova per aprire Azure Cloud Shell.Select **Try it** to open Azure Cloud Shell.
1. Fare clic con il pulsante destro del mouse sulla finestra di Azure Cloud Shell e quindi **scegliere Incolla**.

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

È possibile scegliere di distribuire il modello con una versione di PowerShell installata localmente anziché Azure Cloud Shell.You can choose to deploy the template with a locally installed version of PowerShell instead of Azure Cloud Shell. È necessario [installare il modulo di Azure PowerShell](/powershell/azure/install-az-ps). Esegui `Get-Module -ListAvailable Az` per trovare la versione richiesta.

### <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

Per usare l'interfaccia della riga di comando di Azure per distribuire il modello di Azure Resource Manager:To use Azure CLI to deploy the Azure Resource Manager template:

1. **Copiare** lo script.
2. Selezionare Prova per aprire Azure Cloud Shell.Select **Try it** to open Azure Cloud Shell.
3. Fare clic con il pulsante destro del mouse nella finestra di Azure Cloud Shell e quindi **scegliere Incolla**.

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

* [Documentazione di Azure Resource ManagerAzure Resource Manager documentation](/azure/azure-resource-manager/)
* [Schema del provider di risorse Azure Cosmos DBAzure Cosmos DB resource provider schema](/azure/templates/microsoft.documentdb/allversions)
* [Modelli di guida introduttiva di Azure Cosmos DBAzure Cosmos DB Quickstart templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Risolvere gli errori comuni di distribuzione di Azure Resource ManagerTroubleshoot common Azure Resource Manager deployment errors](../azure-resource-manager/templates/common-deployment-errors.md)
