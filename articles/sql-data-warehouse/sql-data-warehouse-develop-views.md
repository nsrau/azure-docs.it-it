---
title: Uso di viste T-SQL in Azure SQL Data Warehouse | Documentazione Microsoft
description: Suggerimenti per l&quot;uso di viste Transact-SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: b5208f32-8f4a-4056-8788-2adbb253d9fd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 43ab6a2f71ab51c50847b1ba5249f51c48e03fea
ms.openlocfilehash: 902929fba9e2486d97323ab52505ba6db3fe0aab


---
# <a name="views-in-sql-data-warehouse"></a>Viste in SQL Data Warehouse
Le viste sono particolarmente utili in SQL Data Warehouse. Risultano utili in molti modi diversi per migliorare la qualità della soluzione.  Questo articolo contiene alcuni esempi che illustrano come migliorare la soluzione con le viste e le limitazioni da prendere in considerazione.

> [!NOTE]
> La sintassi per `CREATE VIEW` non viene illustrata in questo articolo. Per informazioni di riferimento, vedere l'articolo su [CREATE VIEW][CREATE VIEW] in MSDN.
> 
> 

## <a name="architectural-abstraction"></a>Astrazione dell'architettura
Un modello di applicazione molto comune consiste nel ricreare le tabelle usando CREATE TABLE AS SELECT (CTAS) seguito da un modello di ridenominazione di oggetti durante il caricamento dei dati.

L'esempio seguente aggiunge nuovi record a una dimensione data. Si noti come viene creata una nuova tabella, DimDate_New, e poi rinominata per sostituire la versione originale della tabella.

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

Tuttavia, usando questo approccio la visualizzazione delle tabelle nella vista dell'utente potrebbe non essere costante e potrebbero essere restituiti messaggi di errore di tabella non esistente. Le viste possono essere usate per garantire agli utenti un livello di presentazione coerente mentre vengono rinominati gli oggetti sottostanti. Fornire agli utenti l'accesso ai dati tramite le viste significa che gli utenti non devono necessariamente avere la visibilità delle tabelle sottostanti. Questo approccio garantisce la coerenza dell'esperienza utente e consente l'evoluzione del modello dati e l'ottimizzazione delle prestazioni da parte delle finestre di progettazione del data warehouse usando CTAS durante il processo di caricamento dei dati.    

## <a name="performance-optimization"></a>Ottimizzazione delle prestazioni
Le viste possono anche essere usate per creare join ottimizzati per le prestazioni tra le tabelle. Ad esempio, una vista può incorporare una chiave di distribuzione ridondante come parte dei criteri di join per ridurre al minimo lo spostamento dei dati.  Un altro vantaggio delle viste potrebbe essere l'applicazione di una query specifica o di un hint di join. Usando le viste in questo modo, i join vengono sempre eseguiti in modo ottimale senza che gli utenti debbano ricordare il costrutto corretto per i relativi join.

## <a name="limitations"></a>Limitazioni
Le viste in SQL Data Warehouse sono solo metadati.  Di conseguenza, le opzioni seguenti non sono disponibili:

* Non esiste alcuna opzione di binding dello schema
* Le tabelle di base non possono essere aggiornate tramite la vista
* Non è possibile creare visualizzazioni sulle tabelle temporanee
* Non è disponibile alcun supporto per gli hint EXPAND/NOEXPAND
* Non sono disponibili viste indicizzate in SQL Data Warehouse

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][SQL Data Warehouse development overview].
Per la sintassi di `CREATE VIEW`, vedere [CREATE VIEW][CREATE VIEW].

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CREATE VIEW]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->



<!--HONumber=Jan17_HO4-->


