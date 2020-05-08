---
title: Modelli di Gestione risorse per l'API Azure Cosmos DB per MongoDB
description: Usare i modelli di Azure Resource Manager per creare e configurare l'API Azure Cosmos DB per MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: d6f916002f949f78e4854903940f342261a109ff
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791222"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Gestire Azure Cosmos DB risorse API MongoDB usando modelli di Azure Resource Manager

Questo articolo illustra come usare i modelli di Azure Resource Manager per distribuire e gestire gli account di Azure Cosmos DB per l'API, i database e le raccolte MongoDB.

Questo articolo contiene esempi per l'API di Azure Cosmos DB solo per MongoDB, per trovare esempi per altri account di tipo API, vedere: usare Azure Resource Manager modelli con l'API di Azure Cosmos DB per [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), articoli di [tabella](manage-table-with-resource-manager.md) .

> [!IMPORTANT]
>
> * I nomi degli account sono limitati a 44 caratteri, tutti minuscoli.
> * Per modificare i valori di velocità effettiva, ridistribuire il modello con ur/sec aggiornati.
> * Quando si aggiungono o si rimuovono percorsi in un account Azure Cosmos, non è possibile modificare contemporaneamente altre proprietà. Queste operazioni devono essere eseguite separatamente.

Per creare una delle risorse Azure Cosmos DB di seguito, copiare il modello di esempio seguente in un nuovo file JSON. Facoltativamente, è possibile creare un file JSON dei parametri da usare quando si distribuiscono più istanze della stessa risorsa con nomi e valori diversi. Sono disponibili diversi modi per distribuire Azure Resource Manager modelli, tra cui [portale di Azure](../azure-resource-manager/templates/deploy-portal.md), l'interfaccia della riga di comando di [Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>Account Azure Cosmos per MongoDB con scalabilità automatica con provisioning

Questo modello creerà un account Azure Cosmos per l'API MongoDB (3,2 o 3,6) con due raccolte che condividono la velocità effettiva di scalabilità automatica a livello di database. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta di modelli di avvio rapido di Azure.

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>Account Azure Cosmos per MongoDB con velocità effettiva con provisioning standard

Questo modello creerà un account Azure Cosmos per l'API MongoDB (3,2 o 3,6) con due raccolte che condividono la velocità effettiva 400 ur/s standard (manuale) a livello di database. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta di modelli di avvio rapido di Azure.

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

* [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/)
* [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelli di avvio rapido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Risolvere gli errori comuni di distribuzione di Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
