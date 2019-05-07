---
title: Modelli di Azure Resource Manager per l'API Cassandra di Azure Cosmos DB
description: Usare i modelli Azure Resource Manager per creare e configurare l'API Cassandra di Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 9878939a461f3ba35d2b6c270de2a965f66204cc
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077546"
---
# <a name="create-azure-cosmos-db-cassandra-api-resources-from-a-resource-manager-template"></a>Creare le risorse API Cassandra di Azure Cosmos DB da un modello di Resource Manager

Informazioni su come creare un account Azure Cosmos per l'API Cassandra usando un modello di Azure Resource Manager. L'esempio seguente crea un account di API Cassandra di Azure Cosmos DB dal [modello di avvio rapido di Azure](https://aka.ms/cassandra-arm-qs). Questo modello creerà un account Azure Cosmos per l'API Cassandra con due tabelle che condividono una velocità effettiva di 400 UR/sec a livello di keyspace. 

Di seguito è riportata una copia del modello:

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

Nell'esempio precedente, si è fatto riferimento un modello archiviato in GitHub. È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il `--template-file` parametro.

## <a name="next-steps"></a>Fasi successive

Altre risorse:

- [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
- [Schema di provider di risorse di Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelli di Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Risolvere errori comuni durante la distribuzione Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)