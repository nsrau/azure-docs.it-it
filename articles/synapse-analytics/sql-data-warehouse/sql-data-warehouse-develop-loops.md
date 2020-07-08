---
title: Uso di cicli T-SQL
description: Suggerimenti per lo sviluppo di soluzioni che usano cicli T-SQL e la sostituzione di cursori nel pool SQL sinapsi.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 25dad01a54b6ffe08656379340f58e0fe70ec666
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213415"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>Uso di cicli T-SQL nel pool di SQL sinapsi

In questo articolo sono disponibili suggerimenti per lo sviluppo di soluzioni di pool SQL con cicli T-SQL e la sostituzione di cursori.

## <a name="purpose-of-while-loops"></a>Scopo dei cicli WHILE

Il pool SQL sinapsi supporta il ciclo [while](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per eseguire ripetutamente blocchi di istruzioni. Il ciclo WHILE continua fino a quando le condizioni specificate sono vere o fino a quando il codice termina il ciclo in modo specifico usando la parola chiave BREAK.

I cicli sono utili per la sostituzione di cursori definiti nel codice SQL. Per fortuna, quasi tutti i cursori scritti in codice SQL sono del tipo avanzamento rapido, di sola lettura. Pertanto, i cicli WHILE rappresentano un'ottima alternativa per la sostituzione dei cursori.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Sostituzione di cursori nel pool SQL sinapsi

Tuttavia, prima di iniziare a esaminare prima di tutto, è necessario porsi la domanda seguente: "è possibile riscrivere il cursore per usare le operazioni basate su set?"

In molti casi, la risposta è sì ed è spesso l'approccio migliore. Un'operazione basata su set viene spesso eseguita più velocemente rispetto a un approccio iterativo riga per riga.

I cursori ad avanzamento rapido di sola lettura possono essere facilmente sostituiti con un costrutto di ciclo. L'esempio seguente è uno semplice. Questo esempio di codice aggiorna le statistiche per ogni tabella nel database. Scorrendo le tabelle nel ciclo, eseguire ogni comando in sequenza.

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
