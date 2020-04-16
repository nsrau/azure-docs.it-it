---
title: Modelli di Azure Resource Manager per Azure Cosmos DB
description: Usare i modelli di Azure Resource Manager per creare e configurare il database Cosmos di Azure.Use Azure Resource Manager templates to create and configure Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 1c4f1a1920d98052231eaa3a7d5c0454441c88ee
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390871"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelli di Azure Resource Manager per Azure Cosmos DB

Le tabelle seguenti includono collegamenti ai modelli di Azure Resource Manager per Azure Cosmos DB:The following tables include links to Azure Resource Manager templates for Azure Cosmos DB:

## <a name="sql-core-api"></a>API SQL (Core)

|**Modello**|**Descrizione**|
|---|---|
|[Creare un account, un database e un contenitore Azure Cosmos DB](manage-sql-with-resource-manager.md#create-resource) | Questo modello crea un account API SQL (Core) in due aree con due contenitori con velocità effettiva del database condivisa e un contenitore con velocità effettiva dedicata. La velocità effettiva può essere aggiornata inviando nuovamente il modello con il valore della proprietà della velocità effettiva aggiornata. |
|[Creare un account Cosmos di Azure, un database e un contenitore con una stored procedure, trigger e UDFCreate an Azure Cosmos account, database and container with a stored procedure, trigger and UDF](manage-sql-with-resource-manager.md#create-sproc) | Questo modello crea un account API SQL (Core) in due aree con una stored procedure, trigger e UDF per un contenitore. |
|[Creare un endpoint privato per un account Cosmos di Azure esistenteCreate a private endpoint for an existing Azure Cosmos account](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Questo modello crea un endpoint privato per un account API SQL Cosmos di Azure esistente in una rete virtuale esistente. |

## <a name="mongodb-api"></a>API di MongoDB

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Cosmos di Azure, un database, una raccoltaCreate an Azure Cosmos account, database, collection](manage-mongodb-with-resource-manager.md#create-resource) | Questo modello crea un account usando l'API del database di Azure Cosmos per MongoDB in due aree con più master abilitato. L'account Cosmos di Azure diverrà con due contenitori che condividono la velocità effettiva a livello di database. |

## <a name="cassandra-api"></a>API Cassandra

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Cosmos di Azure, uno spazio chiave, una tabellaCreate an Azure Cosmos account, keyspace, table](manage-cassandra-with-resource-manager.md#create-resource) | Questo modello crea un account API Cassandra in due aree con più master abilitato. L'account Cosmos di Azure disporrà di due tabelle che condividono la velocità effettiva a livello di spazio chiave. |

## <a name="gremlin-api"></a>API Gremlin

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Cosmos di Azure, un database, un graficoCreate an Azure Cosmos account, database, graph](manage-gremlin-with-resource-manager.md#create-resource) | Questo modello crea un account API Gremlin in due aree con più master abilitato. L'account Cosmos di Azure avrà due grafici che condividono la velocità effettiva a livello di database. |

## <a name="table-api"></a>API di tabella

|**Modello**|**Descrizione**|
|---| ---|
|[Creare un account Azure Cosmos, tabellaCreate an Azure Cosmos account, table](manage-table-with-resource-manager.md#create-resource) | Questo modello crea un account API Table in due aree con più master abilitato. L'account Cosmos di Azure avrà una singola tabella. |

> [!TIP]
> Per abilitare la velocità effettiva condivisa quando si usa l'API tabelle, abilitare la velocità effettiva a livello di account nel portale di Azure.To enable shared throughput when using Table API, enable account-level throughput in the Azure Portal.

Per la documentazione di riferimento, vedere Informazioni di riferimento su [Azure Resource Manager per](/azure/templates/microsoft.documentdb/allversions) la documentazione di riferimento.
