---
title: Creare e gestire Azure Cosmos DB con modelli di Gestione risorse
description: Usare modelli di Azure Resource Manager per creare e configurare Azure Cosmos DB per l'API di base (SQL)
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 577bc34e5e4b01a234460e5e175c23fd8215743f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791205"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Gestire Azure Cosmos DB risorse API Core (SQL) con modelli di Azure Resource Manager

Questo articolo illustra come usare i modelli di Azure Resource Manager per distribuire e gestire gli account, i database e i contenitori di Azure Cosmos DB.

Questo articolo illustra solo Azure Resource Manager esempi di modelli per gli account API di base (SQL). È anche possibile trovare esempi di modelli per le API [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)e [Table](manage-table-with-resource-manager.md) .

> [!IMPORTANT]
>
> * I nomi degli account sono limitati a 44 caratteri, tutti minuscoli.
> * Per modificare i valori di velocità effettiva, ridistribuire il modello con ur/sec aggiornati.
> * Quando si aggiungono o si rimuovono percorsi in un account Azure Cosmos, non è possibile modificare contemporaneamente altre proprietà. Queste operazioni devono essere eseguite separatamente.

Per creare una delle risorse Azure Cosmos DB di seguito, copiare il modello di esempio seguente in un nuovo file JSON. Facoltativamente, è possibile creare un file JSON dei parametri da usare quando si distribuiscono più istanze della stessa risorsa con nomi e valori diversi. Sono disponibili diversi modi per distribuire Azure Resource Manager modelli, tra cui [portale di Azure](../azure-resource-manager/templates/deploy-portal.md), l'interfaccia della riga di comando di [Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Account Azure Cosmos con velocità effettiva di scalabilità automatica

Questo modello crea un account Azure Cosmos in due aree con le opzioni per la coerenza e il failover, con database e contenitore configurati per la velocità effettiva di scalabilità automatica con la maggior parte delle opzioni di criteri abilitata. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta di modelli di avvio rapido di Azure.

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-manual-throughput"></a>Account Azure Cosmos con velocità effettiva standard (manuale)

Questo modello consente di creare un account Azure Cosmos in due aree con le opzioni per la coerenza e il failover, con database e contenitore configurati per la velocità effettiva standard con la maggior parte delle opzioni dei criteri abilitata. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta di modelli di avvio rapido di Azure.

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Azure Cosmos DB contenitore con funzionalità lato server

Questo modello consente di creare un account, un database e un contenitore Azure Cosmos con una stored procedure, un trigger e una funzione definita dall'utente. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta di modelli di avvio rapido di Azure.

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Account Azure Cosmos DB del livello gratuito

Questo modello consente di creare un account Azure Cosmos a livello gratuito e un database con una velocità effettiva condivisa che può essere condivisa con un massimo di 25 contenitori. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta di modelli di avvio rapido di Azure.

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

* [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
* [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelli di avvio rapido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Risolvere gli errori comuni di distribuzione di Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
