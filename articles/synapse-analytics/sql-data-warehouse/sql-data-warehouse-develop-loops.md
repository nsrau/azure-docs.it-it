---
title: Utilizzo di cicli T-SQLUsing T-SQL loops
description: Suggerimenti per lo sviluppo di soluzioni utilizzando i cicli T-SQL e la sostituzione dei cursori nel pool SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 72a39804931c0834233e91190aacffa8d35912df
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633485"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>Utilizzo di cicli T-SQL nel pool Sql SynapseUsing T-SQL loops in Synapse SQL pool

In questo articolo sono inclusi suggerimenti per lo sviluppo di soluzioni di pool SQL che usano cicli T-SQL e la sostituzione di cursori.

## <a name="purpose-of-while-loops"></a>Scopo dei cicli WHILE

Il pool SQL Synapse supporta il ciclo [WHILE](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per l'esecuzione ripetuta di blocchi di istruzioni. Il ciclo WHILE continua fino a quando le condizioni specificate sono vere o fino a quando il codice termina il ciclo in modo specifico usando la parola chiave BREAK.

I cicli sono utili per la sostituzione di cursori definiti nel codice SQL. Per fortuna, quasi tutti i cursori scritti in codice SQL sono del tipo avanzamento rapido, di sola lettura. Quindi, i cicli WHILE sono un'ottima alternativa per la sostituzione dei cursori.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Sostituzione dei cursori nel pool SQL Synapse

Tuttavia, prima di tuffarsi in testa prima dovresti porti la seguente domanda: "Questo cursore potrebbe essere riscritto per utilizzare operazioni basate su set?"

In molti casi, la risposta è sì ed è spesso l'approccio migliore. Un'operazione basata su set viene spesso eseguita più velocemente rispetto a un approccio iterativo riga per riga.

I cursori ad avanzamento rapido di sola lettura possono essere facilmente sostituiti con un costrutto di ciclo. L'esempio seguente è semplice. Questo esempio di codice aggiorna le statistiche per ogni tabella nel database. Scorrendo le tabelle nel ciclo, eseguire ogni comando in sequenza.

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

Inizializzare quindi le variabili necessarie per eseguire il ciclo:

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

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).
