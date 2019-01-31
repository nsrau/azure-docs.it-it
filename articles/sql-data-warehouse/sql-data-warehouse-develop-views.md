---
title: Uso di viste T-SQL in Azure SQL Data Warehouse | Documentazione Microsoft
description: Suggerimenti per l'uso di viste T-SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: dba6d49590cc4064155e58784a166d3abbb19b6f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468428"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Viste in Azure SQL Data Warehouse
Suggerimenti per l'uso di viste T-SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni. 

## <a name="why-use-views"></a>Perché usare le viste?
Le viste risultano utili in molti modi diversi per migliorare la qualità della soluzione.  Questo articolo contiene alcuni esempi che illustrano come migliorare la soluzione con le viste e le limitazioni da prendere in considerazione.

> [!NOTE]
> La sintassi per CREATE VIEW non viene illustrata in questo articolo. Per altre informazioni, vedere la documentazione per [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql).
> 
> 

## <a name="architectural-abstraction"></a>Astrazione dell'architettura
Un modello di applicazione comune consiste nel ricreare le tabelle usando CREATE TABLE AS SELECT (CTAS) seguito da un modello di ridenominazione di oggetti durante il caricamento dei dati.

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

Tuttavia, usando questo approccio la visualizzazione delle tabelle nella vista dell'utente potrebbe non essere costante e potrebbero essere restituiti messaggi di errore di tabella non esistente. Le viste possono essere usate per garantire agli utenti un livello di presentazione coerente mentre vengono rinominati gli oggetti sottostanti. Fornendo l'accesso ai dati tramite le viste, gli utenti non devono avere visibilità sulle tabelle sottostanti. Questo livello offre un'esperienza utente coerente, assicurando allo stesso tempo che i progettisti del data warehouse possano sviluppare il modello di dati. La possibilità di sviluppare le tabelle sottostanti implica che i progettisti possono usare CTAS per ottimizzare le prestazioni durante il processo di caricamento dei dati.   

## <a name="performance-optimization"></a>Ottimizzazione delle prestazioni
Le viste possono anche essere usate per creare join ottimizzati per le prestazioni tra le tabelle. Ad esempio, una vista può incorporare una chiave di distribuzione ridondante come parte dei criteri di join per ridurre al minimo lo spostamento dei dati. Un altro vantaggio delle viste potrebbe essere l'applicazione di una query specifica o di un hint di join. Usando le viste in questo modo, i join vengono sempre eseguiti in modo ottimale senza che gli utenti debbano ricordare il costrutto corretto per i relativi join.

## <a name="limitations"></a>Limitazioni
Le viste in SQL Data Warehouse sono archiviate solo come metadati. Di conseguenza, le opzioni seguenti non sono disponibili:

* Non esiste alcuna opzione di binding dello schema
* Le tabelle di base non possono essere aggiornate tramite la vista
* Non è possibile creare visualizzazioni sulle tabelle temporanee
* Non è disponibile alcun supporto per gli hint EXPAND/NOEXPAND
* Non sono disponibili viste indicizzate in SQL Data Warehouse

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


