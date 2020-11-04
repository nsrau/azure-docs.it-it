---
title: Modelli di Resource Manager per l'API di Azure Cosmos DB per MongoDB
description: Usare i modelli di Azure Resource Manager per creare e configurare l'API di Azure Cosmos DB per MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 7664d48bad153b34e7557e9faaf4c8aa0d4215ad
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340626"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Gestire le risorse dell'API di Azure Cosmos DB per MongoDB con modelli di Azure Resource Manager
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Questo articolo illustra come usare i modelli di Azure Resource Manager per distribuire e gestire account di Azure Cosmos DB per l'API MongoDB, i database e le raccolte.

Questo articolo contiene esempi solo per l'API di Azure Cosmos DB per MongoDB. Per trovare esempi per account di altri tipi di API, vedere gli articoli relativi all'uso dei modelli di Azure Resource Manager con l'API di Azure Cosmos DB per [Cassandra](templates-samples-cassandra.md), [Gremlin](templates-samples-gremlin.md), [SQL](templates-samples-sql.md) e [Table](templates-samples-table.md).

> [!IMPORTANT]
>
> * I nomi degli account sono limitati a 44 caratteri, tutti in minuscolo.
> * Per modificare i valori di velocità effettiva, ridistribuire il modello con UR/s aggiornati.
> * Quando si aggiungono o si rimuovono percorsi in un account Azure Cosmos, non è possibile modificare contemporaneamente altre proprietà. Queste operazioni devono essere eseguite separatamente.

Per creare una delle risorse di Azure Cosmos DB seguenti, copiare il modello di esempio seguente in un nuovo file JSON. Facoltativamente, è possibile creare un file JSON dei parametri da usare quando si distribuiscono più istanze della stessa risorsa con nomi e valori diversi. Per distribuire modelli di Azure Resource Manager, è possibile scegliere tra varie soluzioni, tra cui [portale di Azure](../azure-resource-manager/templates/deploy-portal.md), [interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>Account Azure Cosmos per MongoDB con velocità effettiva con provisioning a scalabilità automatica

Questo modello creerà un account Azure Cosmos per l'API MongoDB (3.2 o 3.6) con due raccolte che condividono la velocità effettiva a scalabilità automatica a livello di database. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>Account di Azure Cosmos per MongoDB con velocità effettiva di provisioning standard

Questo modello creerà un account Azure Cosmos per l'API MongoDB (3.2 o 3.6) con due raccolte che condividono una velocità effettiva standard (manuale) di 400 RU/s a livello di database. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

* [Documentazione di Azure Resource Manager](../azure-resource-manager/index.yml)
* [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelli di avvio rapido di Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Risolvere errori comuni durante la distribuzione di Azure con Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)