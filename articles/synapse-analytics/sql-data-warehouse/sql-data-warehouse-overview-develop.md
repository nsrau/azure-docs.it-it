---
title: Risorse per lo sviluppo di un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics
description: Concetti di sviluppo, decisioni di progettazione, consigli e tecniche di codifica per un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 6b34c70b453c26fe27a51e1aa802564864640cb9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453689"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Decisioni di progettazione e tecniche di codifica per un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics 

 In questo articolo sono disponibili risorse aggiuntive che consentono di comprendere meglio le decisioni di progettazione, le raccomandazioni e le tecniche di codifica per un pool SQL dedicato (in precedenza SQL DW) in sinapsi di Azure.

## <a name="key-design-decisions"></a>Decisioni chiave nella progettazione

Negli articoli seguenti vengono illustrati i concetti e le decisioni di progettazione per lo sviluppo di un data warehouse distribuito utilizzando la funzionalità pool SQL dedicato (in precedenza SQL DW) in sinapsi di Azure:

* [connessioni](sql-data-warehouse-connect-overview.md)
* [concurrency](resource-classes-for-workload-management.md)
* [transazioni](sql-data-warehouse-develop-transactions.md)
* [schemi definiti dall'utente](sql-data-warehouse-develop-user-defined-schemas.md)
* [distribuzione di tabelle](sql-data-warehouse-tables-distribute.md)
* [indici tabella](sql-data-warehouse-tables-index.md)
* [partizioni di tabella](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [Statistiche](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Sviluppo dei suggerimenti e delle tecniche di codifica

Negli articoli seguenti sono disponibili tecniche di codifica, suggerimenti e consigli specifici per lo sviluppo di un pool SQL dedicato (in precedenza SQL DW):

* [stored procedure](sql-data-warehouse-develop-stored-procedures.md)
* [etichette](sql-data-warehouse-develop-label.md)
* [Viste](performance-tuning-materialized-views.md)
* [tabelle temporanee](sql-data-warehouse-tables-temporary.md)
* [SQL dinamico](sql-data-warehouse-develop-dynamic-sql.md)
* [ciclo](sql-data-warehouse-develop-loops.md)
* [opzioni di raggruppamento](sql-data-warehouse-develop-group-by-options.md)
* [assegnazione di variabili](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni di riferimento, vedere [istruzioni T-SQL](sql-data-warehouse-reference-tsql-statements.md).
