---
title: Modelli di Azure Resource Manager per Azure Cosmos DB API Cassandra
description: Usare i modelli di Azure Resource Manager per creare e configurare API Cassandra di Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: thvankra
ms.openlocfilehash: d71847da4b395be230bce5773a5ba7a472a412f4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832695"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Gestire Azure Cosmos DB API Cassandra risorse tramite modelli Azure Resource Manager

Questo articolo descrive come eseguire diverse operazioni per automatizzare la gestione degli account Azure Cosmos DB, dei database e dei contenitori usando i modelli Azure Resource Manager. Questo articolo contiene esempi solo per gli account API SQL, per trovare esempi per altri account di tipo API vedere: usare Gestione risorse modelli con l'API Azure Cosmos DB per [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), articoli di [tabella](manage-table-with-resource-manager.md) .

## Creare un account Azure Cosmos, spazio di spazio e tabella<a id="create-resource"></a>

Creare Azure Cosmos DB risorse usando un modello di Azure Resource Manager. Questo modello creerà un account Azure Cosmos per API Cassandra con due tabelle che condividono la velocità effettiva 400 ur/s a livello di spazio dei nomi. Copiare il modello e distribuirlo come illustrato di seguito oppure visitare la [raccolta di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) e distribuire dal portale di Azure. È anche possibile scaricare il modello nel computer locale o creare un nuovo modello e specificare il percorso locale con il parametro `--template-file`.

> [!NOTE]
> I nomi degli account devono essere minuscoli e < 44 caratteri.
> Per aggiornare ur/s, inviare nuovamente il modello con i valori di proprietà della velocità effettiva aggiornati.

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

Per distribuire il modello di Gestione risorse usando l'interfaccia della riga di comando di Azure, **copiare** lo script e selezionare **prova** per aprire Azure cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sulla shell, quindi scegliere **Incolla**:

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

Il comando `az cosmosdb show` Mostra l'account Azure Cosmos appena creato dopo che è stato effettuato il provisioning. Se si sceglie di usare una versione installata localmente dell'interfaccia della riga di comando di Azure invece di usare CloudShell, vedere l'articolo dell' [interfaccia della riga di comando di Azure (CLI)](/cli/azure/) .


## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

- [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
- [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelli di avvio rapido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Risolvere gli errori comuni di distribuzione di Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
