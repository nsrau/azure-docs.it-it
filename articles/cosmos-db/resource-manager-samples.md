---
title: Modelli di Azure Resource Manager per Azure Cosmos DB
description: Usare i modelli di Azure Resource Manager per creare e configurare Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: a98aee61c3a330344b5b7fa0a5f7f53a65e5ecdb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586151"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelli di Azure Resource Manager per Azure Cosmos DB

Le tabelle seguenti includono i collegamenti ai modelli di Azure Resource Manager per Azure Cosmos DB:

## <a name="core-sql-api"></a>API Core (SQL)

|**Modello**|**Descrizione**|
|---|---|
|[Creare un account, un database e un contenitore Azure Cosmos con velocità effettiva a scalabilità automatica](manage-sql-with-resource-manager.md#create-autoscale) | Questo modello consente di creare un account API Core (SQL) in due aree, un database e un contenitore con velocità effettiva a scalabilità automatica. |
|[Creare un account, un database e un contenitore Azure Cosmos con l'archivio analitico](manage-sql-with-resource-manager.md#create-analytical-store) | Questo modello consente di creare un account API (SQL) Core in un'area con un contenitore configurato con la funzione di durata TTL dei dati analitici abilitata e l'opzione per usare la velocità effettiva manuale o a scalabilità automatica. |
|[Creare un account, un database e un contenitore Azure Cosmos con velocità effettiva standard (manuale)](manage-sql-with-resource-manager.md#create-manual) | Questo modello consente di creare un account API Core (SQL) in due aree, un database e un contenitore con velocità effettiva standard. |
|[Creare un account, un database e un contenitore Azure Cosmos con una stored procedure, un trigger e una funzione definita dall'utente](manage-sql-with-resource-manager.md#create-sproc) | Questo modello consente di creare un account API Core (SQL) in due aree con una stored procedure, un trigger e una funzione definita dall'utente per un contenitore. |
|[Creare un endpoint privato per un account Azure Cosmos esistente](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Questo modello consente di creare un endpoint privato per un account API Core (SQL) Azure Cosmos DB esistente in una rete virtuale esistente. |
|[Creare un account Azure Cosmos per il livello gratuito](manage-sql-with-resource-manager.md#free-tier) |  Questo modello consente di creare un account API Core (SQL) Azure Cosmos DB per il livello gratuito. |

## <a name="mongodb-api"></a>API di MongoDB

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account, un database e una raccolta Azure Cosmos con velocità effettiva a scalabilità automatica](manage-mongodb-with-resource-manager.md#create-autoscale) | Questo modello consente di creare un account usando l'API Azure Cosmos DB per MongoDB in due aree con due contenitori che condividono la velocità effettiva a scalabilità automatica a livello di database. |
|[Creare un account, un database e una raccolta Azure Cosmos con velocità effettiva standard (manuale)](manage-mongodb-with-resource-manager.md#create-manual) | Questo modello consente di creare un account usando l'API Azure Cosmos DB per MongoDB in due aree con due contenitori che condividono la velocità effettiva standard a livello di database. |

## <a name="cassandra-api"></a>API Cassandra

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account, un keyspace, e una tabella Azure Cosmos con velocità effettiva a scalabilità automatica](manage-cassandra-with-resource-manager.md#create-autoscale) | Questo modello consente di creare un account API Cassandra in due aree, con un keyspace e una tabella, con velocità effettiva a scalabilità automatica. |
|[Creare un account, un keyspace, e una tabella Azure Cosmos con velocità effettiva standard (manuale)](manage-cassandra-with-resource-manager.md#create-manual) | Questo modello consente di creare un account API Cassandra in due aree, con un keyspace e una tabella, con velocità effettiva manuale. |

## <a name="gremlin-api"></a>API Gremlin

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account, un database e un grafo Azure Cosmos con velocità effettiva a scalabilità automatica](manage-gremlin-with-resource-manager.md#create-autoscale) | Questo modello consente di creare un account API Gremlin in due aree, con un database e un grafo, con velocità effettiva a scalabilità automatica. |
|[Creare un account, un database e un grafo Azure Cosmos con velocità effettiva standard (manuale)](manage-gremlin-with-resource-manager.md#create-manual) | Questo modello consente di creare un account API Gremlin in due aree, con un database e un grafo, con velocità effettiva standard. |

## <a name="table-api"></a>API di tabella

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account e una tabella Azure Cosmos con velocità effettiva a scalabilità automatica](manage-table-with-resource-manager.md#create-autoscale) | Questo modello consente di creare un account API Tabella in due aree e una singola tabella con velocità effettiva a scalabilità automatica. |
|[Creare un account e una tabella Azure Cosmos con velocità effettiva standard (manuale)](manage-table-with-resource-manager.md#create-manual) | Questo modello consente di creare un account API Tabella in due aree e una singola tabella con velocità effettiva standard. |

Per la documentazione di riferimento, vedere la pagina [Informazioni di riferimento di Azure Resource Manager per Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions).
