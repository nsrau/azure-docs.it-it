---
title: Usare le transazioni nel pool di SQL sinapsi
description: Questo articolo include suggerimenti per l'implementazione di transazioni e lo sviluppo di soluzioni nel pool SQL sinapsi.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 40a9e5268b7fccc5c01775c10e55eee47f1aaf3d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213381"
---
# <a name="use-transactions-in-synapse-sql-pool"></a>Usare le transazioni nel pool di SQL sinapsi

Questo articolo include suggerimenti per l'implementazione di transazioni e lo sviluppo di soluzioni nel pool SQL.

## <a name="what-to-expect"></a>Cosa aspettarsi

Come si può immaginare, il pool SQL supporta le transazioni come parte del carico di lavoro del data warehouse. Tuttavia, per garantire che il pool SQL venga mantenuto su larga scala, alcune funzionalità sono limitate rispetto a SQL Server. In questo articolo vengono evidenziate le differenze.

## <a name="transaction-isolation-levels"></a>Livelli di isolamento delle transazioni

Il pool SQL implementa le transazioni ACID. Per impostazione predefinita, il livello di isolamento del supporto transazionale è READ UNCOMMITTED.  È possibile modificarlo in READ COMMITTED SNAPSHOT ISOLATION impostando su ON l'opzione di database READ_COMMITTED_SNAPSHOT per un database utente quando si è connessi al database master.  

