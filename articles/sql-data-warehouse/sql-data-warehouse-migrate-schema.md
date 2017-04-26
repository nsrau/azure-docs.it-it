---
title: Eseguire la migrazione dello schema in SQL Data Warehouse | Documentazione Microsoft
description: Suggerimenti per la migrazione dello schema in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0630f43642a0be98c470032d32b74ca14ee144e5
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Eseguire la migrazione dello schema in SQL Data Warehouse
I riepiloghi seguenti consentono di capire le differenze tra l'uso di SQL Server e di SQL Data Warehouse per la migrazione di un database.

## <a name="table-migration"></a>Migrazione di tabelle
Quando si esegue la migrazione di tabelle, è opportuno acquisire familiarità con le funzionalità delle tabelle di SQL Data Warehouse.  La [panoramica delle tabelle][table overview] è un ottimo punto di partenza.  In questo articolo vengono presentate le considerazioni più importanti da tenere presenti quando si crea una tabella, ad esempio statistiche, distribuzione, partizionamento e indicizzazione di una tabella.  Vengono anche presentate alcune [funzionalità non supportate][unsupported table features] e soluzioni alternative.

SQL Data Warehouse supporta i tipi di dati business comuni.  Vedere l'articolo sui [tipi di dati][data types] per un elenco dei [tipi di dati non supportati][unsupported data types] e supportati.  Nell'articolo sui [tipi di dati][data types] è contenuta anche una query per identificare i [tipi di dati non supportati][unsupported data types].  Quando si convertono i tipi di dati, assicurarsi di esaminare le [procedure consigliate per il tipo di dati][data type best practices].

## <a name="next-steps"></a>Passaggi successivi
Dopo la migrazione dello schema del database a SQL Data Warehouse, passare a uno degli articoli seguenti:

* [Eseguire la migrazione dei dati][Migrate your data]
* [Eseguire la migrazione del codice][Migrate your code]

Per altre informazioni, vedere l'articolo con le [procedure consigliate][best practices] di SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[data type best practices]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->

