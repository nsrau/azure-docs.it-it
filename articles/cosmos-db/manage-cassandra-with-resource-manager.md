---
title: Modelli di Resource Manager per l'API Cassandra di Azure Cosmos
description: Usare i modelli di Azure Resource Manager per creare e configurare l'API Cassandra del database di Azure Cosmos.Use Azure Resource Manager templates to create and configure Azure Cosmos DB Cassandra API.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: c4dc97453fe50865db74f8918ef3dffdb4013b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251895"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Gestire le risorse API Cassandra di Azure Cosmos DB usando i modelli di Azure Resource ManagerManage Azure Cosmos DB Cassandra API resources using Azure Resource Manager templates

Questo articolo descrive come eseguire diverse operazioni per automatizzare la gestione degli account, dei database e dei contenitori di Azure Cosmos usando i modelli di Azure Resource Manager.This article describes how to perform different operations to automate management of your Azure Cosmos DB accounts, databases and containers using Azure Resource Manager templates. Questo articolo contiene esempi solo per gli account DELL'API SQL, per trovare esempi per altri account di tipo API, vedere: Usare i modelli di Azure Resource Manager con l'API di Azure Cosmos DB per [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [articoli della tabella.](manage-table-with-resource-manager.md)

## <a name="create-azure-cosmos-account-keyspace-and-table"></a>Creare l'account Cosmos di Azure, lo spazio chiave e la tabellaCreate Azure Cosmos account, keyspace and table<a id="create-resource"></a>

Creare risorse di Azure Cosmos DB usando un modello di Azure Resource Manager.Create Azure Cosmos DB resources using an Azure Resource Manager template. Questo modello creerà un account Cosmos di Azure per l'API Cassandra con due tabelle che condividono una velocità effettiva di 400 RU/s a livello di keyspace. Copiare il modello e distribuirlo come illustrato di seguito oppure visitare la raccolta di guide rapide di Azure e distribuirlo dal portale di Azure.Copy the template and deploy as shown below or visit [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) and deploy from the Azure portal. È inoltre possibile scaricare il modello nel computer locale o creare `--template-file` un nuovo modello e specificare il percorso locale con il parametro .

> [!NOTE]
> I nomi di account devono essere minuscoli e 44 caratteri o meno.
> Per aggiornare RU/s, inviare nuovamente il modello con i valori delle proprietà della velocità effettiva aggiornati.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di AzureDeploy with the Azure CLI

Per distribuire il modello di Azure Resource Manager usando l'interfaccia della riga di comando di Azure, **copiare** lo script e selezionare Prova per aprire Azure Cloud Shell.To deploy the Azure Resource Manager template using the Azure CLI, Copy the script and select **Try it** to open Azure Cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sulla shell, quindi scegliere **Incolla:**

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

Il `az cosmosdb show` comando mostra l'account Azure Cosmos appena creato dopo il provisioning. Se si sceglie di usare una versione installata localmente dell'interfaccia della riga di comando di Azure anziché usare Cloud Shell, vedere l'articolo [dell'interfaccia della riga di comando](/cli/azure/) di Azure.If you choose to use a locally installed version of the Azure CLI instead of using Cloud Shell, see the Azure CLI article.


## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

- [Documentazione di Azure Resource ManagerAzure Resource Manager documentation](/azure/azure-resource-manager/)
- [Schema del provider di risorse Azure Cosmos DBAzure Cosmos DB resource provider schema](/azure/templates/microsoft.documentdb/allversions)
- [Modelli di guida introduttiva di Azure Cosmos DBAzure Cosmos DB Quickstart templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Risolvere gli errori comuni di distribuzione di Azure Resource ManagerTroubleshoot common Azure Resource Manager deployment errors](../azure-resource-manager/templates/common-deployment-errors.md)
