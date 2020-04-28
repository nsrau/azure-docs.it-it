---
title: Creare e gestire Azure Cosmos DB con modelli di Gestione risorse
description: Usare modelli di Azure Resource Manager per creare e configurare Azure Cosmos DB per l'API SQL (Core)
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: mjbrown
ms.openlocfilehash: dc4f4666df4d3183e27f073e9ebbe8528cfbe262
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188791"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Gestire Azure Cosmos DB risorse API SQL (Core) con modelli di Azure Resource Manager

In questo articolo viene illustrato come usare i modelli di Azure Resource Manager per automatizzare la gestione degli account, dei database e dei contenitori di Azure Cosmos DB.

Questo articolo illustra solo Azure Resource Manager esempi di modelli per gli account API SQL. È anche possibile trovare esempi di modelli per le API [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)e [Table](manage-table-with-resource-manager.md) .

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Creare un account, un database e un contenitore di Azure Cosmos

Il modello di Azure Resource Manager seguente consente di creare un account Azure Cosmos con:

* Due contenitori che condividono la velocità effettiva di 400 unità richiesta al secondo (UR/sec) a livello di database.
* Un contenitore con una velocità effettiva di 400 ur/sec dedicata.

Per creare le risorse di Azure Cosmos DB, copiare il modello di esempio seguente e distribuirlo come descritto tramite [PowerShell](#deploy-via-powershell) o l'interfaccia della riga di comando di [Azure](#deploy-via-azure-cli).

* Facoltativamente, è possibile visitare la [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) e distribuire il modello dal portale di Azure.
* È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.

> [!IMPORTANT]
>
> * Quando si aggiungono o si rimuovono percorsi in un account Azure Cosmos, non è possibile modificare contemporaneamente altre proprietà. Queste operazioni devono essere eseguite separatamente.
> * I nomi degli account sono limitati a 44 caratteri, tutti minuscoli.
> * Per modificare i valori di velocità effettiva, ridistribuire il modello con ur/sec aggiornati.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "defaultValue": "[concat('sql-', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Cosmos DB account name, max length 44 characters"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the Cosmos DB account."
            }
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        },
        "defaultConsistencyLevel": {
            "type": "string",
            "defaultValue": "Session",
            "allowedValues": [ "Eventual", "ConsistentPrefix", "Session", "BoundedStaleness", "Strong" ],
            "metadata": {
                "description": "The default consistency level of the Cosmos DB account."
            }
        },
        "maxStalenessPrefix": {
            "type": "int",
            "minValue": 10,
            "defaultValue": 100000,
            "maxValue": 2147483647,
            "metadata": {
                "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
            }
        },
        "maxIntervalInSeconds": {
            "type": "int",
            "minValue": 5,
            "defaultValue": 300,
            "maxValue": 86400,
            "metadata": {
                "description": "Max lag time (minutes). Required for BoundedStaleness. Valid ranges, Single Region: 5 to 84600. Multi Region: 300 to 86400."
            }
        },
        "automaticFailover": {
            "type": "bool",
            "defaultValue": true,
            "allowedValues": [ true, false ],
            "metadata": {
                "description": "Enable automatic failover for regions"
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "myDatabase",
            "metadata": {
                "description": "The name for the database"
            }
        },
        "containerName": {
            "type": "string",
            "defaultValue": "myContainer",
            "metadata": {
                "description": "The name for the container"
            }
        },
        "throughput": {
            "type": "int",
            "defaultValue": 400,
            "minValue": 400,
            "maxValue": 1000000,
            "metadata": {
                "description": "The throughput for the container"
            }
        }
    },
    "variables": {
        "accountName": "[toLower(parameters('accountName'))]",
        "consistencyPolicy": {
            "Eventual": {
                "defaultConsistencyLevel": "Eventual"
            },
            "ConsistentPrefix": {
                "defaultConsistencyLevel": "ConsistentPrefix"
            },
            "Session": {
                "defaultConsistencyLevel": "Session"
            },
            "BoundedStaleness": {
                "defaultConsistencyLevel": "BoundedStaleness",
                "maxStalenessPrefix": "[parameters('maxStalenessPrefix')]",
                "maxIntervalInSeconds": "[parameters('maxIntervalInSeconds')]"
            },
            "Strong": {
                "defaultConsistencyLevel": "Strong"
            }
        },
        "locations":
        [
            {
                "locationName": "[parameters('primaryRegion')]",
                "failoverPriority": 0,
                "isZoneRedundant": false
            },
            {
                "locationName": "[parameters('secondaryRegion')]",
                "failoverPriority": 1,
                "isZoneRedundant": false
            }
        ]
    },
    "resources":
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[variables('accountName')]",
            "apiVersion": "2020-03-01",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
                "locations": "[variables('locations')]",
                "databaseAccountOfferType": "Standard",
                "enableAutomaticFailover": "[parameters('automaticFailover')]"
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
            "apiVersion": "2020-03-01",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]" ],
            "properties":{
                "resource":{
                    "id": "[parameters('databaseName')]"
                }
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'))]",
            "apiVersion": "2020-03-01",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases', variables('accountName'), parameters('databaseName'))]" ],
            "properties":
            {
                "resource":{
                    "id":  "[parameters('containerName')]",
                    "partitionKey": {
                        "paths": [
                        "/myPartitionKey"
                        ],
                        "kind": "Hash"
                    },
                    "indexingPolicy": {
                        "indexingMode": "consistent",
                        "includedPaths": [{
                                "path": "/*"
                            }
                        ],
                        "excludedPaths": [{
                                "path": "/myPathToNotIndex/*"
                            }
                        ],
                        "compositeIndexes":[  
                        [
                            {
                                "path":"/name",
                                "order":"ascending"
                            },
                            {
                                "path":"/age",
                                "order":"descending"
                            }
                        ]
                    ],
                    "spatialIndexes": [
                            {
                                "path": "/path/to/geojson/property/?",
                                "types": [
                                    "Point",
                                    "Polygon",
                                    "MultiPolygon",
                                    "LineString"
                                ]
                            }
                        ]
                    },
                    "defaultTtl": 86400,
                    "uniqueKeyPolicy": {
                        "uniqueKeys": [
                        {
                            "paths": [
                            "/phoneNumber"
                            ]
                        }
                        ]
                    }
                },
                "options": { "throughput": "[parameters('throughput')]" }
            }
        }
    ]
}
```

> [!NOTE]
> Per creare un contenitore con una chiave di partizione di grandi dimensioni, modificare il modello `"version":2` precedente in modo `partitionKey` da includere la proprietà all'interno dell'oggetto.

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
$containerName = Read-Host -Prompt "Enter the container name"
$throughput = Read-Host -Prompt "Enter the container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName `
    -throughput $throughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2020-03-01" --ResourceGroupName $resourceGroupName).name
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
read -p 'Enter the container name: ' containerName
read -p 'Enter the container throughput: throughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   containerName=$containerName \
   throughput=$throughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Il `az cosmosdb show` comando Mostra l'account Azure Cosmos appena creato dopo il provisioning. È invece possibile scegliere di distribuire il modello con una versione installata localmente dell'interfaccia della riga di comando di Azure Azure Cloud Shell. Per altre informazioni, vedere l'articolo dell' [interfaccia della riga di comando di Azure](/cli/azure/) .

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Creare un contenitore Azure Cosmos DB con la funzionalità lato server

