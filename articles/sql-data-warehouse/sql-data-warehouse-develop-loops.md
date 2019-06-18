---
title: Uso di cicli T-SQL in Azure SQL Data Warehouse | Microsoft Docs
description: Suggerimenti per l'uso di cicli T-SQL e la sostituzione di cursori in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: c321bc4e493799a50ada4dd91faf2d2ebdee8aba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65850466"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Uso di cicli T-SQL in SQL Data Warehouse
Suggerimenti per l'uso di cicli T-SQL e la sostituzione di cursori in Azure SQL Data Warehouse per lo sviluppo di soluzioni.

## <a name="purpose-of-while-loops"></a>Scopo dei cicli WHILE

SQL Data Warehouse supporta il ciclo [WHILE](/sql/t-sql/language-elements/while-transact-sql) per eseguire ripetutamente blocchi di istruzioni. Il ciclo WHILE continua fino a quando le condizioni specificate sono vere o fino a quando il codice termina il ciclo in modo specifico usando la parola chiave BREAK. I cicli sono utili per la sostituzione di cursori definiti nel codice SQL. Per fortuna, quasi tutti i cursori scritti in codice SQL sono del tipo avanzamento rapido, di sola lettura. Pertanto, i cicli [WHILE] sono un'ottima alternativa per la sostituzione dei cursori.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Sostituzione di cursori in SQL Data Warehouse
Prima di procedere, è tuttavia necessario porsi la domanda seguente: "Per usare le operazioni basate su set, è possibile riscrivere questo cursore?". In molti casi la risposta è Sì ed è spesso l'approccio migliore. Un'operazione basata su set viene spesso eseguita più velocemente rispetto a un approccio iterativo riga per riga.

I cursori ad avanzamento rapido di sola lettura possono essere facilmente sostituiti con un costrutto di ciclo. Di seguito è riportato un esempio semplice. Questo esempio di codice aggiorna le statistiche per ogni tabella nel database. Scorrendo le tabelle nel ciclo, eseguire ogni comando in sequenza.

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

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).

