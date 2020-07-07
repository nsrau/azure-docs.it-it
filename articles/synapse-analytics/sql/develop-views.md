---
title: Viste T-SQL con sinapsi SQL
description: Suggerimenti per l'uso di viste T-SQL e per lo sviluppo di soluzioni con sinapsi SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81428654"
---
# <a name="t-sql-views-using-synapse-sql"></a>Viste T-SQL con sinapsi SQL
In questo articolo sono disponibili suggerimenti per l'uso di viste T-SQL e per lo sviluppo di soluzioni con sinapsi SQL. 

## <a name="why-use-views"></a>Perché usare le viste?

Le viste risultano utili in molti modi diversi per migliorare la qualità della soluzione.  Questo articolo illustra alcuni esempi di come migliorare la soluzione con le visualizzazioni e include le limitazioni che devono essere prese in considerazione.

### <a name="sql-pool---create-view"></a>Pool SQL-Crea vista

> [!NOTE]
> **Pool SQL**: la sintassi per Create View non è illustrata in questo articolo. Per altre informazioni, vedere la documentazione per [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="sql-on-demand-preview---create-view"></a>SQL su richiesta (anteprima)-Crea vista

> [!NOTE]
> **SQL su richiesta**: la sintassi per Create View non è illustrata in questo articolo. Per altre informazioni, vedere la documentazione per [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="architectural-abstraction"></a>Astrazione dell'architettura

Un modello di applicazione comune consiste nel ricreare le tabelle usando [create table come SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAs), seguito da un modello di ridenominazione di oggetti durante il caricamento dei dati.

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

Tenere presente che questo approccio può comportare la visualizzazione e la disparizione delle tabelle dalla visualizzazione di un utente e la richiesta di messaggi di errore "tabella inesistente". È possibile utilizzare le viste per fornire agli utenti un livello di presentazione coerente mentre gli oggetti sottostanti vengono rinominati.

Fornendo l'accesso ai dati tramite le viste, gli utenti non necessitano di visibilità per le tabelle sottostanti. Oltre a un'esperienza utente coerente, questo livello garantisce che i progettisti di analisi possano evolvere il modello di dati. La possibilità di sviluppare le tabelle sottostanti significa che i progettisti possono usare CTAS per ottimizzare le prestazioni durante il processo di caricamento dei dati.

## <a name="performance-optimization"></a>Ottimizzazione delle prestazioni

Le visualizzazioni possono essere utilizzate anche per applicare join ottimizzati per le prestazioni tra le tabelle. Ad esempio, una vista può incorporare una chiave di distribuzione ridondante come parte dei criteri di join per ridurre al minimo lo spostamento dei dati.

La forzatura di una query specifica o di un hint di join è un altro vantaggio dell'uso di viste T-SQL. Di conseguenza, la funzionalità views garantisce che i join vengano sempre eseguiti in modo ottimale. Si eviterà la necessità per gli utenti di ricordare il costrutto corretto per i join.

## <a name="limitations"></a>Limitazioni

Le visualizzazioni in sinapsi SQL vengono archiviate solo come metadati. Di conseguenza, le opzioni seguenti non sono disponibili:

* Non esiste un'opzione di associazione dello schema
* Non è possibile aggiornare le tabelle di base tramite la vista
* Non è possibile creare viste su tabelle temporanee
* Non è disponibile alcun supporto per gli hint EXPAND/NOEXPAND
* Non sono presenti viste indicizzate in sinapsi SQL

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere [Synapse SQL pool development overview](develop-overview.md) (Panoramica sullo sviluppo per il pool Synapse SQL).