Dopo l'abilitazione, tutte le transazioni in questo database vengono eseguite con READ COMMITTED SNAPSHOT ISOLATION e l'impostazione READ UNCOMMITTED a livello della sessione non verrà rispettata. Per informazioni dettagliate, vedere [Opzioni di ALTER DATABASE SET (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="transaction-size"></a>Dimensioni delle transazioni

Le dimensioni di una singola transazione di modifica dati sono limitate. Il limite è applicato per ogni distribuzione. Per calcolare l'allocazione totale, quindi, è possibile moltiplicare il limite per il conteggio di distribuzione.

Per calcolare approssimativamente il numero massimo di righe nella transazione, dividere il limite di distribuzione per le dimensioni totali di ogni riga. Per le colonne di lunghezza variabile valutare la possibilità di usare una lunghezza di colonna media invece delle dimensioni massime.

Nella tabella seguente sono stati apportati due presupposti:

* Si è verificata una distribuzione uniforme dei dati
* La lunghezza media delle righe è 250 byte

## <a name="gen2"></a>Seconda generazione

| [DWU](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Limite per ogni distribuzione (GB) | Numero di distribuzioni | Dimensioni MAX delle transazioni (GB) | Numero di righe per distribuzione | Righe max per transazione |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4\.000.000 |240.000.000 |
| DW200c |1.5 |60 |90 |6\.000.000 |360.000.000 |
| DW300c |2.25 |60 |135 |9\.000.000 |540.000.000 |
| DW400c |3 |60 |180 |12.000.000 |720.000.000 |
| DW500c |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW1000c |7.5 |60 |450 |30.000.000 |1\.800.000.000 |
| DW1500c |11,25 |60 |675 |45.000.000 |2\.700.000.000 |
| DW2000c |15 |60 |900 |60.000.000 |3\.600.000.000 |
| DW2500c |18,75 |60 |1125 |75.000.000 |4\.500.000.000 |
| DW3000c |22,5 |60 |1\.350 |90.000.000 |5\.400.000.000 |
| DW5000c |37,5 |60 |2\.250 |150.000.000 |9\.000.000.000 |
| DW6000c |45 |60 |2\.700 |180.000.000 |10.800.000.000 |
| DW7500c |56,25 |60 |3\.375 |225.000.000 |13.500.000.000 |
| DW10000c |75 |60 |4\.500 |300.000.000 |18.000.000.000 |
| DW15000c |112,5 |60 |6\.750 |450.000.000 |27.000.000.000 |
| DW30000c |225 |60 |13.500 |900.000.000 |54.000.000.000 |

## <a name="gen1"></a>Prima generazione

| [DWU](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Limite per ogni distribuzione (GB) | Numero di distribuzioni | Dimensioni MAX delle transazioni (GB) | Numero di righe per distribuzione | Righe max per transazione |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4\.000.000 |240.000.000 |
| DW200 |1.5 |60 |90 |6\.000.000 |360.000.000 |
| DW300 |2.25 |60 |135 |9\.000.000 |540.000.000 |
| DW400 |3 |60 |180 |12.000.000 |720.000.000 |
| DW500 |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW600 |4.5 |60 |270 |18.000.000 |1\.080.000.000 |
| DW1000 |7.5 |60 |450 |30.000.000 |1\.800.000.000 |
| DW1200 |9 |60 |540 |36.000.000 |2\.160.000.000 |
| DW1500 |11,25 |60 |675 |45.000.000 |2\.700.000.000 |
| DW2000 |15 |60 |900 |60.000.000 |3\.600.000.000 |
| DW3000 |22,5 |60 |1\.350 |90.000.000 |5\.400.000.000 |
| DW6000 |45 |60 |2\.700 |180.000.000 |10.800.000.000 |

Il limite delle dimensioni delle transazioni viene applicato per transazione o per operazione. Non viene applicato in tutte le transazioni simultanee. A ogni transazione è quindi consentito scrivere questa quantità di dati nel log.

Per ottimizzare e ridurre al minimo la quantità di dati scritti nel log, vedere l'articolo [Procedure consigliate per le transazioni](sql-data-warehouse-develop-best-practices-transactions.md).

> [!WARNING]
> Le dimensioni massime delle transazioni possono essere ottenute solo per le tabelle distribuite HASH o ROUND_ROBIN in cui i dati sono distribuiti in modo uniforme. Se la transazione scrive dati in modo asimmetrico nelle distribuzioni, è probabile che il limite venga raggiunto prima di raggiungere le dimensioni massime delle transazioni.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Stato della transazione

Il pool SQL usa la funzione XACT_STATE() per segnalare una transazione non riuscita con il valore -2. Questo valore indica che la transazione non è riuscita ed è contrassegnata solo per il rollback.

> [!NOTE]
> L'uso di -2 da parte della funzione XACT_STATE per indicare una transazione non riuscita rappresenta un comportamento diverso da SQL Server. SQL Server usa il valore -1 per rappresentare una transazione di cui non è possibile eseguire il commit. SQL Server è in grado di tollerare alcuni errori all'interno di una transazione senza doverne indicare l'impossibilità di eseguire il commit. Ad esempio, `SELECT 1/0` provocherebbe un errore, ma non forzare una transazione in uno stato di cui non è possibile eseguire il commit.

SQL Server consente anche letture nella transazione di cui non è possibile eseguire il commit. Il pool SQL, invece, non consente questa operazione. Se si verifica un errore all'interno di una transazione del pool SQL, entrerà automaticamente nello stato-2 e non sarà possibile effettuare altre istruzioni SELECT fino a quando non viene eseguito il rollback dell'istruzione.

Di conseguenza, è importante verificare che il codice dell'applicazione per verificare se usa XACT_STATE (), perché potrebbe essere necessario apportare modifiche al codice.

Ad esempio, in SQL Server, è possibile che venga visualizzata una transazione simile alla seguente:

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

Msg 111233, livello 16, stato 1, riga 1 111233; La transazione corrente è stata interrotta ed è stato eseguito il rollback di tutte le modifiche in sospeso. La causa di questo problema è che non viene eseguito il rollback esplicito di una transazione in uno stato di solo rollback prima di un'istruzione DDL, DML o SELECT.

Non si otterrà l'output delle funzioni di ERROR_*.

È quindi necessario modificare leggermente il codice del pool SQL:

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

È importante sottolineare anche che il pool SQL non implementa né supporta la funzione ERROR_LINE(). Se è contenuta nel codice, sarà necessario rimuoverla per renderlo compatibile con il pool SQL.

Anziché implementare una funzionalità equivalente, usare etichette di query nel codice. Per altri dettagli, vedere l'articolo [LABEL](sql-data-warehouse-develop-label.md).

## <a name="using-throw-and-raiserror"></a>Uso di THROW e RAISERROR

THROW è l'implementazione più moderna per la generazione di eccezioni nel pool SQL, ma è supportata anche RAISERROR. Esistono tuttavia alcune differenze a cui vale la pena prestare attenzione.

* I numeri dei messaggi di errore definiti dall'utente non possono essere compresi nell'intervallo da 100.000 a 150.000 per THROW
* I messaggi di errore di RAISERROR sono fissati a 50.000.
* L'uso di sys.messages non è supportato.

## <a name="limitations"></a>Limitazioni

Il pool SQL presenta qualche altra limitazione relativa alle transazioni.

Ecco quali sono:

* Nessuna transazione distribuita
* Non sono consentite transazioni annidate
* Non sono consentiti punti di salvataggio
* Nessuna transazione denominata
* Nessuna transazione contrassegnata
* Nessun supporto per DDL come CREATE TABLE all'interno di una transazione definita dall'utente

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'ottimizzazione delle transazioni, vedere [Procedure consigliate per le transazioni](sql-data-warehouse-develop-best-practices-transactions.md). Per ulteriori informazioni sulle procedure consigliate per il pool SQL, vedere procedure consigliate per il [pool SQL](sql-data-warehouse-best-practices.md).
