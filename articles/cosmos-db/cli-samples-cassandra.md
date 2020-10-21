---
title: Esempi dell'interfaccia della riga di comando di Azure per Azure Cosmos DB API Cassandra
description: Esempi dell'interfaccia della riga di comando di Azure per Azure Cosmos DB API Cassandra
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 574b7728bf39fa0fd91f24b3cf07b00a688b5147
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276992"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-cassandra-api"></a>Esempi dell'interfaccia della riga di comando di Azure per l'API Cassandra di Azure Cosmos DB

La tabella seguente include collegamenti a esempi di script di interfaccia della riga di comando di Azure per Azure Cosmos DB. Usare i collegamenti a destra per passare agli esempi specifici delle API. Gli esempi comuni sono uguali per tutte le API. Per tutti i comandi dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili pagine di riferimento in [Informazioni di riferimento sull'interfaccia della riga di comando di Azure](/cli/azure/cosmosdb). Gli esempi di script dell'interfaccia della riga di comando per Azure Cosmos DB sono disponibili anche nel [repository GitHub dell'interfaccia della riga di comando per Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Questi esempi richiedono l'interfaccia della riga di comando di Azure versione 2.12.1 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="common-samples"></a>Esempi comuni

Questi esempi si applicano a tutte le API di Azure Cosmos DB.

|Attività | Descrizione |
|---|---|
| [Aggiungere aree o eseguire il failover](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Aggiungere un'area, cambiare la priorità di failover, attivare un failover manuale.|
| [Chiavi dell'account e stringhe di connessione](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Elencare chiavi dell'account, chiavi di sola lettura e stringhe di connessione, rigenerare le chiavi.|
| [Proteggere un account con il firewall IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account Cosmos con il firewall IP configurato.|
| [Proteggere un nuovo account con gli endpoint di servizio](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account Cosmos e proteggerlo con gli endpoint di servizio.|
| [Proteggere un account esistente con gli endpoint di servizio](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Aggiornare un account Cosmos per proteggerlo con gli endpoint di servizio quando alla fine viene configurata la subnet.|
|||

## <a name="cassandra-api-samples"></a>Esempi per l'API Cassandra

|Attività | Descrizione |
|---|---|
| [Creare un account, un keyspace e una tabella di Azure Cosmos](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account, un keyspace e una tabella di Azure Cosmos DB per l'API Cassandra. |
| [Creare un account, un keyspace e una tabella con scalabilità automatica Azure Cosmos](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account, un keyspace e una tabella con scalabilità automatica Azure Cosmos DB per l'API Cassandra. |
| [Operazioni di velocità effettiva](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Eseguire operazioni di lettura, aggiornamento e migrazione tra la velocità effettiva con scalabilità automatica e la velocità effettiva standard in un keyspace e in una tabella.|
| [Bloccare le risorse per impedirne l'eliminazione](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedire l'eliminazione delle risorse tramite blocchi.|
|||
