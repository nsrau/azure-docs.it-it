---
title: Risorse per lo sviluppo di funzionalità SQL sinapsi
description: Concetti di sviluppo, decisioni di progettazione, consigli e tecniche di codifica per sinapsi SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: d47b4847a12b63532e44a8a1a47101dd065f811b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446597"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Decisioni di progettazione e tecniche di codifica per le funzionalità di SQL sinapsi in Azure sinapsi Analytics
In questo articolo è presente un elenco di risorse per il pool SQL dedicato e le funzioni del pool SQL senza server di sinapsi SQL. Gli articoli consigliati sono suddivisi in due sezioni: decisioni di progettazione chiave e tecniche di sviluppo e codifica.

L'obiettivo di questi articoli è aiutare a sviluppare l'approccio tecnico ottimale per i componenti di SQL sinapsi in Azure sinapsi Analytics.

## <a name="key-design-decisions"></a>Decisioni chiave nella progettazione
Gli articoli seguenti evidenziano i concetti e le decisioni di progettazione per lo sviluppo di sinapsi SQL:

| Articolo | pool SQL dedicato | Pool SQL serverless |
| ------- | -------- | ------------- |
| [Connessioni](connect-overview.md)                    | Sì | Sì |
| [Classi di risorse e concorrenza](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Sì    | No |
| [Transazioni](develop-transactions.md)              | Sì | No |
| [Schemi definiti dall'utente](develop-user-defined-schemas.md) | Sì | Sì |
| [Distribuzione di tabelle in SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Sì | No |
| [Indicizzazione di tabelle](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Sì | No |
| [Partizioni di tabella](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Sì | No |
| [Statistiche](develop-tables-statistics.md)            | Sì | Sì |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Sì | No |
| [Tabelle esterne](develop-tables-external-tables.md) | Sì | Sì |
| [CETAS](develop-tables-cetas.md)                     | Sì | Sì |


## <a name="recommendations"></a>Consigli

Di seguito sono riportati gli articoli essenziali che evidenziano tecniche di codifica, suggerimenti e consigli per lo sviluppo specifici:

| Articolo | pool SQL dedicato | Pool SQL serverless |
| ------- | -------- | ------------- |
| [Stored procedure](develop-stored-procedures.md)  | Sì                | No                      |
| [Etichette](develop-label.md)                           | Sì                | No                      |
| [Visualizzazioni](develop-views.md)                             | Sì                | Sì                     |
| [Tabelle temporanee](develop-tables-temporary.md)       | Sì                | Sì                     |
| [SQL dinamica](develop-dynamic-sql.md)                 | Sì                | Sì                     |
| [Cicli](develop-loops.md)                         | Sì                | Sì                     |
| [Opzioni di raggruppamento](develop-group-by-options.md)       | Sì                | No                      |
| [Assegnazione di variabili](develop-variable-assignment.md) | Sì                | Sì                     |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni di riferimento, vedere [istruzioni T-SQL del pool SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

