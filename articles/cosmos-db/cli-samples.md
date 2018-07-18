---
title: Esempi dell'interfaccia della riga di comando di Azure Cosmos DB | Documentazione Microsoft
description: Esempi dell'interfaccia della riga di comando di Azure - Creare e gestire account, database, contenitori, aree e firewall di Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/29/2017
ms.author: sngun
ms.openlocfilehash: 42cfe71210b95732b4b69f7ca21a8b647e187a38
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858861"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Esempi dell'interfaccia della riga di comando di Azure Cosmos DB

La tabella seguente include collegamenti a esempi di script di interfaccia della riga di comando di Azure per Azure Cosmos DB. Per tutti i comandi dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili pagine di riferimento in [Azure CLI 2.0 Reference](https://docs.microsoft.com/cli/azure/cosmosdb) (Informazioni di riferimento sull'interfaccia della riga di comando di Azure 2.0).

| |  |
|---|---|
|**Creare account di database e contenitori di Azure Cosmos DB**||
|[Creare un account API SQL](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un account, un database e un contenitore dell'API Azure Cosmos DB da usare con l'API SQL. |
| [Creare un account di API MongoDB](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un singolo account di API MongoDB, database e raccolta di Azure Cosmos DB. |
| [Creare un account API Gremlin](scripts/create-graph-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un singolo account di API Gremlin, database e contenitore di Azure Cosmos DB. |
|**Scalare Azure Cosmos DB**||
| [Scalare la velocità effettiva del contenitore](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Modifica la velocità effettiva con provisioning in un contenitore.|
|[Replicare l'account di database di Azure Cosmos DB in più aree e configurare le priorità di failover](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Replica a livello globale i dati dell'account in più aree con una priorità di failover specificata.|
|**Proteggere Azure Cosmos DB**||
| [Ottenere le chiavi dell'account](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ottiene le chiavi di scrittura master primarie e secondarie e le chiavi di sola lettura primarie e secondarie per l'account.|
| [Ottenere la stringa di connessione di MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ottiene la stringa di connessione per connettere l'app MongoDB all'account di Azure Cosmos DB.|
|[Rigenerare le chiavi dell'account](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Rigenera la chiave master o di sola lettura per l'account.|
|[Creare un firewall](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un criterio di controllo di accesso IP in ingresso per limitare l'accesso all'account da un set di macchine e/o servizi cloud approvati.|
|**Disponibilità elevata, ripristino di emergenza, backup e ripristino**||
|[Configurare i criteri di failover](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Imposta la priorità di failover di ogni area in cui l'account viene replicato.|
|**Connettere Azure Cosmos DB alle risorse**||
|[Connettere un'app Web ad Azure Cosmos DB](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Creare e collegare un database Azure Cosmos DB e un'app Web di Azure.|
|||
