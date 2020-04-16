---
title: Viste T-SQL con Synapse SQL
description: Suggerimenti per l'utilizzo di viste T-SQL e lo sviluppo di soluzioni con Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428654"
---
# <a name="t-sql-views-using-synapse-sql"></a>Viste T-SQL con Synapse SQL
In questo articolo sono disponibili suggerimenti per l'utilizzo di visualizzazioni T-SQL e lo sviluppo di soluzioni con Synapse SQL. 

## <a name="why-use-views"></a>Perché usare le viste?

Le viste risultano utili in molti modi diversi per migliorare la qualità della soluzione.  In questo articolo vengono evidenziati alcuni esempi di come arricchire la soluzione con visualizzazioni e include le limitazioni che è necessario considerare.

### <a name="sql-pool---create-view"></a>Pool SQL - crea visualizzazione

> [!NOTE]
> **Pool SQL:** la sintassi di CREATE VIEW non è descritta in questo articolo. Per altre informazioni, vedere la documentazione per [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="sql-on-demand-preview---create-view"></a>SQL su richiesta (anteprima) - crea visualizzazione

> [!NOTE]
> **SQL su richiesta**: la sintassi per CREATE VIEW non viene descritta in questo articolo. Per altre informazioni, vedere la documentazione per [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="architectural-abstraction"></a>Astrazione dell'architettura

Un modello di applicazione comune consiste nel ricreare le tabelle utilizzando [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), seguito da un modello di ridenominazione degli oggetti durante il caricamento dei dati.

L'esempio seguente aggiunge nuovi record di data a una dimensione data. Si noti come viene creata prima di tutto una nuova tabella, DimDate_New, che viene poi rinominata per sostituire la versione originale della tabella.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Tenere presente che questo approccio può causare la visualizzazione e la scomparsa di tabelle dalla visualizzazione di un utente e viene richiesto messaggi di errore "tabella non esiste". Le viste possono essere utilizzate per fornire agli utenti un livello di presentazione coerente mentre gli oggetti sottostanti vengono rinominati.

Fornendo l'accesso ai dati tramite le viste, gli utenti non hanno bisogno di visibilità per le tabelle sottostanti. Oltre a un'esperienza utente coerente, questo livello garantisce che i progettisti di analisi possano evolvere il modello di dati. La possibilità di evolvere le tabelle sottostanti significa che le finestre di progettazione possono utilizzare CTAS per ottimizzare le prestazioni durante il processo di caricamento dei dati.

## <a name="performance-optimization"></a>Ottimizzazione delle prestazioni

Le viste possono essere utilizzate anche per applicare join ottimizzati per le prestazioni tra le tabelle. Ad esempio, una vista può incorporare una chiave di distribuzione ridondante come parte dei criteri di join per ridurre al minimo lo spostamento dei dati.

Forzare una query o un hint di join specifico è un altro vantaggio dell'utilizzo delle viste T-SQL. Di conseguenza, la funzionalità di visualizzazione garantisce che i join vengano sempre eseguiti in modo ottimale. Eviterai la necessità per gli utenti di ricordare il costrutto corretto per i loro join.

## <a name="limitations"></a>Limitazioni

Le visualizzazioni in Synapse SQL vengono archiviate solo come metadati. Di conseguenza, le seguenti opzioni non sono disponibili:

* Non esiste un'opzione di associazione dello schema
* Le tabelle di base non possono essere aggiornate tramite la vista
* Non è possibile creare viste su tabelle temporanee
* Non è disponibile alcun supporto per i suggerimenti EXPAND / NOEXPAND
* Non sono presenti viste indicizzate in Synapse SQL

## <a name="next-steps"></a>Passaggi successivi

Per altre suggerimenti sullo sviluppo, vedere [Panoramica dello sviluppo sql di Synapse](develop-overview.md).For more development tips, see Synapse SQL development overview .



