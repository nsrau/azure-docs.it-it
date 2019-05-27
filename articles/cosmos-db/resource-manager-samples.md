---
title: Modelli di Azure Resource Manager per Azure Cosmos DB
description: Usare i modelli Azure Resource Manager per creare e configurare Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 4a32b0497d2457a740e9c082f990bb9112208bfd
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969174"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelli di Azure Resource Manager per Azure Cosmos DB

Nelle tabelle seguenti includono i collegamenti ai modelli di Azure Resource Manager per Azure Cosmos DB:

## <a name="sql-core-api"></a>API SQL (Core)

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Azure Cosmos database, contenitore](manage-sql-with-resource-manager.md#create-resource) | Questo modello crea un account API SQL (Core) in due aree con multi-master abilitato. L'account Azure Cosmos avrà due contenitori che condividono la velocità effettiva a livello di database. |
|[Aggiornare la velocità effettiva (UR/sec) per un database](manage-sql-with-resource-manager.md#database-ru-update) | Questo modello consente di aggiornare la velocità effettiva per un database in un account API SQL (Core). |
|[Aggiornare una velocità effettiva (UR/sec) per un contenitore](manage-sql-with-resource-manager.md#container-ru-update) | Questo modello consente di aggiornare la velocità effettiva per un contenitore in un account API SQL (Core). |

## <a name="mongodb-api"></a>API di MongoDB

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Azure Cosmos database, raccolta](manage-mongodb-with-resource-manager.md#create-resource) | Questo modello crea un account usando l'API Azure Cosmos DB per MongoDB in due aree con multi-master abilitato. L'account Azure Cosmos avrà due contenitori che condividono la velocità effettiva a livello di database. |
|[Aggiornare la velocità effettiva (UR/sec) per un database](manage-mongodb-with-resource-manager.md#database-ru-update) | Questo modello consente di aggiornare la velocità effettiva per un database in un account API di MongoDB. |
|[Aggiornare la velocità effettiva (UR/sec) per una raccolta](manage-mongodb-with-resource-manager.md#collection-ru-update) | Questo modello consente di aggiornare la velocità effettiva per un contenitore in un account API di MongoDB. |

## <a name="cassandra-api"></a>API Cassandra

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Azure Cosmos, keyspace e tabella](manage-cassandra-with-resource-manager.md#create-resource) | Questo modello crea un account dell'API Cassandra in due aree con multi-master abilitato. L'account Azure Cosmos avrà due tabelle che condividono la velocità effettiva a livello di keyspace. |
|[Aggiornare una velocità effettiva (UR/sec) per un keyspace](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Questo modello consente di aggiornare la velocità effettiva per un keyspace in un account dell'API Cassandra. |
|[Aggiornare la velocità effettiva (UR/sec) per una tabella](manage-cassandra-with-resource-manager.md#table-ru-update) | Questo modello consente di aggiornare la velocità effettiva per una tabella in un account dell'API Cassandra. |

## <a name="gremlin-api"></a>API Gremlin

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Azure Cosmos, un database, un grafico](manage-gremlin-with-resource-manager.md#create-resource) | Questo modello crea un account dell'API Gremlin in due aree con multi-master abilitato. L'account Azure Cosmos avrà due grafici che condividono la velocità effettiva a livello di database. |
|[Aggiornare la velocità effettiva (UR/sec) per un database](manage-gremlin-with-resource-manager.md#database-ru-update) | Questo modello consente di aggiornare la velocità effettiva per un database in un account dell'API Gremlin. |
|[Aggiornare una velocità effettiva (UR/sec) per un grafico](manage-gremlin-with-resource-manager.md#graph-ru-update) | Questo modello consente di aggiornare la velocità effettiva per un grafico in un account dell'API Gremlin. |

## <a name="table-api"></a>API di tabella

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Azure Cosmos, tabella](manage-table-with-resource-manager.md#create-resource) | Questo modello crea un account dell'API Table in due aree con multi-master abilitato. L'account Azure Cosmos avrà una singola tabella. |
|[Aggiornare la velocità effettiva (UR/sec) per una tabella](manage-table-with-resource-manager.md#table-ru-update) | Questo modello consente di aggiornare la velocità effettiva per una tabella in un account API di tabella. |

> [!TIP]
> Per abilitare condivisa della velocità effettiva quando si usa l'API di tabella, abilitare la velocità effettiva a livello di account nel portale di Azure.

Visualizzare [riferimento ARM per Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) pagina per la documentazione di riferimento.