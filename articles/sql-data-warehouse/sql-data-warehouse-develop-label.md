---
title: Usare etichette per instrumentare query in SQL Data Warehouse | Microsoft Docs
description: Suggerimenti per l'uso di etichette per instrumentare query in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 44988de8-04c1-4fed-92be-e1935661a4e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 9e75bbe528a427724a623305fbd45e2277e9d0af
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017

---
# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Usare etichette per instrumentare query in SQL Data Warehouse
SQL Data Warehouse supporta un concetto detto etichette di query. Prima di approfondire il concetto, eccone un esempio:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

L'ultima riga contrassegna la stringa 'My Query Label' per la query. Ciò è particolarmente utile in quanto l'etichetta supporta la query tramite le DMV. In questo modo si dispone di un meccanismo per tenere traccia di query problematiche e per identificare lo stato di avanzamento tramite un'esecuzione ETL.

Una buona convenzione di denominazione è estremamente utile in questo caso. Ad esempio, una stringa simile a ' PROJECT : PROCEDURE : STATEMENT : COMMENT' può facilitare l'identificazione della query in tutto il codice nel controllo del codice sorgente.

Per la ricerca in base all'etichetta, è possibile usare la query seguente che usa le viste a gestione dinamica:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> È essenziale racchiudere tra parentesi quadre o virgolette doppie la parola label durante l'esecuzione della query. Label è una parola riservata e causa un errore se non viene delimitata.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

