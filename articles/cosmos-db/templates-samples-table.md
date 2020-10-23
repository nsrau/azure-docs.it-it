---
title: Modelli di Resource Manager per l'API Tabella di Azure Cosmos DB
description: Usare i modelli di Azure Resource Manager per creare e configurare l'API Tabella di Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: c77c917cf6063b787dc2972f5ee7db3329e0e743
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284476"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Gestire le risorse dell'API Tabella di Azure Cosmos DB usando i modelli di Azure Resource Manager

Questo articolo illustra come usare i modelli di Azure Resource Manager per distribuire e gestire account, database e contenitori di Azure Cosmos DB.

Questo articolo contiene esempi solo per gli account dell'API Tabella. Per trovare esempi per account di altri tipi di API, vedere gli articoli relativi all'uso dei modelli di Azure Resource Manager con l'API di Azure Cosmos DB per [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md) e [SQL](manage-sql-with-resource-manager.md).

> [!IMPORTANT]
>
> * I nomi degli account sono limitati a 44 caratteri, tutti in minuscolo.
> * Per modificare i valori di velocità effettiva, ridistribuire il modello con UR/s aggiornati.
> * Quando si aggiungono o si rimuovono percorsi in un account Azure Cosmos, non è possibile modificare contemporaneamente altre proprietà. Queste operazioni devono essere eseguite separatamente.

Per creare una delle risorse di Azure Cosmos DB seguenti, copiare il modello di esempio seguente in un nuovo file JSON. Facoltativamente, è possibile creare un file JSON dei parametri da usare quando si distribuiscono più istanze della stessa risorsa con nomi e valori diversi. Per distribuire modelli di Azure Resource Manager, è possibile scegliere tra varie soluzioni, tra cui [portale di Azure](../azure-resource-manager/templates/deploy-portal.md), [interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

> [!TIP]
> Per abilitare la velocità effettiva condivisa durante l'uso dell'API Tabella, abilitare la velocità effettiva a livello di account nel portale di Azure.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>Account di Azure Cosmos per Tabella con velocità effettiva a scalabilità automatica

Questo modello creerà un account di Azure Cosmos per l'API Tabella con una tabella con velocità effettiva a scalabilità automatica. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>Account di Azure Cosmos per Tabella con velocità effettiva di provisioning standard

Questo modello creerà un account di Azure Cosmos per l'API Tabella con una tabella con velocità effettiva standard. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

* [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
* [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelli di avvio rapido di Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Risolvere errori comuni durante la distribuzione di Azure con Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
