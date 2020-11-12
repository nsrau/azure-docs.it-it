---
title: Esempi dell'interfaccia della riga di comando di Azure per l'API Core (SQL) di Azure Cosmos DB
description: Esempi dell'interfaccia della riga di comando di Azure per l'API Core (SQL) di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7c7baf4de56edd25537f93095bfa6ae0402214e2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073180"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-core-sql-api"></a>Esempi dell'interfaccia della riga di comando di Azure per l'API Azure Cosmos DB Core (SQL)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La tabella seguente include collegamenti a esempi di script di interfaccia della riga di comando di Azure per Azure Cosmos DB. Usare i collegamenti a destra per passare agli esempi specifici delle API. Gli esempi comuni sono uguali per tutte le API. Per tutti i comandi dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili pagine di riferimento in [Informazioni di riferimento sull'interfaccia della riga di comando di Azure](/cli/azure/cosmosdb). Gli esempi di script dell'interfaccia della riga di comando per Azure Cosmos DB sono disponibili anche nel [repository GitHub dell'interfaccia della riga di comando per Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Per questi esempi è necessaria l'interfaccia della riga di comando di Azure versione 2.12.1 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Per gli esempi dell'interfaccia della riga di comando di Azure relativi ad altre API, vedere [Esempi dell'interfaccia della riga di comando per Cassandra](cli-samples-cassandra.md), [Esempi dell'interfaccia della riga di comando per l'API MongoDB](cli-samples-mongodb.md), [Esempi dell'interfaccia della riga di comando per Gremlin](cli-samples-gremlin.md), [Esempi dell'interfaccia della riga di comando per Tabella](cli-samples-table.md)

## <a name="common-samples"></a>Esempi comuni

Questi esempi si applicano a tutte le API di Azure Cosmos DB.

|Attività | Descrizione |
|---|---|
| [Aggiungere aree o eseguire il failover](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Aggiungere un'area, cambiare la priorità di failover, attivare un failover manuale.|
| [Chiavi dell'account e stringhe di connessione](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Elencare chiavi dell'account, chiavi di sola lettura e stringhe di connessione, rigenerare le chiavi.|
| [Proteggere un account con il firewall IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account Cosmos con il firewall IP configurato.|
| [Proteggere un nuovo account con gli endpoint di servizio](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account Cosmos e proteggerlo con gli endpoint di servizio.|
| [Proteggere un account esistente con gli endpoint di servizio](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Aggiornare un account Cosmos per proteggerlo con gli endpoint di servizio quando alla fine viene configurata la subnet.|
|||

## <a name="core-sql-api-samples"></a>Esempi per l'API Core (SQL)

|Attività | Descrizione |
|---|---|
| [Creare un account, un database e un contenitore Azure Cosmos ](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account, un database e un contenitore Azure Cosmos DB per l'API Core (SQL). |
| [Creare un account, un database e un contenitore con scalabilità automatica Azure Cosmos](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account, un database e un contenitore con scalabilità automatica Azure Cosmos DB per l'API Core (SQL). |
| [Operazioni di velocità effettiva](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Eseguire operazioni di lettura, aggiornamento e migrazione tra la velocità effettiva con scalabilità automatica e la velocità effettiva standard in un database e in un contenitore.|
| [Bloccare le risorse per impedirne l'eliminazione](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedire l'eliminazione delle risorse tramite blocchi.|
|||
