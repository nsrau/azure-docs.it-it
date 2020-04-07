---
title: Usare le transazioni nel pool SQL SynapseUse transactions in Synapse SQL pool
description: Questo articolo include suggerimenti per l'implementazione di transazioni e lo sviluppo di soluzioni nel pool SQL Synapse.This article includes tips for implementing transactions and developing solutions in Synapse SQL pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d9578653ff8074fee8336df447caf119f79febe0
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745263"
---
# <a name="use-transactions-in-synapse-sql-pool"></a>Usare le transazioni nel pool SQL SynapseUse transactions in Synapse SQL pool

Questo articolo include suggerimenti per l'implementazione di transazioni e lo sviluppo di soluzioni nel pool SQL.

## <a name="what-to-expect"></a>Cosa aspettarsi

Come ci si aspetterebbe, il pool SQL supporta le transazioni come parte del carico di lavoro del data warehouse. Tuttavia, per garantire che il pool SQL venga mantenuto su larga scala, alcune funzionalità sono limitate rispetto a SQL ServerSQL Server.However, to ensure SQL pool is maintained at scale, some features are limited when compared to SQL ServerSQL Server. In questo articolo vengono evidenziate le differenze.

## <a name="transaction-isolation-levels"></a>Livelli di isolamento delle transazioni

Il pool SQL implementa le transazioni ACID. Il livello di isolamento del supporto transazionale è l'impostazione predefinita per READ UNCOMMITTED.  È possibile modificarlo in READ COMMITTED SNAPSHOT ISOLATION attivando l'opzione READ_COMMITTED_SNAPSHOT database per un database utente quando si è connessi al database master.  

Una volta abilitata, tutte le transazioni in questo database vengono eseguite in READ COMMITTED SNAPSHOT ISOLATION e l'impostazione di READ UNCOMMITTED a livello di sessione non verrà rispettata. Per informazioni [dettagliate, vedere ALTER DATABASE SET options (Transact-SQL).](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="transaction-size"></a>Dimensioni delle transazioni

Le dimensioni di una singola transazione di modifica dati sono limitate. Il limite è applicato per ogni distribuzione. Per calcolare l'allocazione totale, quindi, è possibile moltiplicare il limite per il conteggio di distribuzione.

Per calcolare approssimativamente il numero massimo di righe nella transazione, dividere il limite di distribuzione per le dimensioni totali di ogni riga. Per le colonne di lunghezza variabile valutare la possibilità di usare una lunghezza di colonna media invece delle dimensioni massime.

Nella tabella seguente sono state formulate due ipotesi:

* Si è verificata una distribuzione uniforme dei dati
* La lunghezza media delle righe è 250 byte

## <a name="gen2"></a>Seconda generazione

| [DWU](sql-data-warehouse-overview-what-is.md) | Cap per distribuzione (GB) | Numero di distribuzioni | Dimensione transazione MAX (GB) | Numero di righe per distribuzione | Righe max per transazione |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4.000.000 |240.000.000 |
| DW200c |1.5 |60 |90 |6.000.000 |360.000.000 |
| DW300c |2.25 |60 |135 |9.000.000 |540.000.000 |
| DW400c |3 |60 |180 |12.000.000 |720.000.000 |
| DW500c |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW1000c |7.5 |60 |450 |30.000.000 |1.800.000.000 |
| DW1500c |11,25 |60 |675 |45.000.000 |2.700.000.000 |
| DW2000c |15 |60 |900 |60.000.000 |3.600.000.000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22,5 |60 |1.350 |90.000.000 |5.400.000.000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2.700 |180.000.000 |10.800.000.000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4.500 |300.000.000 |18,000,000,000 |
| DW15000c |112.5 |60 |6.750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900.000.000 |54,000,000,000 |

## <a name="gen1"></a>Prima generazione

| [DWU](sql-data-warehouse-overview-what-is.md) | Cap per distribuzione (GB) | Numero di distribuzioni | Dimensione transazione MAX (GB) | Numero di righe per distribuzione | Righe max per transazione |
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

Per ottimizzare e ridurre al minimo la quantità di dati scritti nel log, vedere l'articolo [Procedure consigliate per le transazioni](sql-data-warehouse-develop-best-practices-transactions.md).

