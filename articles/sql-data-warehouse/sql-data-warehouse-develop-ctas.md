---
title: CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse | Microsoft Docs
description: Suggerimenti per la scrittura di codice con l’istruzione CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: igorstan
ms.openlocfilehash: f791f460efec1b84533379e74add003619dbac6f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521568"
---
# <a name="using-create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Uso di CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse
Suggerimenti per la scrittura di codice con l’istruzione T-SQL CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse per lo sviluppo di soluzioni.

## <a name="what-is-create-table-as-select-ctas"></a>Cos'è CREATE TABLE AS SELECT (CTAS)?

L'istruzione [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) o CTAS è una delle funzionalità più importanti disponibili in T-SQL. È un'operazione parallela che crea una nuova tabella basata sull'output di un'istruzione SELECT. CTAS è il modo più semplice e veloce per creare e inserire i dati in una tabella con un unico comando. 

## <a name="selectinto-vs-ctas"></a>SELECT..INTO e CTAS
È possibile considerare un'istruzione CTAS come una versione avanzata dell'istruzione [SELECT...INTO](/sql/t-sql/queries/select-into-clause-transact-sql).

Di seguito è riportato un esempio di una semplice istruzione SELECT..INTO:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Tuttavia SELECT..INTO non consente di cambiare il metodo di distribuzione o il tipo di indice come parte dell'operazione. `[dbo].[FactInternetSales_new]` viene creato utilizzando il tipo di distribuzione predefinito come ROUND_ROBIN e la struttura della tabella predefinite come indice COLUMNSTORE cluster.

Uso di CTAS, si è in grado di specificare sia la distribuzione dei dati della tabella, nonché il tipo di struttura di tabella.
Per convertire l'esempio precedente in un'istruzione CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

 

> [!NOTE]
> Se si intende solo modificare l'indice nell'operazione `CTAS` e la tabella di origine è con distribuzione hash, è possibile che l'operazione `CTAS` possa essere eseguita in modo ottimale se si mantiene la stessa distribuzione di colonne e tipo di dati. In questo modo si eviteranno spostamenti incrociati di distribuzione di dati durante le operazioni con migliori risultati.
> 
> 

## <a name="using-ctas-to-copy-a-table"></a>Uso di CTAS per copiare una tabella
Probabilmente uno degli usi più comuni di `CTAS` consiste nel creare una copia di una tabella in modo da poter modificare il DDL. Se, ad esempio, originariamente creata la tabella come `ROUND_ROBIN` e vuole trasformarla in una tabella distribuita per una colonna, `CTAS` è la modalità di modificare la colonna di distribuzione. `CTAS` anche per modificare il partizionamento, l'indicizzazione o i tipi di colonna.

