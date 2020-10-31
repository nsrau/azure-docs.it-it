---
title: Modelli di Azure Resource Manager per Azure Cosmos DB Core (API SQL)
description: Usare i modelli di Azure Resource Manager per creare e configurare Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 7c060b20920ec17e5de0d10ec74b5619b1575ae2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079394"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelli di Azure Resource Manager per Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

L'articolo contiene solo esempi di modelli di Azure Resource Manager per account API Core (SQL). Sono disponibili esempi di modello anche per API [Cassandra](templates-samples-cassandra.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](templates-samples-mongodb.md) e [Tabella](templates-samples-table.md).

## <a name="core-sql-api"></a>API Core (SQL)

|**Modello**|**Descrizione**|
|---|---|
|[Creare un account, un database e un contenitore Azure Cosmos con velocità effettiva a scalabilità automatica](manage-with-templates.md#create-autoscale) | Questo modello consente di creare un account API Core (SQL) in due aree, un database e un contenitore con velocità effettiva a scalabilità automatica. |
|[Creare un account, un database e un contenitore Azure Cosmos con l'archivio analitico](manage-with-templates.md#create-analytical-store) | Questo modello consente di creare un account API (SQL) Core in un'area con un contenitore configurato con la funzione di durata TTL dei dati analitici abilitata e l'opzione per usare la velocità effettiva manuale o a scalabilità automatica. |
|[Creare un account, un database e un contenitore Azure Cosmos con velocità effettiva standard (manuale)](manage-with-templates.md#create-manual) | Questo modello consente di creare un account API Core (SQL) in due aree, un database e un contenitore con velocità effettiva standard. |
|[Creare un account, un database e un contenitore Azure Cosmos con una stored procedure, un trigger e una funzione definita dall'utente](manage-with-templates.md#create-sproc) | Questo modello consente di creare un account API Core (SQL) in due aree con una stored procedure, un trigger e una funzione definita dall'utente per un contenitore. |
|[Creare un endpoint privato per un account Azure Cosmos esistente](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Questo modello consente di creare un endpoint privato per un account API Core (SQL) Azure Cosmos DB esistente in una rete virtuale esistente. |
|[Creare un account Azure Cosmos per il livello gratuito](manage-with-templates.md#free-tier) |  Questo modello consente di creare un account API Core (SQL) Azure Cosmos DB per il livello gratuito. |

Per la documentazione di riferimento, vedere la pagina [Informazioni di riferimento di Azure Resource Manager per Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions).
