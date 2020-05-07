---
title: Modelli di Gestione risorse per Azure Cosmos DB API Gremlin
description: Usare i modelli di Azure Resource Manager per creare e configurare Azure Cosmos DB API Gremlin.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: defeec2317f47e9d79245cbde0ef81c8a3543bfb
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791239"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Gestire Azure Cosmos DB risorse dell'API Gremlin usando modelli di Azure Resource Manager

In questo articolo si apprenderà come usare i modelli di Azure Resource Manager per semplificare la distribuzione e la gestione di account Azure Cosmos DB, database e grafici.

Questo articolo contiene esempi per gli account dell'API Gremlin, per trovare esempi per altri account di tipo API, vedere: usare Azure Resource Manager modelli con l'API Azure Cosmos DB per [Cassandra](manage-cassandra-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), articoli di [tabella](manage-table-with-resource-manager.md) .

> [!IMPORTANT]
>
> * I nomi degli account sono limitati a 44 caratteri, tutti minuscoli.
> * Per modificare i valori di velocità effettiva, ridistribuire il modello con ur/sec aggiornati.
> * Quando si aggiungono o si rimuovono percorsi in un account Azure Cosmos, non è possibile modificare contemporaneamente altre proprietà. Queste operazioni devono essere eseguite separatamente.

Per creare una delle risorse Azure Cosmos DB di seguito, copiare il modello di esempio seguente in un nuovo file JSON. Facoltativamente, è possibile creare un file JSON dei parametri da usare quando si distribuiscono più istanze della stessa risorsa con nomi e valori diversi. Sono disponibili diversi modi per distribuire Azure Resource Manager modelli, tra cui [portale di Azure](../azure-resource-manager/templates/deploy-portal.md), l'interfaccia della riga di comando di [Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-autoscale-provisioned-throughput"></a>Azure Cosmos DB account per Gremlin con la velocità effettiva con provisioning con scalabilità automatica

Questo modello creerà un account Azure Cosmos per l'API Gremlin con un database e un grafo con velocità effettiva di ridimensionamento automatico.

Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta di modelli di avvio rapido di Azure.

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-standard-provisioned-throughput"></a>Azure Cosmos DB account per Gremlin con velocità effettiva con provisioning standard

Questo modello creerà un account Azure Cosmos per l'API Gremlin con un database e un grafo con velocità effettiva standard (manuale).

Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta di modelli di avvio rapido di Azure.

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json":::

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

* [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
* [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelli di avvio rapido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Risolvere gli errori comuni di distribuzione di Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)