Si supponga che questa tabella sia stata creata usando il tipo di distribuzione predefinito di `ROUND_ROBIN` distribuito, in quanto non è stata specificata alcuna colonna di distribuzione in `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Ora si desidera creare una nuova copia di questa tabella con un indice cluster columnstore, in modo da poter sfruttare le prestazioni delle tabelle cluster columnstore. Si desidera anche distribuire la tabella in ProductKey, poiché si prevedono join per la colonna e si desidera evitare lo spostamento dei dati durante i join in ProductKey. Infine, si desidera aggiungere il partizionamento in OrderDateKey in modo da poter eliminare rapidamente i vecchi dati eliminando le vecchie partizioni. Ecco l'istruzione CTAS, che consente di copiare la vecchia tabella in una nuova tabella.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

È infine possibile rinominare le tabelle in modo da scambiare la nuova tabella ed eliminare quella precedente.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="using-ctas-to-work-around-unsupported-features"></a>Uso di CTAS per ovviare a funzionalità non supportate
CTAS può anche essere usata per ovviare a problemi derivanti da alcune funzionalità non supportate elencate di seguito. Questo metodo può spesso rivelarsi una situazione favorevole come non solo il codice sarà conforme ma esecuzione sarà più veloce in SQL Data Warehouse. Queste prestazioni sono il risultato della progettazione completamente parallelizzata. Gli scenari che è possibile trattare con CTAS includono:

* ANSI JOINS su UPDATE
* ANSI JOINs su DELETE
* Istruzione MERGE

> [!NOTE]
> Provare a considerare "prima CTAS". Se si ritiene che sia possibile risolvere un problema con `CTAS` , questo approccio in genere si rivela la scelta migliore, anche se è necessario scrivere più dati.
> 
> 

## <a name="ansi-join-replacement-for-update-statements"></a>Sostituzione di join ANSI per le istruzioni update
È possibile che si dispone di un aggiornamento complesso che unisce in join più di due tabelle utilizzando la sintassi di join ANSI per eseguire un’istruzione UPDATE o DELETE.

Si immagini di dover aggiornare questa tabella:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

La query originale era simile al seguente:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Poiché SQL Data Warehouse non supporta i join ANSI nella clausola `FROM` di un'istruzione `UPDATE`, non è possibile copiare questo codice senza modificarlo leggermente.

È possibile combinare `CTAS` con un join implicito per sostituire questo codice:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Sostituzione di join ANSI per le istruzioni delete
Talvolta l'uso di `CTAS`si rivela l'approccio migliore per l'eliminazione dei dati. Anziché eliminare i dati, selezionare i dati che si desidera mantenere. Ciò è particolarmente vero per `DELETE` istruzioni che utilizzano ANSI join sintassi poiché SQL Data Warehouse non supporta i join ANSI nella `FROM` clausola di un `DELETE` istruzione.

Un esempio di istruzione DELETE convertita è disponibile di seguito:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Sostituzione delle istruzioni merge
Le istruzioni merge possono essere sostituite, almeno in parte, usando CTAS. È possibile consolidare INSERT e UPDATE in un'unica istruzione. Sarebbe opportuno limitare eventuali record eliminati il `SELECT` omettere dai risultati dell'istruzione.

L'esempio seguente è per un'operazione UPSERT:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Raccomandazione CTAS: dichiarare in modo esplicito il tipo di dati e il supporto dei valori Null di output
Durante la migrazione del codice, si potrebbero incontrare questo tipo di modello di codifica:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Istintivamente si potrebbe pensare che è necessario eseguire la migrazione di questo codice a CTAS e sarebbe un’operazione corretta. Tuttavia, esiste un problema nascosto.

Il codice seguente NON produce lo stesso risultato:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Si noti che la colonna "result" porta avanti i valori del tipo di dati e del supporto di valori null dell'espressione. Ciò può causare lievi variazioni nei valori, se non si presta attenzione.

Provare a eseguire il seguente esempio:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Il valore archiviato per il risultato è diverso. Poiché il valore persistente nella colonna dei risultati viene usato in altre espressioni, l'errore diventa ancora più significativo.

![Risultati di CTAS](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Ciò è particolarmente importante per le migrazioni di dati. Anche se la seconda query è senza dubbio più accurata, esiste un problema. I dati sono diversi rispetto al sistema di origine e ciò comporta problemi di integrità della migrazione. Questo è uno dei rari casi in cui la risposta "errata" è effettivamente quella corretta.

Il motivo per cui esiste questa differenza tra i due risultati è costituito dal cast implicito del tipo. Nel primo esempio, la tabella definisce la definizione di colonna. Quando viene inserita la riga, si verifica una conversione implicita del tipo. Nel secondo esempio, non vi è alcuna conversione implicita del tipo poiché l'espressione definisce il tipo di dati della colonna. Si noti inoltre che la colonna nel secondo esempio è stata definita come una colonna che ammette valori null, mentre nel primo esempio non lo è. Quando la tabella è stata creata nel primo esempio, supporto valori null nelle colonne è stato definito in modo esplicito. Nel secondo esempio, è stato lasciato all'espressione e per impostazione predefinita, genera una definizione NULL.  

Per risolvere questi problemi, è necessario impostare esplicitamente la conversione del tipo e l'ammissione di valori null nella parte SELECT dell'istruzione CTAS. Non è possibile impostare queste proprietà nella parte relativa alla creazione della tabella.

Il seguente esempio illustra come correggere il codice:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Tenere presente quanto segue:

* Era possibile usare CAST o CONVERT
* ISNULL viene usato per forzare NULLability e non COALESCE
* ISNULL è la funzione più esterna
* La seconda parte di ISNULL è una costante, ad esempio 0

> [!NOTE]
> Per impostare correttamente il supporto di valori Null è fondamentale usare ISNULL e non COALESCE. COALESCE non è una funzione deterministica e pertanto il risultato dell'espressione sarà sempre NULLable. ISNULL è diversa. È deterministico. Perciò, quando la seconda parte della funzione ISNULL è una costante o un valore letterale, il valore risultante sarà NOT NULL.
> 
> 

Questo suggerimento non è utile solo per garantire l'integrità dei calcoli. È importante anche per il cambio di partizione della tabella. Si supponga di disporre di questa tabella definita come dato di fatto:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Tuttavia, il campo del valore è un'espressione calcolata che non fa parte dei dati di origine.

Per creare il set di dati partizionati, è possibile eseguire le operazioni seguenti:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

La query verrebbe eseguita correttamente. Il problema sorge quando si tenta di eseguire il cambio di partizione. Le definizioni di tabella non corrispondono. Per rendere le definizioni delle tabelle corrispondenti, deve essere modificato per aggiungere l'istruzione CTAS un `ISNULL` funzione per mantenere l'attributo di supporto di valori null della colonna.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Pertanto è possibile vedere che la coerenza del tipo e le proprietà di gestione del supporto di valori null in CTAS è un’ottima procedura tecnica consigliata. Consente di mantenere l'integrità dei calcoli e assicura inoltre che il cambio di partizione sia possibile.

Vedere le [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) documentazione. È una delle istruzioni più importanti di SQL Data Warehouse di Azure. Assicurarsi di averla compresa completamente.

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).

