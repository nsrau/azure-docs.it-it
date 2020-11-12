---
title: Esempi di Azure PowerShell per l'API Tabella di Azure Cosmos DB
description: Ottenere gli esempi di Azure PowerShell per eseguire attività comuni nell'API Tabella di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: be0fd7db7b28ea9801e065f1327596021b3e31b1
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332900"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-table-api"></a>Esempi di Azure PowerShell per l'API Tabella di Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

La tabella seguente contiene collegamenti a script di Azure PowerShell comunemente usati per Azure Cosmos DB. Usare i collegamenti a destra per passare agli esempi specifici delle API. Gli esempi comuni sono uguali per tutte le API. Per tutti i cmdlet di PowerShell per Azure Cosmos DB sono disponibili pagine di riferimento nelle [informazioni di riferimento su Azure PowerShell](/powershell/module/az.cosmosdb). Verificare regolarmente se sono disponibili aggiornamenti per `Az.CosmosDB`. È anche possibile creare una copia tramite fork di questi esempi di PowerShell per Cosmos DB dal repository GitHub, nella pagina degli [esempi di PowerShell per Cosmos DB in GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Esempi comuni

|Attività | Descrizione |
|---|---|
|[Aggiornare un account](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aggiornare il livello di coerenza predefinito di un account Azure Cosmos DB. |
|[Aggiornare le aree di un account](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aggiornare le aree di un account Cosmos DB. |
|[Cambiare la priorità di failover o attivare un failover](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cambiare la priorità di failover a livello di area di un account Azure Cosmos o attivare un failover manuale. |
|[Chiavi dell'account o stringhe di connessione](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ottenere le chiavi primarie e secondarie e le stringhe di connessione oppure rigenerare la chiave di un account Azure Cosmos DB. |
|[Creare un account Cosmos con il firewall IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un account Azure Cosmos DB con il firewall IP abilitato. |
|||

## <a name="table-api-samples"></a>Esempi per l'API Tabella

|Attività | Descrizione |
|---|---|
|[Creare un account e una tabella](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un account e una tabella Azure Cosmos DB. |
|[Creare un account e una tabella con scalabilità automatica](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un account e una tabella con scalabilità automatica Azure Cosmos. |
|[Elencare o ottenere tabelle](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elencare o ottenere tabelle. |
|[Operazioni di velocità effettiva](scripts/powershell/table/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operazioni di velocità effettiva per una tabella, incluse le operazioni get, update e migrate, tra la velocità effettiva con scalabilità automatica e la velocità effettiva standard. |
|[Bloccare le risorse per impedirne l'eliminazione](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Impedire l'eliminazione delle risorse tramite blocchi. |
|||
