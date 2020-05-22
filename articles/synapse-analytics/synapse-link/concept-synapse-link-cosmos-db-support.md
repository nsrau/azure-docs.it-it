---
title: Funzionalità supportate da Collegamento ad Azure Synapse per Azure Cosmos DB
description: Informazioni sull'elenco corrente di azioni supportate da Collegamento ad Azure Synapse per Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 1b94d7677026f3695d07be4d83a5059373078c2e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599033"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Funzionalità supportate da Collegamento ad Azure Synapse per Azure Cosmos DB

Questo articolo descrive le funzionalità attualmente supportate in Collegamento ad Azure Synapse per Azure Cosmos DB. 

## <a name="azure-synapse-support"></a>Supporto di Azure Synapse

In Azure Cosmos DB sono disponibili due tipi di contenitori:
* Contenitore HTAP: un contenitore con Collegamento a Synapse abilitato. Questo contenitore include sia l'archivio transazionale che l'archivio analitico. 
* Contenitore OLTP: un contenitore che include solo l'archivio transazionale; Collegamento a Synapse non è abilitato. 

È possibile connettersi a un contenitore Azure Cosmos DB senza abilitare Collegamento a Synapse, nel qual caso l'archivio transazionale sarà disponibile solo in lettura/scrittura. Di seguito è riportato un elenco delle funzionalità attualmente supportate all'interno di Collegamento a Synapse per Azure Cosmos DB. 

| Category              | Descrizione |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL serverless](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **Supporto di runtime** |Supporto per la lettura o la scrittura dal runtime di Azure Synapse| ✓ | [Contatta](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **Supporto dell'API di Azure Cosmos DB** |Supporto dell'API come Collegamento a Synapse| SQL/MongoDB | SQL/MongoDB |
| **Object**  |Oggetti, ad esempio una tabella che è possibile creare, che puntano direttamente al contenitore Azure Cosmos DB| Vista, tabella | Visualizzazione |
| **Lettura**    |Lettura di dati da un contenitore Azure Cosmos DB| OLTP/HTAP | HTAP  |
| **Scrittura**   |Scrittura di dati dal runtime in un contenitore Azure Cosmos DB| OLTP | n/d |

* Se si scrivono dati in un contenitore Azure Cosmos DB da Spark, questo processo viene eseguito tramite l'archivio transazionale di Azure Cosmos DB e influirà sulle prestazioni transazionali di Azure Cosmos DB consumando unità richiesta.
* L'integrazione del pool SQL tramite tabelle esterne non è attualmente supportata.

## <a name="supported-code-generated-actions-for-spark"></a>Azioni generate dal codice supportate per Spark

| Movimento              | Descrizione |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Caricamento nel dataframe** |Caricamento e lettura di dati in un dataframe Spark |X| ✓ |
| **Creazione di una tabella Spark** |Creazione di una tabella che punta a un contenitore Azure Cosmos DB|X| ✓ |
| **Scrittura di dataframe in un contenitore** |Scrittura di dati in un contenitore|✓| ✓ |
| **Caricamento di dataframe in streaming da un contenitore** |Streaming dei dati con il feed di modifiche di Azure Cosmos DB|✓| ✓ |
| **Scrittura di un dataframe in streaming in un contenitore** |Streaming dei dati con il feed di modifiche di Azure Cosmos DB|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>Azioni generate dal codice per SQL serverless

| Movimento              | Descrizione |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Selezione dei primi 100** |Anteprima dei primi 100 elementi di un contenitore|X| ✓ |
| **Creazione di una vista** |Creazione di una vista per avere accesso a BI direttamente in un contenitore tramite Synapse SQL|X| ✓ |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [connettersi a Collegamento a Synapse per Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Informazioni su come eseguire query sull'archivio analitico con Spark](how-to-query-analytical-store-spark.md)