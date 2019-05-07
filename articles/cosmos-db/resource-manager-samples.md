---
title: Modelli di Azure Resource Manager per Azure Cosmos DB
description: Usare i modelli Azure Resource Manager per creare e configurare Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: c907de019b64a6a9d03206514a1147fd43c78106
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078461"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelli di Azure Resource Manager per Azure Cosmos DB

Nella tabella seguente include collegamenti ai modelli di Azure Resource Manager per Azure Cosmos DB:

|**Tipo di API** | **collegamento all'esempio**| **Descrizione** |
|---|---| ---|
|API core (SQL)| [Creare un account Azure Cosmos DB (multi-master)](manage-sql-with-resource-manager.md) | Questo modello crea un account API SQL in due aree con multi-master abilitato. L'account Azure Cosmos avrà due contenitori che condividono la velocità effettiva a livello di database. |
| API di MongoDB | [Creare un account Azure Cosmos DB (multi-master)](manage-mongodb-with-resource-manager.md) | Questo modello crea un account usando l'API di Azure Cosmos DB per MongoDB in due aree con multi-master abilitato. L'account Azure Cosmos avrà due contenitori che condividono la velocità effettiva a livello di database. |
| API Cassandra | [Creare un account Azure Cosmos DB (multi-master)](manage-cassandra-with-resource-manager.md) | Questo modello crea un account dell'API Cassandra in due aree con multi-master abilitato. L'account Azure Cosmos avrà due tabelle che condividono la velocità effettiva a livello di keyspace. |
| API Gremlin| [Creare un account Azure Cosmos DB (multi-master)](manage-gremlin-with-resource-manager.md) | Questo modello crea un account dell'API Gremlin in due aree con multi-master abilitato. L'account Azure Cosmos avrà due grafici che condividono la velocità effettiva a livello di database. |
| API di tabella | [Creare un account Azure Cosmos DB (multi-master)](manage-table-with-resource-manager.md) | Questo modello crea un account dell'API Table in due aree con multi-master abilitato. L'account Azure Cosmos avrà una singola tabella. |

> [!TIP]
> Per abilitare condivisa della velocità effettiva quando si usa l'API di tabella, abilitare la velocità effettiva a livello di account nel portale di Azure.

Visualizzare [riferimento ARM per Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) pagina per la documentazione di riferimento.