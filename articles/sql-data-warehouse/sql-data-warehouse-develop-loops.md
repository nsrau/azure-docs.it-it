---
title: Sfruttare cicli T-SQL in Azure SQL Data Warehouse | Documentazione Microsoft
description: Suggerimenti sui di cicli Transact-SQL e sulla sostituzione di cursori in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: f3384b81-b943-431b-bc73-90e47e4c195f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 40a872ff310f48bfd543ac184fe7301b85b50258
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="loops-in-sql-data-warehouse"></a>Cicli in SQL Data Warehouse
SQL Data Warehouse supporta il ciclo [WHILE][WHILE] per eseguire ripetutamente blocchi di istruzioni. L'esecuzione continua fino a quando le condizioni specificate sono vere o fino a quando il codice termina il ciclo in modo specifico usando la parola chiave `BREAK` . I cicli sono particolarmente utili per la sostituzione di cursori definiti nel codice SQL. Per fortuna, quasi tutti i cursori scritti in codice SQL sono del tipo avanzamento rapido, di sola lettura. Di conseguenza i cicli [WHILE] rappresentano un'ottima alternativa se è necessario sostituirne uno.

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>Uso di cicli e sostituzione di cursori in SQL Data Warehouse
Tuttavia, prima di procedere è innanzitutto necessario chiedersi se il cursore può essere riscritto per l'uso di operazioni basate su set. In molti casi la risposta è Sì ed è spesso l'approccio migliore. Un'operazione basata su set viene spesso eseguita molto più velocemente rispetto a un approccio iterativo riga per riga.

I cursori ad avanzamento rapido di sola lettura possono essere facilmente sostituiti con un costrutto di ciclo. Un semplice esempio viene riportato di seguito: Questo esempio di codice aggiorna le statistiche per ogni tabella nel database. Scorrendo le tabelle nel ciclo è possibile eseguire ogni comando in sequenza.

Prima di tutto, creare una tabella temporanea contenente un numero di riga univoco usato per identificare le singole istruzioni:

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Inizializzare quindi le variabili necessarie per eseguire il ciclo:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Ora eseguire un ciclo delle istruzioni, una alla volta:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Infine eliminare la tabella temporanea creata nel primo passaggio.

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[WHILE]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->
