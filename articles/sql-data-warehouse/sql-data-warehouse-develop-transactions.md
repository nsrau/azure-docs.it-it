---
title: Transazioni in SQL Data Warehouse | Documentazione Microsoft
description: Suggerimenti per l&quot;implementazione di transazioni in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: ae621788-e575-41f5-8bfe-fa04dc4b0b53
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: a0582c71e786ae5365e39a5f161b63e946435b2e
ms.lasthandoff: 03/10/2017


---
# <a name="transactions-in-sql-data-warehouse"></a>Transazioni in SQL Data Warehouse
Come si può immaginare, SQL Data Warehouse supporta le transazioni come parte del carico di lavoro del data warehouse. Tuttavia, per garantire che le prestazioni di SQL Data Warehouse siano mantenute al massimo livello, alcune funzionalità sono limitate rispetto a SQL Server. Questo articolo evidenzia le differenze ed elenca le altre. 

## <a name="transaction-isolation-levels"></a>Livelli di isolamento delle transazioni
SQL Data Warehouse implementa le transazioni ACID. Tuttavia, l'isolamento del supporto delle transazioni è limitato a `READ UNCOMMITTED` e non può essere modificato. È possibile implementare numerosi metodi di codifica per evitare letture dirty dei dati se ciò costituisce un problema. I metodi più diffusi usano CTAS e il cambio della partizione di tabella (spesso noto come modello di finestra temporale scorrevole) per impedire agli utenti di eseguire query sui dati ancora in fase di preparazione. Anche le visualizzazioni che filtrano preventivamente i dati costituiscono un approccio comune.  

## <a name="transaction-size"></a>Dimensioni delle transazioni
Le dimensioni di una singola transazione di modifica dati sono limitate. Il limite è attualmente applicato "per ogni distribuzione". Per calcolare l'allocazione totale, quindi, è possibile moltiplicare il limite per il conteggio di distribuzione. Per calcolare approssimativamente il numero massimo di righe nella transazione, dividere il limite di distribuzione per le dimensioni totali di ogni riga. Per le colonne di lunghezza variabile valutare la possibilità di usare una lunghezza di colonna media invece delle dimensioni massime.

Ecco alcuni presupposti riportati nella tabella seguente:

* Si è verificata una distribuzione uniforme dei dati 
* La lunghezza media delle righe è 250 byte

| [DWU][DWU] | Limite per ogni distribuzione (GiB) | Numero di distribuzioni | Dimensioni MAX delle transazioni (GiB) | # Righe per distribuzione | Righe max per transazione |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4.000.000 |240.000.000 |
| DW200 |1,5 |60 |90 |6.000.000 |360.000.000 |
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

Per ottimizzare e ridurre al minimo la quantità di dati scritti nel log, vedere [Ottimizzazione delle transazioni per SQL Data Warehouse][Transactions best practices].

> [!WARNING]
> Le dimensioni massime delle transazioni possono essere ottenute solo per le tabelle distribuite HASH o ROUND_ROBIN in cui i dati sono distribuiti in modo uniforme. Se la transazione scrive dati in modo asimmetrico nelle distribuzioni, è probabile che il limite venga raggiunto prima di raggiungere le dimensioni massime delle transazioni.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Stato della transazione
SQL Data Warehouse usa la funzione XACT_STATE() per segnalare una transazione non riuscita con il valore -2. Ciò significa che la transazione non è riuscita ed è contrassegnata solo per il rollback.

> [!NOTE]
> L'uso di -2 da parte della funzione XACT_STATE per indicare una transazione non riuscita rappresenta un comportamento diverso da SQL Server. SQL Server usa il valore -1 per rappresentare una transazione di cui non è possibile eseguire il commit. SQL Server è in grado di tollerare alcuni errori all'interno di una transazione senza doverne indicare l'impossibilità di eseguire il commit. Ad esempio, `SELECT 1/0` causa un errore, ma non applica alla transazione lo stato per cui non è possibile eseguire il commit. SQL Server consente anche letture nella transazione di cui non è possibile eseguire il commit. SQL Data Warehouse, invece, non consente questa operazione. Se si verifica un errore in una transazione SQL Data Warehouse, verrà inserito automaticamente lo stato-2 e non sarà più possibile eseguire ulteriori istruzioni SELECT finché non verrà eseguito il rollback dell'istruzione. È quindi importante verificare il codice dell'applicazione per vedere se usa XACT_STATE(), perché può essere necessario modificarlo.
> 
> 

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
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Se si lascia il codice come qui sopra, si otterrà il seguente messaggio di errore:

Msg 111233, livello 16, stato 1, riga 1 111233;La transazione corrente è stata interrotta ed è stato eseguito il rollback di tutte le modifiche in sospeso. Causa: non è stato eseguito il rollback in modo esplicito di una transazione in uno stato di solo rollback prima di un'istruzione DDL, DML o SELECT.

Inoltre non si otterrà l'output delle funzioni di ERROR_*.

È quindi necessario modificare leggermente il codice in SQL Data Warehouse:

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
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
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

Ciò dimostra che il `ROLLBACK` della transazione doveva essere eseguito prima della lettura delle informazioni sull'errore nel blocco `CATCH`.

## <a name="errorline-function"></a>Funzione Error_Line()
È importante sottolineare anche che SQL Data Warehouse non implementa né supporta la funzione ERROR_LINE(). Se è contenuta nel codice sarà necessario rimuoverla per renderlo compatibile con SQL Data Warehouse. Anziché implementare una funzionalità equivalente, usare etichette di query nel codice. Per altre informazioni su questa funzionalità, vedere l'articolo [Usare etichette per instrumentare query in SQL Data Warehouse][LABEL].

## <a name="using-throw-and-raiserror"></a>Uso di THROW e RAISERROR
THROW è l'implementazione più moderna per la generazione di eccezioni in SQL Data Warehouse, ma è supportata anche RAISERROR. Esistono tuttavia alcune differenze a cui vale la pena prestare attenzione.

* I numeri dei messaggi di errore definiti dall'utente non possono essere compresi nell'intervallo da 100.000 a 150.000 per THROW.
* I messaggi di errore di RAISERROR sono fissati a 50.000.
* L'uso di sys.messages non è supportato.

## <a name="limitiations"></a>Limitazioni
SQL Data Warehouse presenta qualche altra limitazione relativa alle transazioni.

Ecco quali sono:

* Nessuna transazione distribuita
* Non sono consentite transazioni annidate
* Non sono consentiti punti di salvataggio
* Nessuna transazione denominata
* Nessuna transazione contrassegnata
* Nessun supporto per DDL come `CREATE TABLE` all'interno di una transazione definita dall'utente

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'ottimizzazione delle transazioni, vedere [Ottimizzazione delle transazioni per SQL Data Warehouse][Transactions best practices].  Per altre informazioni sulle procedure consigliate per SQL Data Warehouse, vedere [Procedure consigliate per Azure SQL Data Warehouse][SQL Data Warehouse best practices].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Transactions best practices]: ./sql-data-warehouse-develop-best-practices-transactions.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->

