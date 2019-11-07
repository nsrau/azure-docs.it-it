---
title: Uso di viste T-SQL
description: Suggerimenti per l'uso di viste T-SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 14ff990fa21f4af3c77b4dba10b4fea758ff32b6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685766"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Viste in Azure SQL Data Warehouse
Le viste risultano utili in molti modi diversi per migliorare la qualità della soluzione. 

Azure SQL Data Warehouse supporta le viste standard e materializzate. Entrambe sono tabelle virtuali create con espressioni SELECT e presentate alle query come tabelle logiche. Le visualizzazioni incapsulano la complessità del calcolo comune dei dati e aggiungono un livello di astrazione alle modifiche di calcolo, pertanto non è necessario riscrivere le query.

## <a name="standard-view"></a>Vista standard
Una vista standard calcola i dati ogni volta che viene utilizzata la vista. Non sono presenti dati archiviati su disco. Le persone utilizzano in genere viste standard come uno strumento che consente di organizzare gli oggetti logici e le query in un database. Per utilizzare una vista standard, è necessario eseguire un riferimento diretto a una query. Per altre informazioni, vedere la documentazione per [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql).

Le viste in SQL Data Warehouse sono archiviate solo come metadati. Di conseguenza, le opzioni seguenti non sono disponibili:
* Non esiste alcuna opzione di binding dello schema
* Le tabelle di base non possono essere aggiornate tramite la vista
* Non è possibile creare visualizzazioni sulle tabelle temporanee
* Non è disponibile alcun supporto per gli hint EXPAND/NOEXPAND
* Non sono disponibili viste indicizzate in SQL Data Warehouse

È possibile utilizzare le visualizzazioni standard per applicare join ottimizzati per le prestazioni tra le tabelle. Ad esempio, una vista può incorporare una chiave di distribuzione ridondante come parte dei criteri di join per ridurre al minimo lo spostamento dei dati. Un altro vantaggio delle viste potrebbe essere l'applicazione di una query specifica o di un hint di join. Usando le viste in questo modo, i join vengono sempre eseguiti in modo ottimale senza che gli utenti debbano ricordare il costrutto corretto per i relativi join.

## <a name="materialized-view"></a>Vista materializzata
Una vista materializzata consente di pre-calcolare, archiviare e mantenere i dati in Azure SQL Data Warehouse esattamente come una tabella. Non è necessario ricalcolare ogni volta quando viene usata una vista materializzata. Quando i dati vengono caricati in tabelle di base, Azure SQL Data Warehouse aggiorna in modo sincrono le viste materializzate.  Il Query Optimizer utilizza automaticamente viste materializzate distribuite per migliorare le prestazioni di esecuzione delle query anche se nella query non viene fatto riferimento alle viste.  Le query che sfruttano la maggior parte delle viste materializzate sono query complesse, in genere query con join e aggregazioni, in tabelle di grandi dimensioni che producono un set di risultati di piccole dimensioni.  

Per informazioni dettagliate sulla sintassi di visualizzazione materializzata e su altri requisiti, vedere [creare una vista materializzata come SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  

Per informazioni aggiuntive sull'ottimizzazione delle query, vedere [ottimizzazione delle prestazioni con viste materializzate](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views). 

## <a name="example"></a>Esempio
Un modello di applicazione comune consiste nel ricreare le tabelle usando CREATE TABLE AS SELECT (CTAS) seguito da un modello di ridenominazione di oggetti durante il caricamento dei dati.  L'esempio seguente aggiunge nuovi record di data a una dimensione data. Si noti come viene creata prima di tutto una nuova tabella, DimDate_New, che viene poi rinominata per sostituire la versione originale della tabella.

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
Tuttavia, usando questo approccio la visualizzazione delle tabelle nella vista dell'utente potrebbe non essere costante e potrebbero essere restituiti messaggi di errore di tabella non esistente. Le viste possono essere usate per garantire agli utenti un livello di presentazione coerente mentre vengono rinominati gli oggetti sottostanti. Fornendo l'accesso ai dati tramite le viste, gli utenti non devono avere visibilità sulle tabelle sottostanti. Questo livello offre un'esperienza utente coerente, assicurando allo stesso tempo che i progettisti del data warehouse possano sviluppare il modello di dati. La possibilità di sviluppare le tabelle sottostanti implica che i progettisti possono usare CTAS per ottimizzare le prestazioni durante il processo di caricamento dei dati.   

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