> [!WARNING]
> Le dimensioni massime delle transazioni possono essere ottenute solo per le tabelle distribuite HASH o ROUND_ROBIN in cui i dati sono distribuiti in modo uniforme. Se la transazione scrive dati in modo asimmetrico nelle distribuzioni, è probabile che il limite venga raggiunto prima di raggiungere le dimensioni massime delle transazioni.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Stato della transazione

Il pool SQL utilizza la funzione XACT_STATE() per segnalare una transazione non riuscita utilizzando il valore -2. Questo valore indica che la transazione non è riuscita ed è contrassegnata solo per il rollback.

> [!NOTE]
> L'uso di -2 da parte della funzione XACT_STATE per indicare una transazione non riuscita rappresenta un comportamento diverso da SQL Server. SQL Server usa il valore -1 per rappresentare una transazione di cui non è possibile eseguire il commit. SQL Server è in grado di tollerare alcuni errori all'interno di una transazione senza doverne indicare l'impossibilità di eseguire il commit. Ad esempio, `SELECT 1/0` causerebbe un errore ma non imporrebilmente una transazione in uno stato di cui non è possibile eseguire il commit.

SQL Server consente anche letture nella transazione di cui non è possibile eseguire il commit. Tuttavia, il pool SQL non consente di eseguire questa operazione. Se si verifica un errore all'interno di una transazione del pool SQL, verrà automaticamente immesso lo stato -2 e non sarà possibile eseguire ulteriori istruzioni SELECT fino a quando non è stato eseguito il rollback dell'istruzione.

Di conseguenza, è importante verificare che il codice dell'applicazione per verificare se utilizza XACT_STATE() in quanto potrebbe essere necessario apportare modifiche al codice.

Ad esempio, in SQL Server, è possibile visualizzare una transazione simile alla seguente:For example, in SQL Server, you might see a transaction that looks like the following:

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

Msg 111233, livello 16, stato 1, riga 1 111233; La transazione corrente è stata interrotta ed è stato eseguito il rollback di tutte le modifiche in sospeso. La causa di questo problema è che una transazione in uno stato di solo rollback non viene eseguito in modo esplicito il rollback prima di un DDL, DML o SELECT istruzione.

Non si otterrà l'output delle funzioni di ERROR_*.

Nel pool SQL il codice deve essere leggermente modificato:In SQL pool the code needs to be slightly ltered:

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

Vale anche la pena notare che il pool SQL non implementa né supporta la funzione ERROR_LINE(). Se si dispone di questo nel codice, è necessario rimuoverlo per essere compatibile con il pool SQL.

Anziché implementare una funzionalità equivalente, usare etichette di query nel codice. Per altri dettagli, vedere l'articolo [LABEL](sql-data-warehouse-develop-label.md).

## <a name="using-throw-and-raiserror"></a>Uso di THROW e RAISERROR

THROW è l'implementazione più moderna per la generazione di eccezioni nel pool SQL, ma è supportato anche RAISERROR. Esistono tuttavia alcune differenze a cui vale la pena prestare attenzione.

* I numeri dei messaggi di errore definiti dall'utente non possono essere compresi nell'intervallo da 100.000 a 150.000 per THROW
* I messaggi di errore di RAISERROR sono fissati a 50.000.
* L'uso di sys.messages non è supportato.

## <a name="limitations"></a>Limitazioni

Il pool SQL ha alcune altre restrizioni relative alle transazioni.

Ecco quali sono:

* Nessuna transazione distribuita
* Non sono consentite transazioni annidate
* Non sono consentiti punti di salvataggio
* Nessuna transazione denominata
* Nessuna transazione contrassegnata
* Nessun supporto per DDL come CREATE TABLE all'interno di una transazione definita dall'utente

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'ottimizzazione delle transazioni, vedere [Procedure consigliate per le transazioni](sql-data-warehouse-develop-best-practices-transactions.md). Per informazioni sulle altre procedure consigliate per il pool SQL, vedere [Procedure consigliate per il pool SQL](sql-data-warehouse-best-practices.md).
