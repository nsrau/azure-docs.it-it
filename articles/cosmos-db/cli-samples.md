---
title: Esempi di interfaccia della riga di comando di Azure per Azure Cosmos DB
description: Esempi di interfaccia della riga di comando di Azure per Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 58460dffad3d44090644a544f4082b7727ece3f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840337"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Esempi dell'interfaccia della riga di comando di Azure Cosmos DB

La tabella seguente include collegamenti a esempi di script di interfaccia della riga di comando di Azure per Azure Cosmos DB. Usare i collegamenti a destra per passare agli esempi specifici delle API. Gli esempi comuni sono uguali per tutte le API. Per tutti i comandi dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili pagine di riferimento in [Informazioni di riferimento sull'interfaccia della riga di comando di Azure](/cli/azure/cosmosdb). Gli esempi di script dell'interfaccia della riga di comando per Azure Cosmos DB sono disponibili anche nel [repository GitHub dell'interfaccia della riga di comando per Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Per questi esempi è necessaria l'interfaccia della riga di comando di Azure versione 2.9.1 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

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

## <a name="core-sql-api-samples"></a>Esempi per l'API Core (SQL)

|Attività | Descrizione |
|---|---|
| [Creare un account, un database e un contenitore Azure Cosmos ](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account, un database e un contenitore Azure Cosmos DB per l'API Core (SQL). |
| [Creare un account, un database e un contenitore con scalabilità automatica Azure Cosmos](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account, un database e un contenitore con scalabilità automatica Azure Cosmos DB per l'API Core (SQL). |
| [Operazioni di velocità effettiva](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Eseguire operazioni di lettura, aggiornamento e migrazione tra la velocità effettiva con scalabilità automatica e la velocità effettiva standard in un database e in un contenitore.|
| [Bloccare le risorse per impedirne l'eliminazione](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedire l'eliminazione delle risorse tramite blocchi.|
|||

## <a name="mongodb-api-samples"></a>Esempi per l'API MongoDB

|Attività | Descrizione |
|---|---|
| [Creare un account, un database e una raccolta di Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account, un database e una raccolta di Azure Cosmos DB per l'API MongoDB. |
| [Creare un account, un database con scalabilità automatica e due raccolte con velocità effettiva condivisa Azure Cosmos](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account, un database con scalabilità automatica e due raccolte con velocità effettiva condivisa Azure Cosmos DB per l'API MongoDB. |
| [Operazioni di velocità effettiva](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Eseguire operazioni di lettura, aggiornamento e migrazione tra la velocità effettiva con scalabilità automatica e la velocità effettiva standard in un database e in una raccolta.|
| [Bloccare le risorse per impedirne l'eliminazione](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedire l'eliminazione delle risorse tramite blocchi.|
|||

## <a name="cassandra-api-samples"></a>Esempi per l'API Cassandra

|Attività | Descrizione |
|---|---|
| [Creare un account, un keyspace e una tabella di Azure Cosmos](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account, un keyspace e una tabella di Azure Cosmos DB per l'API Cassandra. |
| [Creare un account, un keyspace e una tabella con scalabilità automatica Azure Cosmos](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account, un keyspace e una tabella con scalabilità automatica Azure Cosmos DB per l'API Cassandra. |
| [Operazioni di velocità effettiva](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Eseguire operazioni di lettura, aggiornamento e migrazione tra la velocità effettiva con scalabilità automatica e la velocità effettiva standard in un keyspace e in una tabella.|
| [Bloccare le risorse per impedirne l'eliminazione](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedire l'eliminazione delle risorse tramite blocchi.|
|||

## <a name="gremlin-api-samples"></a>Esempi per l'API Gremlin

|Attività | Descrizione |
|---|---|
| [Creare un account, un database e un grafo di Azure Cosmos DB](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account, un database e un grafo di Azure Cosmos DB per l'API Gremlin. |
| [Creare un account, un database e un grafo con scalabilità automatica Azure Cosmos](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account, un database e un grafo con scalabilità automatica Azure Cosmos DB per l'API Gremlin. |
| [Operazioni di velocità effettiva](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Eseguire operazioni di lettura, aggiornamento e migrazione tra la velocità effettiva con scalabilità automatica e la velocità effettiva standard in un database e in un grafo.|
| [Bloccare le risorse per impedirne l'eliminazione](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedire l'eliminazione delle risorse tramite blocchi.|
|||

## <a name="table-api-samples"></a>Esempi per l'API Tabella

|Attività | Descrizione |
|---|---|
| [Creare un account e una tabella di Azure Cosmos](scripts/cli/table/create.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account e una tabella di Azure Cosmos DB per l'API Tabella. |
| [Creare un account e una tabella con scalabilità automatica Azure Cosmos](scripts/cli/table/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account e una tabella con scalabilità automatica Azure Cosmos DB per l'API Tabella. |
| [Operazioni di velocità effettiva](scripts/cli/table/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Eseguire operazioni di lettura, aggiornamento e migrazione tra la velocità effettiva con scalabilità automatica e la velocità effettiva standard in una tabella.|
| [Bloccare le risorse per impedirne l'eliminazione](scripts/cli/table/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedire l'eliminazione delle risorse tramite blocchi.|
|||
