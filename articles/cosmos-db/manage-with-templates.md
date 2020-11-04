---
title: Creare e gestire Azure Cosmos DB con modelli di Resource Manager
description: Usare i modelli di Azure Resource Manager per creare e configurare Azure Cosmos DB per l'API Core (SQL)
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 32c79360606a67cc7e85a43a226bb34c105d5b7a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333393"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Gestire le risorse dell'API Core (SQL) di Azure Cosmos DB con modelli di Azure Resource Manager
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo illustra come usare i modelli di Azure Resource Manager per distribuire e gestire account, database e contenitori di Azure Cosmos DB.

L'articolo contiene solo esempi di modelli di Azure Resource Manager per account API Core (SQL). Sono disponibili esempi di modello anche per API [Cassandra](templates-samples-cassandra.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](templates-samples-mongodb.md) e [Tabella](templates-samples-table.md).

> [!IMPORTANT]
>
> * I nomi degli account sono limitati a 44 caratteri, tutti in minuscolo.
> * Per modificare i valori di velocità effettiva, ridistribuire il modello con UR/s aggiornati.
> * Quando si aggiungono o si rimuovono percorsi in un account Azure Cosmos, non è possibile modificare contemporaneamente altre proprietà. Queste operazioni devono essere eseguite separatamente.
> * Non è possibile rinominare Azure Cosmos DB risorse perché questo viola il modo in cui Azure Resource Manager funziona con gli URI delle risorse.

Per creare una delle risorse di Azure Cosmos DB seguenti, copiare il modello di esempio seguente in un nuovo file JSON. Facoltativamente, è possibile creare un file JSON dei parametri da usare quando si distribuiscono più istanze della stessa risorsa con nomi e valori diversi. Per distribuire modelli di Azure Resource Manager, è possibile scegliere tra varie soluzioni, tra cui [portale di Azure](../azure-resource-manager/templates/deploy-portal.md), [interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Account Azure Cosmos con velocità effettiva a scalabilità automatica

Questo modello consente di creare un account Azure Cosmos in due aree con le opzioni per la coerenza e il failover, con database e contenitore configurati per la velocità effettiva a scalabilità automatica con la maggior parte delle opzioni di criteri abilitate. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>Account Azure Cosmos con archivio analitico

Questo modello consente di creare un account Azure Cosmos in un'area con un contenitore con la funzione di durata TTL dei dati analitici abilitata e opzioni per usare la velocità effettiva manuale o a scalabilità automatica. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-analytical-store%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-analytical-store/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>Account di Azure Cosmos con provisioning della velocità effettiva standard

Questo modello consente di creare un account Azure Cosmos in due aree con le opzioni per la coerenza e il failover, con database e contenitore configurati per la velocità effettiva standard con la maggior parte delle opzioni di criteri abilitate. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Contenitore Azure Cosmos DB con funzionalità lato server

Questo modello consente di creare un account, un database e un contenitore Azure Cosmos con una stored procedure, un trigger e una funzione definita dall'utente. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Account Azure Cosmos DB per il livello gratuito

Questo modello consente di creare un account e un database Azure Cosmos per il livello gratuito con una velocità effettiva che può essere condivisa con un massimo di 25 contenitori. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

* [Documentazione di Azure Resource Manager](../azure-resource-manager/index.yml)
* [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelli di avvio rapido di Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Risolvere errori comuni durante la distribuzione di Azure con Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)