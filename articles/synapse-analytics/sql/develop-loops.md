---
title: Uso di cicli T-SQL
description: Suggerimenti per l'uso di cicli T-SQL, la sostituzione di cursori e lo sviluppo di soluzioni correlate con il pool SQL in sinapsi SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: baff2806b1a8c3c99546365c2496238c24b2b243
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429954"
---
# <a name="using-t-sql-loops-in-synapse-sql"></a>Uso di cicli T-SQL in sinapsi SQL
Questo articolo fornisce suggerimenti essenziali per l'uso di cicli T-SQL, la sostituzione di cursori e lo sviluppo di soluzioni correlate con il pool SQL in sinapsi SQL.

## <a name="purpose-of-while-loops"></a>Scopo dei cicli WHILE

Sinapsi SQL supporta il ciclo [while](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) per eseguire ripetutamente blocchi di istruzioni. Il ciclo WHILE continua fino a quando le condizioni specificate sono vere o fino a quando il codice termina il ciclo in modo specifico usando la parola chiave BREAK. 

I cicli nel pool SQL sono utili per la sostituzione di cursori definiti nel codice SQL. Per fortuna, quasi tutti i cursori scritti in codice SQL sono del tipo avanzamento rapido, di sola lettura. Pertanto, i cicli [WHILE] rappresentano un'ottima alternativa per la sostituzione dei cursori.

## <a name="replacing-cursors-in-sql-pool"></a>Sostituzione di cursori nel pool SQL

Prima di immergersi in, è necessario prendere in considerazione la domanda seguente: "è possibile riscrivere il cursore per l'uso di operazioni basate su set?" In molti casi, la risposta è sì ed è spesso l'approccio migliore. Un'operazione basata su set viene spesso eseguita più velocemente rispetto a un approccio iterativo riga per riga.

I cursori di sola lettura con avanzamento rapido sono facilmente sostituibili con un costrutto di ciclo. Il codice seguente è un esempio semplice. Questo esempio di codice aggiorna le statistiche per ogni tabella nel database. Scorrendo le tabelle nel ciclo, eseguire ogni comando in sequenza.

Prima di tutto, creare una tabella temporanea contenente un numero di riga univoco usato per identificare le singole istruzioni:

```sql
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

In secondo luogo, inizializzare le variabili necessarie per eseguire il ciclo:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Ora eseguire un ciclo delle istruzioni, una alla volta:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Infine eliminare la tabella temporanea creata nel primo passaggio.

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](develop-overview.md).
