---
title: Esempi di Azure PowerShell per l'API Core (SQL) di Azure Cosmos DB
description: Ottenere gli esempi di Azure PowerShell per eseguire attività comuni nell'API Core (SQL) di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: 95b3bdf7c0f232655b4f64f3700579f3672eb3b8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341978"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-core-sql-api"></a>Esempi di Azure PowerShell per l'API Core (SQL) di Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La tabella seguente contiene collegamenti a script di Azure PowerShell comunemente usati per Azure Cosmos DB. Usare i collegamenti a destra per passare agli esempi specifici delle API. Gli esempi comuni sono uguali per tutte le API. Per tutti i cmdlet di PowerShell per Azure Cosmos DB sono disponibili pagine di riferimento nelle [informazioni di riferimento su Azure PowerShell](/powershell/module/az.cosmosdb). Verificare regolarmente se sono disponibili aggiornamenti per `Az.CosmosDB`. È anche possibile creare una copia tramite fork di questi esempi di PowerShell per Cosmos DB dal repository GitHub, nella pagina degli [esempi di PowerShell per Cosmos DB in GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Per i cmdlet di PowerShell relativi ad altre API, vedere [Esempi di PowerShell per Cassandra](powershell-samples-cassandra.md), [Esempi di PowerShell per l'API MongoDB](powershell-samples-mongodb.md), [Esempi di PowerShell per Gremlin](powershell-samples-gremlin.md), [Esempi di PowerShell per Tabella](powershell-samples-table.md)

## <a name="common-samples"></a>Esempi comuni

|Attività | Descrizione |
|---|---|
|[Aggiornare un account](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aggiornare il livello di coerenza predefinito di un account Azure Cosmos DB. |
|[Aggiornare le aree di un account](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aggiornare le aree di un account Cosmos DB. |
|[Cambiare la priorità di failover o attivare un failover](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cambiare la priorità di failover a livello di area di un account Azure Cosmos o attivare un failover manuale. |
|[Chiavi dell'account o stringhe di connessione](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ottenere le chiavi primarie e secondarie e le stringhe di connessione oppure rigenerare la chiave di un account Azure Cosmos DB. |
|[Creare un account Cosmos con il firewall IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un account Azure Cosmos DB con il firewall IP abilitato. |
|||

## <a name="core-sql-api-samples"></a>Esempi per l'API Core (SQL)

|Attività | Descrizione |
|---|---|
|[Creare un account, un database e un contenitore](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un account, un database e un contenitore Azure Cosmos DB. |
|[Creare un account, un database e un contenitore con scalabilità automatica](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un account, un database e un contenitore con scalabilità automatica Azure Cosmos DB. |
|[Creare un contenitore con una chiave di partizione grande](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un contenitore con una chiave di partizione grande. |
|[Creare un contenitore senza criterio di indicizzazione](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Creare un contenitore Azure Cosmos con il criterio di indicizzazione disattivato.|
|[Elencare o ottenere database o contenitori](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elencare o ottenere database o contenitori. |
|[Operazioni di velocità effettiva](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operazioni di velocità effettiva per un database o un contenitore, incluse le operazioni get, update e migrate, tra la velocità effettiva con scalabilità automatica e la velocità effettiva standard. |
|[Bloccare le risorse per impedirne l'eliminazione](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Impedire l'eliminazione delle risorse tramite blocchi. |
|||
