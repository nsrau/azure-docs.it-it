---
title: Utilizzo di transazioni
description: Suggerimenti per l'implementazione di transazioni nel pool SQL (data warehouse) per lo sviluppo di soluzioni.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 9b9ce5110a03ec4d67b3e8af6d9b18e5ad6836af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428719"
---
# <a name="using-transactions-in-sql-pool"></a>Utilizzo di transazioni nel pool SQL

Suggerimenti per l'implementazione di transazioni nel pool SQL (data warehouse) per lo sviluppo di soluzioni.

## <a name="what-to-expect"></a>Cosa aspettarsi

Come ci si aspetterebbe, il pool SQL supporta le transazioni come parte del carico di lavoro data warehouse. Tuttavia, per garantire che le prestazioni del pool SQL vengano mantenute su larga scala, alcune funzionalità sono limitate rispetto a SQL Server. Questo articolo evidenzia le differenze ed elenca le altre.

## <a name="transaction-isolation-levels"></a>Livelli di isolamento delle transazioni

Il pool SQL implementa transazioni ACID. Per impostazione predefinita, il livello di isolamento del supporto transazionale è READ UNCOMMITTED.  È possibile modificarlo in READ COMMITTED SNAPSHOT ISOLAtion attivando l'opzione di database READ_COMMITTED_SNAPSHOT per un database utente quando si è connessi al database master.  

