---
title: Eseguire la migrazione dello schema in SQL Data Warehouse | Microsoft Docs
description: Suggerimenti per la migrazione dello schema in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/25/2016
ms.author: jrj;barbkess;sonyama

---
# Eseguire la migrazione dello schema in SQL Data Warehouse
I riepiloghi seguenti consentono di capire le differenze tra l'uso di SQL Server e di SQL Data Warehouse per la migrazione di un database.

## Migrazione di tabelle
Quando si esegue la migrazione di tabelle, è opportuno acquisire familiarità con le funzionalità delle tabelle di SQL Data Warehouse. La [panoramica delle tabelle][panoramica delle tabelle] è un ottimo punto di partenza. In questo articolo vengono presentate le considerazioni più importanti da tenere presenti quando si crea una tabella, ad esempio statistiche, distribuzione, partizionamento e indicizzazione di una tabella. Inoltre vengono presentate anche alcune [funzionalità non supportate][funzionalità non supportate] e soluzioni alternative.

SQL Data Warehouse supporta i tipi di dati business comuni. Vedere l'articolo sul [tipi di dati][tipi di dati] per un elenco dei [tipi di dati non supportati][tipi di dati non supportati] e supportati. Nell'articolo sui [tipi di dati][tipi di dati] è contenuta anche una query per identificare i [tipi di dati non supportati][tipi di dati non supportati]. Quando si convertono i tipi di dati, assicurarsi di esaminare le [procedure consigliate per il tipo di dati][procedure consigliate per il tipo di dati].

## Passaggi successivi
Dopo la migrazione dello schema del database a SQL Data Warehouse, passare a uno degli articoli seguenti:

* [Eseguire la migrazione dei dati][Eseguire la migrazione dei dati]
* [Eseguire la migrazione del codice][Eseguire la migrazione del codice]

Per altre informazioni, vedere l'articolo con le [procedure consigliate][procedure consigliate] di SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[Eseguire la migrazione del codice]: ./sql-data-warehouse-migrate-code.md
[Eseguire la migrazione dei dati]: ./sql-data-warehouse-migrate-data.md
[procedure consigliate]: ./sql-data-warehouse-best-practices.md
[panoramica delle tabelle]: ./sql-data-warehouse-tables-overview.md
[funzionalità non supportate]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[tipi di dati]: ./sql-data-warehouse-tables-data-types.md
[tipi di dati non supportati]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[procedure consigliate per il tipo di dati]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0831_2016-->