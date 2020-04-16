---
title: Risorse per lo sviluppo di funzionalità Synapse SQL
description: Concetti di sviluppo, decisioni di progettazione, consigli e tecniche di codifica per Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: ba6ceec3064c5c876ca899ab58881e23913b9701
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429018"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Progettare le decisioni e le tecniche di codifica per le funzionalità SQL di Synapse in Azure Synapse AnalyticsDesign decisions and coding techniques for Synapse SQL features in Azure Synapse Analytics
In questo articolo è presente un elenco di risorse per il pool SQL e le funzioni SQL su richiesta (anteprima) di Synapse SQL. Gli articoli consigliati sono suddivisi in due sezioni: decisioni di progettazione chiave e tecniche di sviluppo e codifica.

L'obiettivo di questi articoli è quello di aiutarti a sviluppare l'approccio tecnico ottimale per i componenti SQL Synapse all'interno di Synapse Analytics.

## <a name="key-design-decisions"></a>Decisioni chiave nella progettazione
Gli articoli seguenti evidenziano i concetti e le decisioni di progettazione per lo sviluppo Sql Synapse:The articles below highlight concepts and design decisions for Synapse SQL development:

|                                                          |   Pool SQL   | SQL su richiesta |
| -----------------------------------------------------    | ---- | ---- |
| [Connessioni](connect-overview.md)                    | Sì | Sì |
| [Classi di risorse e concorrenza](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Sì    | No |
| [Transazioni](develop-transactions.md)              | Sì | No |
| [Schemi definiti dall'utente](develop-user-defined-schemas.md) | Sì | Sì |
| [Distribuzione tabelle](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Sì | No |
| [Indici di tabella](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Sì | No |
| [Partizioni di tabella](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Sì | No |
| [Statistiche](develop-tables-statistics.md)            | Sì | Sì |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Sì | No |
| [Tabelle esterne](develop-tables-external-tables.md) | Sì | Sì |
| [CETAI](develop-tables-cetas.md)                     | Sì | Sì |


## <a name="recommendations"></a>Consigli

Di seguito sono riportati gli articoli essenziali che enfatizzano tecniche di codifica specifiche, suggerimenti e consigli per lo sviluppo:

|                                            | Pool SQL | SQL su richiesta |
| ------------------------------------------ | ------------------ | ----------------------- |
| [Stored procedure](develop-stored-procedures.md)  | Sì                | No                      |
| [Etichette](develop-label.md)                           | Sì                | No                      |
| [Viste](develop-views.md)                             | Sì                | Sì                     |
| [Tabelle temporanee](develop-tables-temporary.md)       | Sì                | Sì                     |
| [SQL dinamica](develop-dynamic-sql.md)                 | Sì                | Sì                     |
| [Loop](develop-loops.md)                         | Sì                | Sì                     |
| [Opzioni di raggruppamento](develop-group-by-options.md)       | Sì                | No                      |
| [Assegnazione di variabili](develop-variable-assignment.md) | Sì                | Sì                     |

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni di riferimento, vedere [Istruzioni T-SQL del pool SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

