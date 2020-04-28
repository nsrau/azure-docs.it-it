---
title: Modelli di Azure Resource Manager per Azure Cosmos DB
description: Usare i modelli di Azure Resource Manager per creare e configurare Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 14c3438c5d6e7bed110f9e0dbfcd04655c3e9121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193259"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelli di Azure Resource Manager per Azure Cosmos DB

Le tabelle seguenti includono collegamenti a modelli di Azure Resource Manager per Azure Cosmos DB:

## <a name="sql-core-api"></a>API SQL (Core)

|**Modello**|**Descrizione**|
|---|---|
|[Creare un account, un database e un contenitore Azure Cosmos DB](manage-sql-with-resource-manager.md#create-resource) | Questo modello consente di creare un account API SQL (Core) in due aree con due contenitori con velocità effettiva del database condivisa e un contenitore con velocità effettiva dedicata. La velocità effettiva può essere aggiornata inviando nuovamente il modello con il valore della proprietà velocità effettiva aggiornata. |
|[Creare un account, un database e un contenitore di Azure Cosmos con una stored procedure, un trigger e una funzione definita dall'utente](manage-sql-with-resource-manager.md#create-sproc) | Questo modello consente di creare un account API SQL (Core) in due aree con un stored procedure, un trigger e una funzione definita dall'utente per un contenitore. |
|[Creare un endpoint privato per un account Azure Cosmos esistente](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Questo modello crea un endpoint privato per un account API di Azure Cosmos SQL esistente in una rete virtuale esistente. |
|[Creare un account Azure Cosmos a livello gratuito](manage-sql-with-resource-manager.md#free-tier) |  Questo modello consente di creare un account di Azure Cosmos DB per il livello gratuito. |

## <a name="mongodb-api"></a>API di MongoDB

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account, un database e una raccolta di Azure Cosmos](manage-mongodb-with-resource-manager.md#create-resource) | Questo modello consente di creare un account usando Azure Cosmos DB API per MongoDB in due aree con l'abilitazione di più master. L'account Azure Cosmos avrà due contenitori che condividono la velocità effettiva a livello di database. |

## <a name="cassandra-api"></a>API Cassandra

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Azure Cosmos, spazio per la tabella](manage-cassandra-with-resource-manager.md#create-resource) | Questo modello crea un account API Cassandra in due aree con la funzionalità multimaster abilitata. L'account Azure Cosmos disporrà di due tabelle che condividono la velocità effettiva a livello di spazio. |

## <a name="gremlin-api"></a>API Gremlin

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account, un database, un grafo di Azure Cosmos](manage-gremlin-with-resource-manager.md#create-resource) | Questo modello consente di creare un account API Gremlin in due aree con la funzionalità multimaster abilitata. L'account Azure Cosmos avrà due grafici che condividono la velocità effettiva a livello di database. |

## <a name="table-api"></a>API di tabella

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Azure Cosmos, tabella](manage-table-with-resource-manager.md#create-resource) | Questo modello crea un account API Tabella in due aree con la funzionalità multimaster abilitata. L'account Azure Cosmos avrà una singola tabella. |

> [!TIP]
> Per abilitare la velocità effettiva condivisa quando si usa API Tabella, abilitare la velocità effettiva a livello di account nel portale di Azure.

Per la documentazione di riferimento, vedere [Azure Resource Manager riferimento per Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) pagina.