Una volta abilitate, tutte le transazioni in questo database vengono eseguite nell'isolamento dello SNAPSHOT READ COMMITTED e l'impostazione READ UNCOMMITTED sul livello della sessione non verrà rispettata. Per informazioni dettagliate, vedere [Opzioni ALTER database set (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest) .

## <a name="transaction-size"></a>Dimensioni delle transazioni
Le dimensioni di una singola transazione di modifica dati sono limitate. Il limite è applicato per ogni distribuzione. Per calcolare l'allocazione totale, quindi, è possibile moltiplicare il limite per il conteggio di distribuzione. 

Per calcolare approssimativamente il numero massimo di righe nella transazione, dividere il limite di distribuzione per le dimensioni totali di ogni riga. Per le colonne di lunghezza variabile valutare la possibilità di usare una lunghezza di colonna media invece delle dimensioni massime.

Ecco alcuni presupposti riportati nella tabella seguente:

* Si è verificata una distribuzione uniforme dei dati
* La lunghezza media delle righe è 250 byte

## <a name="gen2"></a>Seconda generazione

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Limite per distribuzione (GB) | Numero di distribuzioni | Dimensioni MASSIMe transazioni (GB) | Numero di righe per distribuzione | Righe max per transazione |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4.000.000 |240.000.000 |
| DW200c |1.5 |60 |90 |6.000.000 |360.000.000 |
| DW300c |2.25 |60 |135 |9.000.000 |540.000.000 |
| DW400c |3 |60 |180 |12.000.000 |720.000.000 |
| DW500c |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW1000c |7.5 |60 |450 |30.000.000 |1.800.000.000 |
| DW1500c |11,25 |60 |675 |45.000.000 |2.700.000.000 |
| DW2000c |15 |60 |900 |60.000.000 |3.600.000.000 |
| DW2500c |18,75 |60 |1125 |75 milioni |4,5 miliardi |
| DW3000c |22,5 |60 |1.350 |90.000.000 |5.400.000.000 |
| DW5000c |37,5 |60 |2.250 |150 milioni |9 miliardi |
| DW6000c |45 |60 |2.700 |180.000.000 |10.800.000.000 |
| DW7500c |56,25 |60 |3.375 |225 milioni |13,5 miliardi |
| DW10000c |75 |60 |4.500 |300.000.000 |18 miliardi |
| DW15000c |112,5 |60 |6.750 |450 milioni |27 miliardi |
| DW30000c |225 |60 |13.500 |900.000.000 |54 miliardi |

## <a name="gen1"></a>Prima generazione

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Limite per distribuzione (GB) | Numero di distribuzioni | Dimensioni MASSIMe transazioni (GB) | Numero di righe per distribuzione | Righe max per transazione |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4.000.000 |240.000.000 |
| DW200 |1.5 |60 |90 |6.000.000 |360.000.000 |
| DW300 |2.25 |60 |135 |9.000.000 |540.000.000 |
| DW400 |3 |60 |180 |12.000.000 |720.000.000 |
| DW500 |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW600 |4.5 |60 |270 |18.000.000 |1.080.000.000 |
| DW1000 |7.5 |60 |450 |30.000.000 |1.800.000.000 |
| DW1200 |9 |60 |540 |36.000.000 |2.160.000.000 |
| DW1500 |11,25 |60 |675 |45.000.000 |2.700.000.000 |
| DW2000 |15 |60 |900 |60.000.000 |3.600.000.000 |
| DW3000 |22,5 |60 |1.350 |90.000.000 |5.400.000.000 |
| DW6000 |45 |60 |2.700 |180.000.000 |10.800.000.000 |

Il limite delle dimensioni delle transazioni viene applicato per transazione o per operazione. Non viene applicato in tutte le transazioni simultanee. A ogni transazione è quindi consentito scrivere questa quantità di dati nel log.

Per ottimizzare e ridurre al minimo la quantità di dati scritti nel log, vedere l'articolo [Procedure consigliate per le transazioni](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

> [!WARNING]
> Le dimensioni massime delle transazioni possono essere ottenute solo per le tabelle distribuite HASH o ROUND_ROBIN in cui i dati sono distribuiti in modo uniforme. Se la transazione scrive dati in modo asimmetrico nelle distribuzioni, è probabile che il limite venga raggiunto prima di raggiungere le dimensioni massime delle transazioni.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Stato della transazione

Il pool SQL usa la funzione XACT_STATE () per segnalare una transazione non riuscita usando il valore-2. Questo valore indica che la transazione non è riuscita ed è contrassegnata solo per il rollback.

> [!NOTE]
> L'uso di -2 da parte della funzione XACT_STATE per indicare una transazione non riuscita rappresenta un comportamento diverso da SQL Server. SQL Server usa il valore -1 per rappresentare una transazione di cui non è possibile eseguire il commit. SQL Server è in grado di tollerare alcuni errori all'interno di una transazione senza doverne indicare l'impossibilità di eseguire il commit. Ad esempio, `SELECT 1/0` causa un errore, ma non applica alla transazione lo stato per cui non è possibile eseguire il commit. SQL Server consente anche letture nella transazione di cui non è possibile eseguire il commit. Tuttavia, il pool SQL non consente di eseguire questa operazione. Se si verifica un errore all'interno di una transazione del pool SQL, entrerà automaticamente nello stato-2 e non sarà possibile effettuare altre istruzioni SELECT fino a quando non viene eseguito il rollback dell'istruzione. È quindi importante verificare il codice dell'applicazione per vedere se usa XACT_STATE(), perché può essere necessario modificarlo.

Ad esempio, in SQL Server potrebbe essere visualizzata una transazione simile alla seguente:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Il codice precedente genera il messaggio di errore seguente:

Msg 111233, livello 16, stato 1, riga 1 111233; La transazione corrente è stata interrotta ed è stato eseguito il rollback di tutte le modifiche in sospeso. Causa: non è stato eseguito il rollback in modo esplicito di una transazione in uno stato di solo rollback prima di un'istruzione DDL, DML o SELECT.

Non si otterrà l'output delle funzioni di ERROR_*.

Nel pool SQL è necessario modificare leggermente il codice:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

A questo punto si osserva il comportamento previsto. L'errore della transazione viene gestito e le funzioni ERROR_* forniscono i valori previsti.

Ciò dimostra che il ROLLBACK della transazione doveva essere eseguito prima della lettura delle informazioni sull'errore nel blocco CATCH.

## <a name="error_line-function"></a>Funzione Error_Line()

È inoltre importante notare che il pool SQL non implementa né supporta la funzione ERROR_LINE (). Se è presente nel codice, è necessario rimuoverlo per renderlo conforme al pool SQL. Anziché implementare una funzionalità equivalente, usare etichette di query nel codice. Per altri dettagli, vedere l'articolo [LABEL](develop-label.md).

## <a name="using-throw-and-raiserror"></a>Uso di THROW e RAISERROR

THROW è l'implementazione più moderna per la generazione di eccezioni nel pool SQL, ma è supportato anche RAISERROR. Esistono tuttavia alcune differenze a cui vale la pena prestare attenzione.

* I numeri dei messaggi di errore definiti dall'utente non possono essere compresi nell'intervallo da 100.000 a 150.000 per THROW
* I messaggi di errore di RAISERROR sono fissati a 50.000.
* L'uso di sys.messages non è supportato.

## <a name="limitations"></a>Limitazioni

Il pool SQL presenta alcune altre restrizioni correlate alle transazioni.

Ecco quali sono:

* Nessuna transazione distribuita
* Non sono consentite transazioni annidate
* Non sono consentiti punti di salvataggio
* Nessuna transazione denominata
* Nessuna transazione contrassegnata
* Nessun supporto per DDL come CREATE TABLE all'interno di una transazione definita dall'utente

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'ottimizzazione delle transazioni, vedere [Procedure consigliate per le transazioni](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Sono inoltre disponibili guide alle procedure consigliate aggiuntive per il [pool SQL](best-practices-sql-pool.md) e [SQL su richiesta (anteprima)](on-demand-workspace-overview.md).
