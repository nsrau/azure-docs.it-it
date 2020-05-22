---
title: Creare e gestire Azure Cosmos DB con modelli di Resource Manager
description: Usare i modelli di Azure Resource Manager per creare e configurare Azure Cosmos DB per l'API Core (SQL)
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: dfdeb210c59377822b2ee69bde286b87c2251021
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592492"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Gestire le risorse dell'API Core (SQL) di Azure Cosmos DB con modelli di Azure Resource Manager

Questo articolo illustra come usare i modelli di Azure Resource Manager per distribuire e gestire account, database e contenitori di Azure Cosmos DB.

L'articolo contiene solo esempi di modelli di Azure Resource Manager per account API Core (SQL). Sono disponibili esempi di modello anche per API [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md) e [Tabella](manage-table-with-resource-manager.md).

> [!IMPORTANT]
>
> * I nomi degli account sono limitati a 44 caratteri, tutti in minuscolo.
> * Per modificare i valori di velocità effettiva, ridistribuire il modello con UR/s aggiornati.
> * Quando si aggiungono o si rimuovono percorsi in un account Azure Cosmos, non è possibile modificare contemporaneamente altre proprietà. Queste operazioni devono essere eseguite separatamente.

Per creare una delle risorse di Azure Cosmos DB seguenti, copiare il modello di esempio seguente in un nuovo file JSON. Facoltativamente, è possibile creare un file JSON dei parametri da usare quando si distribuiscono più istanze della stessa risorsa con nomi e valori diversi. Per distribuire modelli di Azure Resource Manager, è possibile scegliere tra varie soluzioni, tra cui [portale di Azure](../azure-resource-manager/templates/deploy-portal.md), [interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Account Azure Cosmos con velocità effettiva a scalabilità automatica

Questo modello consente di creare un account Azure Cosmos in due aree con le opzioni per la coerenza e il failover, con database e contenitore configurati per la velocità effettiva a scalabilità automatica con la maggior parte delle opzioni di criteri abilitate. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>Account Azure Cosmos con archivio analitico

Questo modello consente di creare un account Azure Cosmos in un'area con un contenitore con la funzione di durata TTL dei dati analitici abilitata e opzioni per usare la velocità effettiva manuale o a scalabilità automatica.

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
          "defaultValue": "",
          "metadata": {
             "description": "Location for the Cosmos DB account."
          }
       },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name for the database"
            }
        },
        "containerName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name for the container"
            }
        },
        "partitionKeyPath": {
            "type": "string",
            "defaultValue": "/partitionKey",
            "metadata": {
                "description": "The partition key for the container"
            }
        },
        "throughputPolicy":{
            "type": "string",
            "defaultValue": "Autoscale",
            "allowedValues": [ "Manual", "Autoscale" ],
            "metadata": {
                "description": "The throughput policy for the container"
            }
        },
        "manualProvisionedThroughput": {
            "type": "int",
            "defaultValue": 400,
            "minValue": 400,
            "maxValue": 1000000,
            "metadata": {
                "description": "Throughput value when using Manual Throughput Policy for the container"
            }
        },
        "maxAutoscaleThroughput": {
            "type": "int",
            "defaultValue": 4000,
            "minValue": 4000,
            "maxValue": 1000000,
            "metadata": {
                "description": "Maximum throughput when using Autoscale Throughput Policy for the container"
            }
        }
    },
    "variables": {
        "accountName": "[toLower(parameters('accountName'))]",
        "locations": 
        [ 
            {
                "locationName": "[parameters('location')]",
                "failoverPriority": 0,
                "isZoneRedundant": false
            }
        ],
        "throughputPolicy": {
            "Manual": {
                "Throughput": "[parameters('manualProvisionedThroughput')]"
            },
            "Autoscale": {
                "ProvisionedThroughputSettings": "[concat('{\"maxThroughput\":\"', parameters('maxAutoscaleThroughput'), '\"}')]"
            }            
        },
        "throughputPolicyToUse": "[if(equals(parameters('throughputPolicy'), 'Manual'), variables('throughputPolicy').Manual, variables('throughputPolicy').Autoscale)]"
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[variables('accountName')]",
            "apiVersion": "2020-03-01",
            "location": "[parameters('location')]",
            "properties": {
                "consistencyPolicy": {"defaultConsistencyLevel": "Session"},
                "databaseAccountOfferType": "Standard",
                "locations": "[variables('locations')]",
                "enableAnalyticalStorage": true
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
                        "paths": [ "[parameters('partitionKeyPath')]" ],
                        "kind": "Hash"
                    },
                    "analyticalStorageTtl": -1
                },
                "options": "[variables('throughputPolicyToUse')]"
            }
        }
    ]
}
```

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-manual-throughput"></a>Account Azure Cosmos con velocità effettiva standard (manuale)

Questo modello consente di creare un account Azure Cosmos in due aree con le opzioni per la coerenza e il failover, con database e contenitore configurati per la velocità effettiva standard con la maggior parte delle opzioni di criteri abilitate. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Contenitore Azure Cosmos DB con funzionalità lato server

Questo modello consente di creare un account, un database e un contenitore Azure Cosmos con una stored procedure, un trigger e una funzione definita dall'utente. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Account Azure Cosmos DB per il livello gratuito

Questo modello consente di creare un account e un database Azure Cosmos per il livello gratuito con una velocità effettiva che può essere condivisa con un massimo di 25 contenitori. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

* [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
* [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelli di avvio rapido di Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Risolvere errori comuni durante la distribuzione di Azure con Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
