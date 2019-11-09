---
title: Modelli di Azure Resource Manager per Azure Cosmos DB
description: Usare i modelli di Azure Resource Manager per creare e configurare Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: thvankra
ms.openlocfilehash: 7c508d736c7c04cc6df2b42e0a90a2906052adbc
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835527"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelli di Azure Resource Manager per Azure Cosmos DB

Le tabelle seguenti includono collegamenti a modelli di Azure Resource Manager per Azure Cosmos DB:

## <a name="sql-core-api"></a>API SQL (Core)

|**Modello**|**Descrizione**|
|---|---|
|[Creare un account Azure Cosmos, un database, un contenitore](manage-sql-with-resource-manager.md#create-resource) | Questo modello consente di creare un account API SQL (Core) in due aree con due contenitori con velocità effettiva del database condivisa e un contenitore con velocità effettiva dedicata. La velocità effettiva può essere aggiornata inviando nuovamente il modello con il valore della proprietà velocità effettiva aggiornata. |
|[Creare un account, un database e un contenitore di Azure Cosmos con una stored procedure, un trigger e una funzione definita dall'utente](manage-sql-with-resource-manager.md#create-sproc) | Questo modello consente di creare un account API SQL (Core) in due aree con un stored procedure, un trigger e una funzione definita dall'utente per un contenitore. |

## <a name="mongodb-api"></a>API di MongoDB

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account, un database e una raccolta di Azure Cosmos](manage-mongodb-with-resource-manager.md#create-resource) | Questo modello consente di creare un account usando Azure Cosmos DB API per MongoDB in due aree con l'abilitazione di più master. L'account Azure Cosmos avrà due contenitori che condividono la velocità effettiva a livello di database. |
|[Aggiornamento della velocità effettiva (UR/sec) per un database](manage-mongodb-with-resource-manager.md#database-ru-update) | Questo modello Aggiorna la velocità effettiva per un database in un account dell'API MongoDB. |
|[Aggiornamento della velocità effettiva (UR/sec) per una raccolta](manage-mongodb-with-resource-manager.md#collection-ru-update) | Questo modello Aggiorna la velocità effettiva per un contenitore in un account dell'API MongoDB. |

## <a name="cassandra-api"></a>API Cassandra

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Azure Cosmos, spazio per la tabella](manage-cassandra-with-resource-manager.md#create-resource) | Questo modello crea un account API Cassandra in due aree con la funzionalità multimaster abilitata. L'account Azure Cosmos disporrà di due tabelle che condividono la velocità effettiva a livello di spazio. |

## <a name="gremlin-api"></a>API Gremlin

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account, un database, un grafo di Azure Cosmos](manage-gremlin-with-resource-manager.md#create-resource) | Questo modello consente di creare un account API Gremlin in due aree con la funzionalità multimaster abilitata. L'account Azure Cosmos avrà due grafici che condividono la velocità effettiva a livello di database. |
|[Aggiornamento della velocità effettiva (UR/sec) per un database](manage-gremlin-with-resource-manager.md#database-ru-update) | Questo modello Aggiorna la velocità effettiva per un database in un account dell'API Gremlin. |
|[Aggiornamento della velocità effettiva (UR/sec) per un grafo](manage-gremlin-with-resource-manager.md#graph-ru-update) | Questo modello Aggiorna la velocità effettiva per un grafico in un account dell'API Gremlin. |

## <a name="table-api"></a>API di tabella

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Azure Cosmos, tabella](manage-table-with-resource-manager.md#create-resource) | Questo modello crea un account API Tabella in due aree con la funzionalità multimaster abilitata. L'account Azure Cosmos avrà una singola tabella. |
|[Aggiornamento della velocità effettiva (UR/sec) per una tabella](manage-table-with-resource-manager.md#table-ru-update) | Questo modello Aggiorna la velocità effettiva per una tabella in un account API Tabella. |

> [!TIP]
> Per abilitare la velocità effettiva condivisa quando si usa API Tabella, abilitare la velocità effettiva a livello di account nel portale di Azure.

Per la documentazione di riferimento, vedere [riferimento a ARM per](/azure/templates/microsoft.documentdb/allversions) la pagina Azure Cosmos DB.
