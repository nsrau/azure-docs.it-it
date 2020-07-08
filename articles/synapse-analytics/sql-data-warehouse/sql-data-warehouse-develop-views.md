---
title: Uso di viste T-SQL
description: Suggerimenti per l'uso di viste T-SQL e per lo sviluppo di soluzioni nel pool SQL sinapsi.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 26eb3a495fd1c896416265687d92da66dfc3599b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212293"
---
# <a name="views-in-synapse-sql-pool"></a>Visualizzazioni nel pool SQL sinapsi

Le viste risultano utili in molti modi diversi per migliorare la qualità della soluzione.

Il pool SQL supporta le viste standard e le viste materializzate. Entrambe sono tabelle virtuali create con espressioni SELECT e presentate alle query come tabelle logiche.

Le viste incapsulano la complessità del calcolo dei dati comuni e aggiungono un livello di astrazione alle modifiche dei calcoli, in modo che non sia necessario riscrivere le query.

## <a name="standard-view"></a>Vista standard

Una vista standard calcola i dati ogni volta che viene usata. Non sono presenti dati archiviati su disco. Le viste standard vengono in genere usate come strumento per organizzare gli oggetti logici e le query in un database.

Per usare una vista standard, una query deve farvi riferimento diretto. Per altre informazioni, vedere la documentazione per [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Le visualizzazioni nel pool SQL vengono archiviate solo come metadati. Di conseguenza, le opzioni seguenti non sono disponibili:

* Non esiste alcuna opzione di binding dello schema
* Non è possibile aggiornare le tabelle di base tramite la vista
* Non è possibile creare viste su tabelle temporanee
* Non è disponibile alcun supporto per gli hint EXPAND/NOEXPAND
* Non sono presenti viste indicizzate nel pool SQL

È possibile utilizzare le visualizzazioni standard per applicare join ottimizzati per le prestazioni tra le tabelle. Ad esempio, una vista può incorporare una chiave di distribuzione ridondante come parte dei criteri di join per ridurre al minimo lo spostamento dei dati.

Un altro vantaggio delle viste potrebbe essere l'applicazione di una query specifica o di un hint di join. Usando le viste in questo modo, i join vengono sempre eseguiti in modo ottimale senza che gli utenti debbano ricordare il costrutto corretto per i relativi join.

## <a name="materialized-view"></a>Vista materializzata

Una vista materializzata pre-calcola, archivia e gestisce i dati nel pool SQL esattamente come una tabella. Non è necessario ricalcolare una vista materializzata a ogni utilizzo.

Quando i dati vengono caricati in tabelle di base, il pool SQL Aggiorna in modo sincrono le viste materializzate.  Il Query Optimizer utilizza automaticamente viste materializzate distribuite per migliorare le prestazioni di esecuzione delle query anche se nella query non viene fatto riferimento alle viste.  

Le query che sfruttano la maggior parte delle viste materializzate sono query complesse, in genere query con join e aggregazioni, in tabelle di grandi dimensioni che producono un set di risultati di piccole dimensioni.  

Per informazioni dettagliate sulla sintassi e sugli altri requisiti delle viste materializzate, vedere [CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Per informazioni aggiuntive sull'ottimizzazione delle query, vedere [ottimizzazione delle prestazioni con viste materializzate](performance-tuning-materialized-views.md).

## <a name="example"></a>Esempio

Un modello di applicazione comune consiste nel ricreare le tabelle usando CREATE TABLE come SELECT (CTAS) seguito da un modello di ridenominazione di oggetti durante il caricamento dei dati.  

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
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Questo approccio, tuttavia, può comportare la visualizzazione e la scomparsa di tabelle da parte di un utente insieme all'emissione di messaggi di errore "tabella inesistente".

È possibile utilizzare le viste per fornire agli utenti un livello di presentazione coerente mentre gli oggetti sottostanti vengono rinominati. Fornendo l'accesso ai dati tramite le viste, gli utenti non necessitano di visibilità per le tabelle sottostanti.

Questo livello offre un'esperienza utente coerente, assicurando allo stesso tempo che i progettisti del data warehouse possano sviluppare il modello di dati. La possibilità di sviluppare le tabelle sottostanti implica che i progettisti possono usare CTAS per ottimizzare le prestazioni durante il processo di caricamento dei dati.

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo di pool SQL](sql-data-warehouse-overview-develop.md).
