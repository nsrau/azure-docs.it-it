---
title: Modelli di Gestione risorse per l'API Azure Cosmos DB per MongoDB
description: Usare i modelli di Azure Resource Manager per creare e configurare l'API Azure Cosmos DB per MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: e312b5d8b6052dafa4855afa035429ef06608f1b
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200786"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Gestire Azure Cosmos DB risorse API MongoDB usando modelli di Azure Resource Manager

Questo articolo descrive come eseguire diverse operazioni per automatizzare la gestione degli account Azure Cosmos DB, dei database e dei contenitori usando i modelli Azure Resource Manager. Questo articolo contiene esempi per l'API di Azure Cosmos DB solo per MongoDB, per trovare esempi per altri account di tipo API, vedere: usare Azure Resource Manager modelli con l'API di Azure Cosmos DB per [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), articoli di [tabella](manage-table-with-resource-manager.md) .

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>Creare Azure Cosmos DB API per l'account, il database e la raccolta di MongoDB<a id="create-resource"></a>

Creare Azure Cosmos DB risorse usando un modello di Azure Resource Manager. Questo modello creerà un account Azure Cosmos per l'API MongoDB con due raccolte che condividono la velocità effettiva 400 ur/s a livello di database. Copiare il modello e distribuirlo come illustrato di seguito oppure visitare la [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) e distribuire dal portale di Azure. È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.

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
   "serverVersion": {
      "defaultValue": "3.6",
      "allowedValues": [
         "3.2",
         "3.6"
      ],
      "type": "String",
      "metadata": {
         "description": "Specifies the MongoDB server version to use."
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
   "databaseName": {
      "type": "string",
      "defaultValue": "Database1",
      "metadata": {
         "description": "The name for the Mongo DB database"
      }
   },
   "throughput": {
      "type": "int",
      "defaultValue": 400,
      "minValue": 400,
      "maxValue": 1000000,
      "metadata": {
         "description": "The shared throughput for the Mongo DB database"
      }
   },
   "collection1Name": {
      "type": "string",
      "defaultValue": "Collection1",
      "metadata": {
         "description": "The name for the first Mongo DB collection"
      }
   },
   "collection2Name": {
      "type": "string",
      "defaultValue": "Collection2",
      "metadata": {
         "description": "The name for the second Mongo DB collection"
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
      "kind": "MongoDB",
      "properties": {
         "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
         "locations": "[variables('locations')]",
         "databaseAccountOfferType": "Standard",
         "apiProperties": {
            "serverVersion": "[parameters('serverVersion')]"
         }
      }
   },
   {
      "type": "Microsoft.DocumentDB/databaseAccounts/mongodbDatabases",
      "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
      "apiVersion": "2020-03-01",
      "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/', variables('accountName'))]" ],
      "properties":{
         "resource":{
            "id": "[parameters('databaseName')]"
         },
         "options": { "throughput": "[parameters('throughput')]" }
      }
   },
   {
      "type": "Microsoft.DocumentDb/databaseAccounts/mongodbDatabases/collections",
      "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('collection1Name'))]",
      "apiVersion": "2020-03-01",
      "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/mongodbDatabases', variables('accountName'), parameters('databaseName'))]" ],
      "properties":
      {
         "resource":{
            "id":  "[parameters('collection1Name')]",
            "shardKey": { "user_id": "Hash" },
            "indexes": [
               {
                  "key": { "keys":["user_id", "user_address"] },
                  "options": { "unique": "true" }
               },
               {
                  "key": { "keys":["_ts"] },
                  "options": { "expireAfterSeconds": "2629746" }
               }
            ],
            "options": {
               "If-Match": "<ETag>"
            }
         }
      }
   },
   {
      "type": "Microsoft.DocumentDb/databaseAccounts/mongodbDatabases/collections",
      "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('collection2Name'))]",
      "apiVersion": "2020-03-01",
      "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/mongodbDatabases', variables('accountName'),  parameters('databaseName'))]" ],
      "properties":
      {
         "resource":{
            "id":  "[parameters('collection2Name')]",
            "shardKey": { "company_id": "Hash" },
            "indexes": [
               {
                  "key": { "keys":["company_id", "company_address"] },
                  "options": { "unique": "true" }
               },
               {
                  "key": { "keys":["_ts"] },
                  "options": { "expireAfterSeconds": "2629746" }
               }
            ],
            "options": {
               "If-Match": "<ETag>"
            }
         }
      }
   }
]
}
```

### <a name="deploy-via-the-azure-cli"></a>Distribuire tramite l'interfaccia della riga di comando di Azure

Per distribuire il modello di Azure Resource Manager usando l'interfaccia della riga di comando di Azure, **copiare** lo script e selezionare **prova** per aprirlo Azure cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sulla shell, quindi scegliere **Incolla**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the database throughput: ' throughput
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName throughput=$throughput collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Il `az cosmosdb show` comando Mostra l'account Azure Cosmos appena creato dopo che è stato effettuato il provisioning. Se si sceglie di usare una versione installata localmente dell'interfaccia della riga di comando di Azure invece di usare Cloud Shell, vedere l'articolo dell'interfaccia della riga di comando di [Azure](/cli/azure/) .

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

- [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
- [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelli di avvio rapido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Risolvere gli errori comuni di distribuzione di Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
