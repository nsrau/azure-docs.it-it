---
title: Modelli di Azure Resource Manager per Azure Cosmos DB
description: Usare i modelli di Azure Resource Manager per creare e configurare Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 501065875cafc035d491e606c016f3ad4dbfc5d4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791630"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelli di Azure Resource Manager per Azure Cosmos DB

Le tabelle seguenti includono collegamenti a modelli di Azure Resource Manager per Azure Cosmos DB:

## <a name="core-sql-api"></a>API Core (SQL)

|**Modello**|**Descrizione**|
|---|---|
|[Creare un account Azure Cosmos, un database, un contenitore con velocità effettiva di scalabilità automatica](manage-sql-with-resource-manager.md#create-autoscale) | Questo modello consente di creare un account API principale (SQL) in due aree, un database e un contenitore con velocità effettiva di ridimensionamento automatico. |
|[Creare un account Azure Cosmos, un database, un contenitore con velocità effettiva standard (manuale)](manage-sql-with-resource-manager.md#create-manual) | Questo modello consente di creare un account API principale (SQL) in due aree, un database e un contenitore con velocità effettiva standard. |
|[Creare un account, un database e un contenitore di Azure Cosmos con una stored procedure, un trigger e una funzione definita dall'utente](manage-sql-with-resource-manager.md#create-sproc) | Questo modello consente di creare un account API principale (SQL) in due aree con un stored procedure, un trigger e una funzione definita dall'utente per un contenitore. |
|[Creare un endpoint privato per un account Azure Cosmos esistente](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Questo modello crea un endpoint privato per un account API di Azure Cosmos Core (SQL) esistente in una rete virtuale esistente. |
|[Creare un account Azure Cosmos a livello gratuito](manage-sql-with-resource-manager.md#free-tier) |  Questo modello consente di creare un account API di Azure Cosmos DB Core (SQL) nel livello gratuito. |

## <a name="mongodb-api"></a>API di MongoDB

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Azure Cosmos, un database, una raccolta con velocità effettiva di scalabilità automatica](manage-mongodb-with-resource-manager.md#create-autoscale) | Questo modello crea un account usando Azure Cosmos DB API per MongoDB in due aree con due contenitori che condividono la velocità effettiva di scalabilità automatica a livello di database. |
|[Creare un account Azure Cosmos, un database, una raccolta con velocità effettiva standard (manuale)](manage-mongodb-with-resource-manager.md#create-manual) | Questo modello crea un account usando Azure Cosmos DB API per MongoDB in due aree con due contenitori che condividono la velocità effettiva standard a livello di database. |

## <a name="cassandra-api"></a>API Cassandra

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Azure Cosmos, spazio di spazio per le tabelle con velocità effettiva di scalabilità automatica](manage-cassandra-with-resource-manager.md#create-autoscale) | Questo modello consente di creare un account API Cassandra in due aree con spazio e tabella con una velocità effettiva di ridimensionamento automatico. |
|[Creare un account Azure Cosmos, spazio di spazio per le tabelle con velocità effettiva standard (manuale)](manage-cassandra-with-resource-manager.md#create-manual) | Questo modello consente di creare un account di API Cassandra in due aree con spazio e tabella con una velocità effettiva manuale. |

## <a name="gremlin-api"></a>API Gremlin

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account, un database e un grafo di Azure Cosmos con velocità effettiva di scalabilità automatica](manage-gremlin-with-resource-manager.md#create-autoscale) | Questo modello consente di creare un account API Gremlin in due aree con un database e un grafo con velocità effettiva di scalabilità automatica. |
|[Creare un account, un database, un grafo di Azure Cosmos con una velocità effettiva standard (manuale)](manage-gremlin-with-resource-manager.md#create-manual) | Questo modello consente di creare un account API Gremlin in due aree con un database e un grafo con velocità effettiva standard. |

## <a name="table-api"></a>API di tabella

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Azure Cosmos, tabella con velocità effettiva di scalabilità automatica](manage-table-with-resource-manager.md#create-autoscale) | Questo modello consente di creare un account API Tabella in due aree e una singola tabella con velocità effettiva di ridimensionamento automatico. |
|[Creare un account Azure Cosmos, tabella con velocità effettiva standard (manuale)](manage-table-with-resource-manager.md#create-manual) | Questo modello consente di creare un account API Tabella in due aree e una singola tabella con velocità effettiva standard. |

Per la documentazione di riferimento, vedere [Azure Resource Manager riferimento per Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) pagina.
