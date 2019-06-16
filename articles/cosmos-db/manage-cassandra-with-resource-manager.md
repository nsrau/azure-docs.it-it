---
title: Modelli di Azure Resource Manager per l'API Cassandra di Azure Cosmos DB
description: Usare i modelli Azure Resource Manager per creare e configurare l'API Cassandra di Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: db754adbe60acfa155400910c47de556db793eef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968903"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Gestire le risorse di API Cassandra di Azure Cosmos DB usando modelli di Azure Resource Manager

## Creare account Azure Cosmos, keyspace e tabella <a id="create-resource"></a>

Creare risorse di Azure Cosmos DB usando un modello di Azure Resource Manager. Questo modello creerà un account Azure Cosmos per l'API Cassandra con due tabelle che condividono una velocità effettiva di 400 UR/sec a livello di keyspace. Copiare il modello e distribuire come illustrato di seguito o visita [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) e la distribuzione dal portale di Azure. È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

Per distribuire il modello di Resource Manager tramite la CLI di Azure, **copia** lo script e selezionare **Provalo** per aprire Azure Cloud shell. Per incollare lo script, la shell e quindi scegliere **incollare**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyset name: ' keysetName
read -p 'Enter the first table name: ' table1Name
read -p 'Enter the second table name: ' table2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keysetName=$keysetName \
   table1Name=$table1Name table2Name=$table2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Il `az cosmosdb show` comando Mostra l'account Azure Cosmos appena creato dopo averne effettuato. Se si sceglie di usare una versione installata in locale della riga di comando di Azure invece di usare cloud shell, vedere [interfaccia della riga di comando di Azure](/cli/azure/) articolo.

## Aggiornamento della velocità effettiva (UR/sec) in un keyspace <a id="keyspace-ru-update"></a>

Il modello seguente aggiornerà la velocità effettiva di un keyspace. Copiare il modello e distribuire come illustrato di seguito o visita [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-keyspace-ru-update/) e la distribuzione dal portale di Azure. È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.

[!code-json[cosmosdb-cassandra-keyspace-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json)]

### <a name="deploy-keyspace-template-via-azure-cli"></a>Distribuire il modello di keyspace tramite CLI di Azure

Per distribuire il modello di Resource Manager tramite la CLI di Azure, selezionare **Provalo** per aprire Azure Cloud shell. Per incollare lo script, la shell e quindi scegliere **incollare**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName throughput=$throughput
```

## Aggiornamento della velocità effettiva (UR/sec) in una tabella <a id="table-ru-update"></a>

Il modello seguente aggiornerà la velocità effettiva di una tabella. Copiare il modello e distribuire come illustrato di seguito o visita [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-table-ru-update/) e la distribuzione dal portale di Azure. È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.

[!code-json[cosmosdb-cassandra-table-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-template-via-azure-cli"></a>Distribuire il modello di tabella tramite la CLI di Azure

Per distribuire il modello di Resource Manager tramite la CLI di Azure, selezionare **Provalo** per aprire Azure Cloud shell. Per incollare lo script, la shell e quindi scegliere **incollare**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>Fasi successive

Altre risorse:

- [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
- [Schema di provider di risorse di Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelli di Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Risolvere errori comuni durante la distribuzione Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)