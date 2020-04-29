---
title: Risorse per lo sviluppo di un pool SQL sinapsi in Azure sinapsi Analytics
description: Concetti di sviluppo, decisioni di progettazione, consigli e tecniche di codifica per SQL Data Warehouse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 12f91633834ee98582eaad886b48eb8619378265
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81411656"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Decisioni di progettazione e tecniche di codifica per un pool SQL sinapsi in Azure sinapsi Analytics 
 In questo articolo sono disponibili risorse aggiuntive che consentono di comprendere meglio le decisioni di progettazione principali, le raccomandazioni e le tecniche di codifica per un pool SQL in sinapsi di Azure.

## <a name="key-design-decisions"></a>Decisioni chiave nella progettazione
Gli articoli seguenti illustrano i concetti e le decisioni di progettazione per lo sviluppo di un data warehouse distribuito usando la funzionalità del pool SQL in sinapsi di Azure:

* [connessioni](../sql/connect-overview.md)
* [concorrenza](resource-classes-for-workload-management.md)
* [transazioni](sql-data-warehouse-develop-transactions.md)
* [schemi definiti dall'utente](sql-data-warehouse-develop-user-defined-schemas.md)
* [Distribuzione di tabelle in SQL Data Warehouse](sql-data-warehouse-tables-distribute.md)
* [Indicizzazione di tabelle in SQL Data Warehouse](sql-data-warehouse-tables-index.md)
* [Partizioni di tabella](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [Statistiche](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Sviluppo dei suggerimenti e delle tecniche di codifica
Negli articoli seguenti sono disponibili tecniche di codifica, suggerimenti e consigli specifici per lo sviluppo di un pool SQL:

* [stored procedure](sql-data-warehouse-develop-stored-procedures.md)
* [etichette](sql-data-warehouse-develop-label.md)
* [Viste](sql-data-warehouse-develop-views.md)
* [tabelle temporanee](sql-data-warehouse-tables-temporary.md)
* [SQL dinamico](sql-data-warehouse-develop-dynamic-sql.md)
* [Cicli](sql-data-warehouse-develop-loops.md)
* [opzioni di raggruppamento](sql-data-warehouse-develop-group-by-options.md)
* [assegnazione di variabili](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni di riferimento, vedere [istruzioni T-SQL](sql-data-warehouse-reference-tsql-statements.md).
