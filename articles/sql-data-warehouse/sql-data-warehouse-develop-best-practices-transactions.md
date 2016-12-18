---
title: Ottimizzazione delle transazioni per SQL Data Warehouse | Documentazione Microsoft
description: Indicazioni sulle procedure consigliate per la scrittura di aggiornamenti di transazioni efficienti in Azure SQL Data Warehouse
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 6f326f26-8a54-49df-a482-9c96a58db371
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ed017b542de11a5e8abe46e1651b04cb61c77265


---
# <a name="optimizing-transactions-for-sql-data-warehouse"></a>Ottimizzazione delle transazioni per SQL Data Warehouse
Questo articolo illustra come ottimizzare le prestazioni del codice transazionale riducendo al contempo il rischio di rollback di lunga durata.

## <a name="transactions-and-logging"></a>Transazioni e registrazione
Le transazioni sono un componente importante in un motore di database relazionale. SQL Data Warehouse usa le transazioni durante la modifica dei dati. Queste operazioni possono essere esplicite o implicite. Le istruzioni singole `INSERT`, `UPDATE` e `DELETE` sono esempi di transazioni implicite. Le transazioni esplicite sono scritte in modo esplicito dallo sviluppatore con `BEGIN TRAN`, `COMMIT TRAN` o `ROLLBACK TRAN` e vengono usate in genere quando è necessario collegare più istruzioni di modifica in un'unica unità atomica. 

Azure SQL Data Warehouse esegue il commit delle modifiche al database usando i log delle transazioni. Ogni distribuzione ha un proprio log delle transazioni. Le scritture del log delle transazioni avvengono in modo automatico e non è richiesta alcuna configurazione. Tuttavia, se da un lato questo processo garantisce la scrittura dall'altro provoca un sovraccarico nel sistema. È possibile ridurre al minimo tale impatto scrivendo codice efficiente a livello transazionale. Il codice efficiente a livello transazionale rientra in due categorie generali.

* Sfruttare costrutti di registrazione minima, dove possibile.
* Elaborare i dati tramite batch con ambito per evitare singole transazioni a esecuzione prolungata.
* Adottare un modello di cambio di partizione per apportare modifiche estese a una determinata partizione.

## <a name="minimal-vs-full-logging"></a>Confronto tra registrazione minima e registrazione completa
A differenza delle operazioni con registrazione completa, che usano il log delle transazioni per tenere traccia di ogni modifica di riga, le operazioni con registrazione minima tengono traccia unicamente delle allocazioni di extent e delle modifiche ai metadati. La registrazione minima prevede quindi la registrazione delle sole informazioni necessarie per eseguire il rollback della transazione in caso di errore o di richiesta esplicita (`ROLLBACK TRAN`). Dato che nel log delle transazioni viene registrata una quantità di informazioni notevolmente inferiore, le operazioni con registrazione minima offrono prestazioni migliori rispetto alle operazioni con registrazione completa di dimensioni simili. Il minor numero di scritture nel log delle transazioni comporta anche la generazione di una quantità molto inferiore di dati di log e quindi operazioni di I/O più efficienti.

I limiti di sicurezza delle transazioni si applicano solo alle operazioni con registrazione completa.

> [!NOTE]
> le operazioni con registrazione minima possono partecipare alle transazioni esplicite. È possibile eseguire il rollback delle operazioni con registrazione minima, dal momento che viene tenuta traccia di tutte le modifiche alle strutture di allocazione. È importante comprendere che la registrazione delle modifiche è minima e non assente.
> 
> 

## <a name="minimally-logged-operations"></a>Operazioni con registrazione minima
Le operazioni indicate di seguito sono compatibili con la registrazione minima:

* CREATE TABLE AS SELECT ([CTAS][CTAS])
* INSERT..SELECT
* CREATE INDEX
* ALTER INDEX REBUILD
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Le operazioni di spostamento dei dati interni (ad esempio `BROADCAST` e `SHUFFLE`) non sono interessate dal limite di sicurezza delle transazioni.
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Registrazione minima con caricamento bulk
`CTAS` e `INSERT...SELECT` sono entrambe operazioni di caricamento bulk. Tuttavia, entrambe sono influenzate dalla definizione della tabella di destinazione e variano a seconda dello scenario di caricamento. La tabella riportata di seguito indica la registrazione completa o minima delle operazioni bulk elencate:  

| Indice primario | Scenario di caricamento | Modalità di registrazione |
| --- | --- | --- |
| Heap |Qualsiasi |**Minima** |
| Indice cluster |Tabella di destinazione vuota |**Minima** |
| Indice cluster |Le righe caricate non si sovrappongono alle pagine esistenti nella destinazione |**Minima** |
| Indice cluster |Le righe caricate si sovrappongono alle pagine esistenti nella destinazione |Completa |
| Indice columnstore cluster |Dimensioni batch >= 102.400 per ogni distribuzione allineata alle partizioni |**Minima** |
| Indice columnstore cluster |Dimensioni batch < 102.400 per ogni distribuzione allineata alle partizioni |Completa |

Si noti che eventuali scritture di aggiornamento di indici secondari o non cluster saranno sempre operazioni con registrazione completa.

> [!IMPORTANT]
> SQL Data Warehouse ha 60 distribuzioni. Di conseguenza, supponendo che tutte le righe siano distribuite in modo uniforme e che vengano inserite in una singola partizione, il batch dovrà contenere almeno 6.144.000 di righe per la registrazione minima durante la scrittura un indice columnstore cluster. Se la tabella è partizionata e le righe da inserire si estendono oltre i limiti della partizione, saranno necessari 6.144.000 di righe per limite di partizione, supponendo una distribuzione uniforme dei dati. Per la registrazione minima dell'inserimento nella distribuzione, ogni partizione in ogni distribuzione deve superare singolarmente la soglia di 102.400 righe.
> 
> 