È possibile usare un modello di Azure Resource Manager per creare un contenitore Azure Cosmos DB con una stored procedure, un trigger e una funzione definita dall'utente.

Copiare il modello di esempio seguente e distribuirlo come descritto, tramite [PowerShell](#deploy-with-powershell) o l'interfaccia della riga di comando di [Azure](#deploy-with-azure-cli).

* Facoltativamente, è possibile visitare la [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) e distribuire il modello dal portale di Azure.
* È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "defaultValue": "[concat('cosmos-', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Cosmos DB account name"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the Cosmos DB account."
            }
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        },
        "defaultConsistencyLevel": {
            "type": "string",
            "defaultValue": "Session",
            "allowedValues": [ "Eventual", "ConsistentPrefix", "Session", "BoundedStaleness", "Strong" ],
            "metadata": {
                "description": "The default consistency level of the Cosmos DB account."
            }
        },
        "maxStalenessPrefix": {
            "type": "int",
            "minValue": 10,
            "defaultValue": 100000,
            "maxValue": 2147483647,
            "metadata": {
                "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
            }
        },
        "maxIntervalInSeconds": {
            "type": "int",
            "minValue": 5,
            "defaultValue": 300,
            "maxValue": 86400,
            "metadata": {
                "description": "Max lag time (seconds). Required for BoundedStaleness. Valid ranges, Single Region: 5 to 84600. Multi Region: 300 to 86400."
            }
        },
        "automaticFailover": {
            "type": "bool",
            "defaultValue": true,
            "allowedValues": [ true, false ],
            "metadata": {
                "description": "Enable automatic failover for regions"
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "database1",
            "metadata": {
                "description": "The name for the Core (SQL) database"
            }
        },
        "containerName": {
            "type": "string",
            "defaultValue": "container1",
            "metadata": {
                "description": "The name for the Core (SQL) API container"
            }
        },
        "throughput": {
            "type": "int",
            "defaultValue": 400,
            "minValue": 400,
            "maxValue": 1000000,
            "metadata": {
                "description": "The throughput for the container"
            }
        }
    },
    "variables": {
        "accountName": "[toLower(parameters('accountName'))]",
        "consistencyPolicy": {
            "Eventual": {
                "defaultConsistencyLevel": "Eventual"
            },
            "ConsistentPrefix": {
                "defaultConsistencyLevel": "ConsistentPrefix"
            },
            "Session": {
                "defaultConsistencyLevel": "Session"
            },
            "BoundedStaleness": {
                "defaultConsistencyLevel": "BoundedStaleness",
                "maxStalenessPrefix": "[parameters('maxStalenessPrefix')]",
                "maxIntervalInSeconds": "[parameters('maxIntervalInSeconds')]"
            },
            "Strong": {
                "defaultConsistencyLevel": "Strong"
            }
        },
        "locations":
        [
            {
                "locationName": "[parameters('primaryRegion')]",
                "failoverPriority": 0,
                "isZoneRedundant": false
            },
            {
                "locationName": "[parameters('secondaryRegion')]",
                "failoverPriority": 1,
                "isZoneRedundant": false
            }
        ]
    },
    "resources":
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[variables('accountName')]",
            "apiVersion": "2020-03-01",
            "location": "[parameters('location')]",
            "kind": "GlobalDocumentDB",
            "properties": {
                "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
                "locations": "[variables('locations')]",
                "databaseAccountOfferType": "Standard",
                "enableAutomaticFailover": "[parameters('automaticFailover')]"
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
            "apiVersion": "2020-03-01",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]" ],
            "properties":{
                "resource":{
                    "id": "[parameters('databaseName')]"
                }
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'))]",
            "apiVersion": "2020-03-01",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases', variables('accountName'), parameters('databaseName'))]" ],
            "properties":
            {
                "resource":{
                    "id":  "[parameters('containerName')]",
                    "partitionKey": {
                        "paths": ["/myPartitionKey"],
                        "kind": "Hash"
                    },
                    "indexingPolicy": {
                        "indexingMode": "consistent",
                        "includedPaths": [{ "path": "/*" } ],
                        "excludedPaths": [{ "path": "/myPathToNotIndex/*" } ]
                    }
                },
                "options": { "throughput": "[parameters('throughput')]" }
            },
            "resources":
            [
                {
                    "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures",
                    "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'), '/myStoredProcedure')]",
                    "apiVersion": "2020-03-01",
                    "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers', variables('accountName'), parameters('databaseName'), parameters('containerName'))]" ],
                    "properties": {
                        "resource": {
                        "id": "myStoredProcedure",
                        "body": "function () { var context = getContext(); var response = context.getResponse(); response.setBody('Hello, World'); }"
                        }
                    }
                },
                {
                    "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers",
                    "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'), '/myPreTrigger')]",
                    "apiVersion": "2020-03-01",
                    "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers', variables('accountName'), parameters('databaseName'), parameters('containerName'))]" ],
                    "properties": {
                        "resource": {
                        "id": "myPreTrigger",
                        "triggerType": "Pre",
                        "triggerOperation": "Create",
                        "body": "function validateToDoItemTimestamp(){var context=getContext();var request=context.getRequest();var itemToCreate=request.getBody();if(!('timestamp'in itemToCreate)){var ts=new Date();itemToCreate['timestamp']=ts.getTime();}request.setBody(itemToCreate);}"
                        }
                    }
                },
                {
                    "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions",
                    "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'), '/myUserDefinedFunction')]",
                    "apiVersion": "2020-03-01",
                    "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers', variables('accountName'), parameters('databaseName'), parameters('containerName'))]" ],
                    "properties": {
                        "resource": {
                        "id": "myUserDefinedFunction",
                        "body": "function tax(income){if(income==undefined)throw'no input';if(income<1000)return income*0.1;else if(income<10000)return income*0.2;else return income*0.4;}"
                        }
                    }
                }
            ]
        }
    ]
}
```

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
$throughput = Read-Host -Prompt "Enter the container throughput (i.e. 400)"

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
    -throughput $throughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2020-03-01" --ResourceGroupName $resourceGroupName).name
```

