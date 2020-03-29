---
title: Resource Manager templates for Azure Cosmos DB API for MongoDB
description: Usare i modelli di Azure Resource Manager per creare e configurare l'API del database di Azure Cosmos per MongoDB.Use Azure Resource Manager templates to create and configure Azure Cosmos DB API for MongoDB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 531f122679c463b11c84eba2fca9f30b09e0935f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063629"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Gestire le risorse dell'API MongoDB di Azure Cosmos DB usando i modelli di Azure Resource ManagerManage Azure Cosmos DB MongoDB API resources using Azure Resource Manager templates

Questo articolo descrive come eseguire diverse operazioni per automatizzare la gestione degli account, dei database e dei contenitori di Azure Cosmos usando i modelli di Azure Resource Manager.This article describes how to perform different operations to automate management of your Azure Cosmos DB accounts, databases and containers using Azure Resource Manager templates. Questo articolo contiene esempi solo per l'API di Azure Cosmos DB per MongoDB per trovare esempi per altri account di tipo API: Usare i modelli di Azure Resource Manager con l'API di Azure Cosmos DB per gli articoli [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [Table](manage-table-with-resource-manager.md) .

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>Creare l'API del database di Azure per l'account, il database e la raccolta MongoDBCreate Azure Cosmos DB API for MongoDB account, database and collection<a id="create-resource"></a>

Creare risorse di Azure Cosmos DB usando un modello di Azure Resource Manager.Create Azure Cosmos DB resources using an Azure Resource Manager template. Questo modello creerà un account Azure Cosmos per l'API MongoDB con due raccolte che condividono velocità effettiva di 400 RU/s a livello di database. Copiare il modello e distribuirlo come illustrato di seguito oppure visitare la raccolta di guide rapide di Azure e distribuirlo dal portale di Azure.Copy the template and deploy as shown below or visit [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) and deploy from the Azure portal. È inoltre possibile scaricare il modello nel computer locale o creare `--template-file` un nuovo modello e specificare il percorso locale con il parametro .

> [!NOTE]
> I nomi di account devono essere minuscoli e 44 caratteri o meno.
> Per aggiornare RU/s, inviare nuovamente il modello con i valori delle proprietà della velocità effettiva aggiornati.
>
> Attualmente è possibile creare solo la versione 3.2 (ovvero `*.documents.azure.com`gli account usando l'endpoint nel formato ) dell'API di Azure Cosmos DB per gli account MongoDB usando PowerShell e l'interfaccia della riga di comando. Per creare una versione 3.6 degli account, usare invece i modelli di Resource Manager (sotto) o il portale di Azure.To create 3.6 version of accounts, use Resource Manager templates (below) or Azure portal instead.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

### <a name="deploy-via-the-azure-cli"></a>Distribuire tramite l'interfaccia della riga di comando di AzureDeploy via the Azure CLI

Per distribuire il modello di Azure Resource Manager usando l'interfaccia della riga di comando di Azure, **copiare** lo script e selezionare Prova per aprire Azure Cloud Shell.To deploy the Azure Resource Manager template using the Azure CLI, Copy the script and select **Try it** to open Azure Cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sulla shell, quindi scegliere **Incolla:**

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Il `az cosmosdb show` comando mostra l'account Azure Cosmos appena creato dopo il provisioning. Se si sceglie di usare una versione installata localmente dell'interfaccia della riga di comando di Azure anziché usare Cloud Shell, vedere l'articolo [dell'interfaccia della riga di comando](/cli/azure/) di Azure.If you choose to use a locally installed version of the Azure CLI instead of using Cloud Shell, see the Azure CLI article.

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

- [Documentazione di Azure Resource ManagerAzure Resource Manager documentation](/azure/azure-resource-manager/)
- [Schema del provider di risorse Azure Cosmos DBAzure Cosmos DB resource provider schema](/azure/templates/microsoft.documentdb/allversions)
- [Modelli di guida introduttiva di Azure Cosmos DBAzure Cosmos DB Quickstart templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Risolvere gli errori comuni di distribuzione di Azure Resource ManagerTroubleshoot common Azure Resource Manager deployment errors](../azure-resource-manager/templates/common-deployment-errors.md)
