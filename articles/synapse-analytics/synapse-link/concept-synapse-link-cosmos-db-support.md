---
title: Funzionalità supportate da Collegamento ad Azure Synapse (anteprima) per Azure Cosmos DB
description: Informazioni sull'elenco corrente di azioni supportate da Collegamento ad Azure Synapse per Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 0cdc9e242c2254cafaf0af75bcb8f8879cf3eb58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91287829"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Funzionalità supportate da Collegamento ad Azure Synapse (anteprima) per Azure Cosmos DB

Questo articolo descrive le funzionalità attualmente supportate in Collegamento ad Azure Synapse per Azure Cosmos DB.

## <a name="azure-synapse-support"></a>Supporto di Azure Synapse

In Azure Cosmos DB sono disponibili due tipi di contenitori:
* Contenitore HTAP: un contenitore con Collegamento a Synapse abilitato. Questo contenitore include sia l'archivio transazionale che l'archivio analitico. 
* Contenitore OLTP: un contenitore con collegamento Synaspe non è abilitato. Questo contenitore include solo l'archivio transazionale e nessun archivio analitico.

> [!IMPORTANT]
> Il collegamento a sinapsi di Azure per Azure Cosmos DB è attualmente supportato nelle aree di lavoro sinapsi per le quali non è abilitata la rete virtuale gestita. 

È possibile connettersi a un contenitore Azure Cosmos DB senza abilitare il collegamento sinapsi. In questo scenario, è possibile leggere/scrivere solo nell'archivio transazionale. Di seguito è riportato un elenco delle funzionalità attualmente supportate nel collegamento sinapsi per Azure Cosmos DB. 

| Category              | Descrizione |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL serverless](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Supporto di runtime** |Runtime di Azure sinapsi supportato per accedere a Azure Cosmos DB| ✓ | [Contatta](mailto:cosmosdbsynapselink@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB) |
| **Supporto dell'API di Azure Cosmos DB** | Tipo di API Azure Cosmos DB supportato | SQL/MongoDB | SQL/MongoDB |
| **Object**  |Oggetti, ad esempio una tabella che è possibile creare, che puntano direttamente al contenitore Azure Cosmos DB| Dataframe, vista, tabella | Visualizzazione |
| **Lettura**    | Tipo di contenitore di Azure Cosmos DB che è possibile leggere | OLTP/HTAP | HTAP  |
| **Scrittura**   | È possibile usare il runtime della sinapsi di Azure per scrivere i dati in un contenitore di Azure Cosmos DB | Sì | No |

* Se si scrivono dati in un contenitore Azure Cosmos DB da Spark, questo processo si verifica tramite l'archivio transazionale di Azure Cosmos DB. Avrà un effetto sulle prestazioni transazionali del Azure Cosmos DB tramite l'utilizzo di unità richiesta.
* L'integrazione del pool SQL tramite tabelle esterne non è attualmente supportata.
 
## <a name="supported-code-generated-actions-for-spark"></a>Azioni generate dal codice supportate per Spark

| Movimento              | Descrizione |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Caricamento nel dataframe** |Caricamento e lettura di dati in un dataframe Spark |✓| ✓ |
| **Creazione di una tabella Spark** |Creazione di una tabella che punta a un contenitore Azure Cosmos DB|✓| ✓ |
| **Scrittura di dataframe in un contenitore** |Scrittura di dati in un contenitore|✓| ✓ |
| **Caricamento di dataframe in streaming da un contenitore** |Streaming dei dati con il feed di modifiche di Azure Cosmos DB|✓| ✓ |
| **Scrittura di un dataframe in streaming in un contenitore** |Streaming dei dati con il feed di modifiche di Azure Cosmos DB|✓| ✓ |


## <a name="supported-code-generated-actions-for-sql-serverless"></a>Azioni generate dal codice per SQL serverless

| Movimento              | Descrizione |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Esplorare i dati** |Esplorare i dati da un contenitore con la nota sintassi T-SQL e l'inferenza automatica dello schema|X| ✓ |
| **Creazione di visualizzazioni e creazione di report di Business Intelligence** |Creare una vista SQL per avere accesso diretto a un contenitore per la Business Intelligence tramite la sinapsi SQL senza server |X| ✓ |
| **Join di origini dati diversi insieme ai dati di Cosmos DB** | Archiviare i risultati delle query che leggono i dati da contenitori di Cosmos DB insieme ai dati nell'archivio BLOB di Azure o Azure Data Lake Storage tramite CETAS |X| ✓ |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [connettersi a Collegamento a Synapse per Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Informazioni su come eseguire query sull'archivio analitico con Spark](how-to-query-analytical-store-spark.md)
