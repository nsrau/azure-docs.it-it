---
title: Esempi di Azure PowerShell per Azure Cosmos DB - API SQL (Core)
description: Ottenere gli esempi di Azure PowerShell per eseguire varie attività comuni negli account dell'API SQL per Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 7cd14151d8cb0fbef7593d62eee050be7987ab18
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713540"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Esempi di Azure PowerShell per Azure Cosmos DB - API SQL (Core)

La tabella seguente include collegamenti a esempi comuni di script di Azure PowerShell per Azure Cosmos DB per l'API SQL (Core). Se si vuole creare una copia tramite fork di questi esempi di PowerShell per Cosmos DB dal repository GitHub, visitare la pagina di [esempi di Cosmos DB PowerShell in GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Per altri esempi e per la documentazione di Cosmos DB PowerShell per l'API SQL (Core), vedere [Gestire le risorse dell'API SQL di Azure Cosmos DB tramite PowerShell](manage-with-powershell.md). Per alcuni esempi di Cosmos DB PowerShell per altre API, vedere [API Cassandra](powershell-samples-cassandra.md), [API MongoDB](powershell-samples-mongodb.md), [API Gremlin](powershell-samples-gremlin.md) e [API Tabella](powershell-samples-table.md).

> [!NOTE]
> Gli esempi usano i cmdlet di gestione di [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb). Verificare regolarmente se sono disponibili aggiornamenti per `Az.CosmosDB`.

| | |
|---|---|
|[Creare un account, un database e un contenitore](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un account, un database e un contenitore Azure Cosmos DB. |
|[Creare un contenitore con una chiave di partizione grande](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un contenitore con una chiave di partizione grande. |
|[Elencare o ottenere database o contenitori](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elencare o ottenere database o contenitori. |
|[Ottenere le unità richiesta al secondo](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ottenere le unità richiesta al secondo per un database o un contenitore. |
|[Aggiornare le unità richiesta al secondo](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aggiornare le unità richiesta al secondo per un database o un contenitore. |
|[Creare un contenitore senza criterio di indicizzazione](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Creare un contenitore Azure Cosmos con il criterio di indicizzazione disattivato.|
|[Aggiornare un account](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aggiornare il livello di coerenza predefinito di un account Azure Cosmos DB. |
|[Aggiornare le aree di un account](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aggiornare le aree di un account Cosmos DB. |
|[Cambiare la priorità di failover o attivare un failover](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cambiare la priorità di failover a livello di area di un account Azure Cosmos o attivare un failover manuale. |
|[Chiavi dell'account o stringhe di connessione](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ottenere le chiavi primarie e secondarie e le stringhe di connessione oppure rigenerare la chiave di un account Azure Cosmos DB. |
|[Creare un account Cosmos con il firewall IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un account Azure Cosmos DB con il firewall IP abilitato. |
|||