È possibile scegliere di distribuire il modello con una versione installata localmente di PowerShell anziché Azure Cloud Shell. È necessario [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). Eseguire `Get-Module -ListAvailable Az` per trovare la versione richiesta.

### <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

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
read -p 'Enter the throughput: ' throughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName throughput=$throughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

<a id="free-tier"></a>

## <a name="create-a-free-tier-azure-cosmos-db-account"></a>Creazione di un account Azure Cosmos DB di livello gratuito

È possibile usare un modello di Azure Resource Manager per creare un contenitore di Azure Cosmos DB nel livello gratuito con un database con una velocità effettiva di 400 ur/s che può essere condivisa con un massimo di 25 contenitori.

Copiare il modello di esempio seguente e distribuirlo come descritto, tramite [PowerShell](#deploy-with-powershell) o l'interfaccia della riga di comando di [Azure](#deploy-with-azure-cli).

* È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "defaultValue": "[concat('cosmos-', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Cosmos DB account name"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the Cosmos DB account."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "database1",
            "metadata": {
                "description": "The name for the Core (SQL) database"
            }
        }
    },
    "variables": {
        "accountName": "[toLower(parameters('accountName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2020-03-01",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "properties": {
                "enableFreeTier": true,
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session"
                }
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases",
            "apiVersion": "2020-03-01",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]" ],
            "properties": {
                "resource": {
                    "id": "[parameters('databaseName')]"
                },
                "options": {"throughput": 400}
            }
        }
    ]
}
```

### <a name="deploy-with-powershell"></a>Eseguire la distribuzione con PowerShell

Per usare PowerShell per distribuire il modello di Azure Resource Manager:

1. **Copiare** lo script.
1. Selezionare **prova** per aprire Azure cloud Shell.
1. Fare clic con il pulsante destro del mouse sulla finestra Azure Cloud Shell e quindi scegliere **Incolla**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$databaseName = Read-Host -Prompt "Enter the database name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -databaseName $databaseName `

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2020-03-01" --ResourceGroupName $resourceGroupName).name
```

È possibile scegliere di distribuire il modello con una versione installata localmente di PowerShell anziché Azure Cloud Shell. È necessario [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). Eseguire `Get-Module -ListAvailable Az` per trovare la versione richiesta.

### <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

Per usare l'interfaccia della riga di comando di Azure per distribuire il modello di Azure Resource Manager:

1. **Copiare** lo script.
2. Selezionare **prova** per aprire Azure cloud Shell.
3. Fare clic con il pulsante destro del mouse nella finestra Azure Cloud Shell e quindi scegliere **Incolla**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

* [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
* [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelli di avvio rapido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Risolvere gli errori comuni di distribuzione di Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