Il caricamento di dati in una tabella non vuota con un indice cluster può spesso contenere una combinazione di righe con registrazione completa e con registrazione minima. Un indice cluster è un albero B (bilanciato) di pagine. Se la pagina in cui si scrive contiene già righe provenienti da un'altra transazione, la scrittura verrà eseguita con registrazione completa. Se invece la pagina è vuota, la scrittura verrà eseguita con registrazione minima.

## <a name="optimizing-deletes"></a>Ottimizzazione delle eliminazioni
`DELETE` è un'operazione con registrazione completa.  Per eliminare una grande quantità di dati da una tabella o una partizione spesso è più pratico usare `SELECT` per indicare i dati da conservare, operazione che può essere eseguita registrazione minima.  A tale scopo, creare una nuova tabella con [CTAS][CTAS].  Dopo averla creata, usare [RENAME][RENAME] per sostituire la tabella precedente con quella nuova.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Ottimizzazione degli aggiornamenti
`UPDATE` è un'operazione con registrazione completa.  Se è necessario aggiornare un numero elevato di righe in una tabella o in una partizione, spesso può risultare molto più efficiente usare un'operazione con registrazione minima, ad esempio [CTAS][CTAS].

Nell'esempio seguente l'aggiornamento completo di una tabella è stato convertito in `CTAS` per consentire la registrazione minima.

In questo caso viene aggiunto a posteriori un importo di sconto alle vendite nella tabella:

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Per creare nuovamente tabelle di grandi dimensioni è possibile sfruttare le funzionalità di gestione del carico di lavoro di SQL Data Warehouse. Per altre informazioni, vedere la sezione relativa alla gestione del carico di lavoro nell'articolo sulla [concorrenza][concurrency].
> 
> 

## <a name="optimizing-with-partition-switching"></a>Ottimizzazione con cambio della partizione
In caso di modifiche su larga scala all'interno di una [partizione di tabella][partizione di tabella] può risultare molto utile adottare un modello di cambio di partizione. Se si tratta di una modifica dei dati di notevole entità che si estende su più partizioni, una semplice operazione di iterazione nelle partizioni permette di ottenere lo stesso risultato.

I passaggi per eseguire un cambio di partizione sono indicati di seguito:

1. Creare una partizione di disattivazione vuota.
2. Eseguire l'operazione di aggiornamento come CTAS.
3. Disattivare i dati esistenti nella tabella di disattivazione.
4. Attivare i nuovi dati.
5. Eseguire la pulizia dei dati.

Tuttavia, per identificare le partizioni per il cambio è necessario compilare prima di tutto una routine di supporto, come quella riportata di seguito. 

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id] 
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Questa routine ottimizza il riutilizzo del codice e permette di mantenere più compatto l'esempio di cambio di partizione.

Il codice seguente illustra i cinque passaggi citati sopra per ottenere una routine di cambio di partizione completa.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Riduzione della registrazione con batch di piccole dimensioni
Per operazioni di modifica dei dati di grandi dimensioni, può risultare utile suddividere l'operazione in blocchi o in batch per definire l'ambito dell'unità di lavoro.

Di seguito viene fornito un esempio funzionante. Le dimensioni del batch sono state impostate su un numero simbolico per evidenziare la tecnica. Nella realtà le dimensioni del batch sarebbero notevolmente più grandi. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Linee guida per la sospensione e il ridimensionamento
Azure SQL Data Warehouse permette di sospendere, riprendere e ridimensionare il data warehouse su richiesta. Quando si sospende o si ridimensiona SQL Data Warehouse è importante sapere che le eventuali transazioni in corso vengono interrotte immediatamente, con il conseguente rollback delle transazioni aperte. Se il carico di lavoro ha avviato una modifica dei dati a esecuzione prolungata e incompleta prima dell'operazione di sospensione o ridimensionamento, occorre annullare l'operazione. Questo può influire sul tempo necessario a sospendere o ridimensionare il database Azure SQL Data Warehouse. 

> [!IMPORTANT]
> `UPDATE` e `DELETE` sono operazioni con registrazione completa e qualsiasi operazione di annullamento o ripristino può richiedere molto più tempo rispetto alle operazioni con registrazione minima equivalenti. 
> 
> 

Lo scenario migliore sarebbe consentire il completamento delle transazioni di modifica dei dati in corso prima di sospendere o ridimensionare SQL Data Warehouse. Tuttavia, questo potrebbe non essere sempre possibile. Per ridurre il rischio di un rollback troppo lungo, prendere in considerazione una delle opzioni seguenti:

* Riscrivere le operazioni a esecuzione prolungata con [CTAS][CTAS]
* Suddividere l'operazione in blocchi e lavorare su un subset delle righe.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su limiti transazionali e livelli di isolamento, vedere [Transazioni in SQL Data Warehouse][Transazioni in SQL Data Warehouse].  Per una panoramica sulle procedure consigliate, vedere [Procedure consigliate per SQL Data Warehouse][Procedure consigliate per SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Transazioni in SQL Data Warehouse]: ./sql-data-warehouse-develop-transactions.md
[partizione di tabella]: ./sql-data-warehouse-tables-partition.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[Procedure consigliate per SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[modificare l'indice]:https://msdn.microsoft.com/library/ms188388.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx

<!-- Other web references -->




<!--HONumber=Nov16_HO3-->


