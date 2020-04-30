---
title: Modelli di Gestione risorse per Azure Cosmos DB API Gremlin
description: Usare i modelli di Azure Resource Manager per creare e configurare Azure Cosmos DB API Gremlin.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 77390d58412530208c2886a51460d3aab8114d27
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200769"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Gestire Azure Cosmos DB risorse dell'API Gremlin usando modelli di Azure Resource Manager

Questo articolo descrive come eseguire diverse operazioni per automatizzare la gestione degli account Azure Cosmos DB, dei database e dei contenitori usando i modelli Azure Resource Manager. Questo articolo contiene esempi per gli account dell'API Gremlin, per trovare esempi per altri account di tipo API, vedere: usare Azure Resource Manager modelli con l'API Azure Cosmos DB per [Cassandra](manage-cassandra-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), articoli di [tabella](manage-table-with-resource-manager.md) .

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>Creare Azure Cosmos DB API per l'account, il database e la raccolta di MongoDB<a id="create-resource"></a>

Creare Azure Cosmos DB risorse usando un modello di Azure Resource Manager. Questo modello creerà un account Azure Cosmos per l'API Gremlin con un database e un grafo con una velocità effettiva di 400 ur/s. Copiare il modello e distribuirlo come illustrato di seguito oppure visitare la [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin/) e distribuire dal portale di Azure. È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.

> [!NOTE]
> I nomi degli account devono essere minuscoli e 44 o un numero inferiore di caratteri.
> Per aggiornare ur/s, ridistribuire il modello con i valori della proprietà velocità effettiva aggiornati.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
       "accountName": {
           "type": "string",
           "defaultValue": "",
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
       "primaryRegion": {
           "type": "string",
           "metadata": {
               "description": "The primary replica region for the Cosmos DB account."
           }
       },
       "secondaryRegion": {
           "type": "string",
           "metadata": {
               "description": "The secondary replica region for the Cosmos DB account."
           }
       },
       "defaultConsistencyLevel": {
           "type": "string",
           "defaultValue": "Session",
           "allowedValues": [
               "Eventual",
               "ConsistentPrefix",
               "Session",
               "BoundedStaleness",
               "Strong"
           ],
           "metadata": {
               "description": "The default consistency level of the Cosmos DB account."
           }
       },
       "maxStalenessPrefix": {
           "type": "int",
           "defaultValue": 100000,
           "minValue": 10,
           "maxValue": 1000000,
           "metadata": {
               "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
           }
       },
       "maxIntervalInSeconds": {
           "type": "int",
           "defaultValue": 300,
           "minValue": 5,
           "maxValue": 86400,
           "metadata": {
               "description": "Max lag time (seconds). Required for BoundedStaleness. Valid ranges, Single Region: 5 to 84600. Multi Region: 300 to 86400."
           }
       },
       "automaticFailover": {
           "type": "bool",
           "defaultValue": true,
           "allowedValues": [
               true,
               false
           ],
           "metadata": {
               "description": "Enable automatic failover for regions"
           }
       },
       "databaseName": {
           "type": "string",
           "defaultValue": "database1",
           "metadata": {
               "description": "The name for the Gremlin database"
           }
       },
       "graphName": {
           "type": "string",
           "defaultValue": "graph1",
           "metadata": {
               "description": "The name for the Gremlin graph"
           }
       },
       "throughput": {
           "type": "int",
           "defaultValue": 400,
           "minValue": 400,
           "maxValue": 1000000,
           "metadata": {
               "description": "Throughput for the Gremlin graph"
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
       "locations": [
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
   "resources": [
       {
           "type": "Microsoft.DocumentDB/databaseAccounts",
           "name": "[variables('accountName')]",
           "apiVersion": "2020-03-01",
           "location": "[parameters('location')]",
           "kind": "GlobalDocumentDB",
           "properties": {
               "capabilities": [
                   {
                       "name": "EnableGremlin"
                   }
               ],
               "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
               "locations": "[variables('locations')]",
               "databaseAccountOfferType": "Standard",
               "enableAutomaticFailover": "[parameters('automaticFailover')]"
           }
       },
       {
           "type": "Microsoft.DocumentDB/databaseAccounts/gremlinDatabases",
           "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
           "apiVersion": "2020-03-01",
           "dependsOn": [
               "[resourceId('Microsoft.DocumentDB/databaseAccounts/', variables('accountName'))]"
           ],
           "properties": {
               "resource": {
                   "id": "[parameters('databaseName')]"
               }
           }
       },
       {
           "type": "Microsoft.DocumentDb/databaseAccounts/gremlinDatabases/graphs",
           "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('graphName'))]",
           "apiVersion": "2020-03-01",
           "dependsOn": [
               "[resourceId('Microsoft.DocumentDB/databaseAccounts/gremlinDatabases', variables('accountName'),  parameters('databaseName'))]"
           ],
           "properties": {
               "resource": {
                   "id": "[parameters('graphName')]",
                   "indexingPolicy": {
                       "indexingMode": "consistent",
                       "includedPaths": [
                           {
                               "path": "/*"
                           }
                       ],
                       "excludedPaths": [
                           {
                               "path": "/myPathToNotIndex/*"
                           }
                       ]
                   },
                   "partitionKey": {
                       "paths": [
                           "/myPartitionKey"
                       ],
                       "kind": "Hash"
                   },
                   "options": {
                       "throughput": "[parameters('throughput')]"
                   }
               }
           }
       }
   ]
}
```

## <a name="deploy-with-the-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

Per distribuire il modello di Azure Resource Manager usando l'interfaccia della riga di comando di Azure, **copiare** lo script e selezionare **prova** per aprirlo Azure cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sulla shell, quindi scegliere **Incolla**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the graph name: ' graphName
read -p 'Enter the throughput: ' throughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   graphName=$graphName throughput=$throughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Il `az cosmosdb show` comando Mostra l'account Azure Cosmos appena creato dopo che è stato effettuato il provisioning. Se si sceglie di usare una versione installata localmente dell'interfaccia della riga di comando di Azure invece di usare Cloud Shell, vedere l'articolo dell'interfaccia della riga di comando di [Azure](/cli/azure/) .

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

- [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
- [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelli di avvio rapido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Risolvere gli errori comuni di distribuzione di Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)