---
title: CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse | Microsoft Docs
description: Spiegazione ed esempi dell'istruzione CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: e347287a6d77cdc947a79ca497fdb2ffe83ad1bc
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882478"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>CREATE TABLE come SELECT (CTAS) in Azure SQL Data Warehouse

Questo articolo illustra l'istruzione T-SQL CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse per lo sviluppo di soluzioni. L'articolo contiene anche esempi di codice.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

L'istruzione [create table As Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAs) è una delle funzionalità più importanti disponibili in T-SQL. CTAS è un'operazione parallela che consente di creare una nuova tabella basata sull'output di un'istruzione SELECT. CTAS è il modo più semplice e rapido per creare e inserire dati in una tabella con un unico comando.

## <a name="selectinto-vs-ctas"></a>Seleziona... IN vs. CTAS

CTAS è una versione più personalizzabile di [Select... INTO](/sql/t-sql/queries/select-into-clause-transact-sql) (istruzione).

Di seguito è riportato un esempio di una semplice selezione... IN

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Seleziona... IN non consente di modificare il metodo di distribuzione o il tipo di indice come parte dell'operazione. È possibile creare `[dbo].[FactInternetSales_new]` usando il tipo di distribuzione predefinito di ROUND_ROBIN e la struttura di tabella predefinita dell'indice COLUMNStore CLUSTER.

Con CTAS, d'altra parte, è possibile specificare sia la distribuzione dei dati della tabella sia il tipo di struttura della tabella. Per convertire l'esempio precedente in CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Se si sta provando solo a modificare l'indice nell'operazione CTAS e la tabella di origine è con distribuzione hash, mantenere la stessa colonna di distribuzione e il tipo di dati. In questo modo si evita lo spostamento dei dati tra distribuzioni durante l'operazione, che è più efficiente.

## <a name="use-ctas-to-copy-a-table"></a>Usare CTAS per copiare una tabella

Probabilmente uno degli usi più comuni di CTAS è la creazione di una copia di una tabella per modificare il DDL. Supponiamo che la tabella sia stata creata in origine come `ROUND_ROBIN`e che ora si voglia modificarla in una tabella distribuita in una colonna. CTAS è la modalità di modifica della colonna di distribuzione. È anche possibile usare CTAS per modificare il partizionamento, l'indicizzazione o i tipi di colonna.

Si direbbe che la tabella sia stata creata usando il tipo di distribuzione predefinito di `ROUND_ROBIN`, senza specificare una colonna di distribuzione nel `CREATE TABLE`.

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
    CustomerPONumber nvarchar(25));
```

A questo punto si vuole creare una nuova copia di questa tabella con una `Clustered Columnstore Index`, in modo da poter sfruttare le prestazioni delle tabelle columnstore cluster. Si vuole anche distribuire questa tabella in `ProductKey`, perché si prevede di partecipare a questo articolo e si vuole evitare lo spostamento dei dati durante i join `ProductKey`. Infine, si vuole anche aggiungere il partizionamento in `OrderDateKey`, in modo da poter eliminare rapidamente i dati obsoleti rilasciando le partizioni obsolete. Di seguito è riportata l'istruzione CTAS, che consente di copiare la tabella precedente in una nuova tabella.

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

Infine, è possibile rinominare le tabelle per scambiarsi nella nuova tabella e quindi eliminare la vecchia tabella.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Usare CTAS per aggirare le funzionalità non supportate

È anche possibile usare CTAS per aggirare alcune delle funzionalità non supportate elencate di seguito. Questo metodo spesso può risultare utile, perché non solo il codice sarà conforme, ma verrà spesso eseguito più velocemente su SQL Data Warehouse. Questa prestazione è il risultato della progettazione completamente parallela. Gli scenari includono:

* ANSI JOINS su UPDATE
* ANSI JOINs su DELETE
* Istruzione MERGE

> [!TIP]
> Provare a pensare "CTAS First". La risoluzione di un problema usando CTAS è in genere un approccio efficace, anche se si stanno scrivendo più dati come risultato.

## <a name="ansi-join-replacement-for-update-statements"></a>Sostituzione di join ANSI per le istruzioni update

Potrebbe essere presente un aggiornamento complesso. L'aggiornamento viene unito a più di due tabelle utilizzando la sintassi di join ANSI per eseguire l'aggiornamento o l'eliminazione.

Si immagini di dover aggiornare questa tabella:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

La query originale potrebbe avere un aspetto simile all'esempio seguente:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
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
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

SQL Data Warehouse non supporta i join ANSI nella clausola `FROM` di un'istruzione `UPDATE`, pertanto non è possibile usare l'esempio precedente senza modificarlo.

È possibile usare una combinazione di CTAS e un join implicito per sostituire l'esempio precedente:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Sostituzione di join ANSI per le istruzioni delete

In alcuni casi l'approccio migliore per l'eliminazione dei dati consiste nell'usare CTAS, specialmente per `DELETE` istruzioni che usano la sintassi di join ANSI. Questo perché SQL Data Warehouse non supporta i join ANSI nella clausola `FROM` di un'istruzione `DELETE`. Anziché eliminare i dati, selezionare i dati che si desidera memorizzare.

Di seguito è riportato un esempio di un'istruzione `DELETE` convertita:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Sostituzione delle istruzioni merge

È possibile sostituire le istruzioni merge, almeno in parte, usando CTAS. È possibile combinare il `INSERT` e il `UPDATE` in un'unica istruzione. Tutti i record eliminati devono essere limitati dall'istruzione `SELECT` per omettere i risultati.

L'esempio seguente è relativo a un `UPSERT`:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>dichiarare in modo esplicito il tipo di dati e il supporto dei valori Null di output

Quando si esegue la migrazione del codice, è possibile che venga eseguito in questo tipo di modello di codifica:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Si potrebbe pensare di voler eseguire la migrazione di questo codice a CTAS e di essere corretti. Tuttavia, c'è un problema nascosto qui.

Il codice seguente non produce lo stesso risultato:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Si noti che la colonna "result" porta avanti i valori del tipo di dati e del supporto di valori null dell'espressione. Il trasporto del tipo di dati in avanti può causare variazioni minime nei valori se non si presta attenzione.

Provare l'esempio seguente:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

Il valore archiviato per il risultato è diverso. Poiché il valore permanente nella colonna risultato viene usato in altre espressioni, l'errore diventa ancora più significativo.

![Screenshot dei risultati di CTAS](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Questa operazione è importante per le migrazioni di dati. Anche se la seconda query è probabilmente più accurata, si verifica un problema. I dati sono diversi rispetto al sistema di origine e ciò comporta problemi di integrità nella migrazione. Questo è uno dei rari casi in cui la risposta "errata" è effettivamente quella corretta.

Il motivo per cui viene visualizzata una disparità tra i due risultati è dovuto al cast implicito dei tipi. Nel primo esempio la tabella definisce la definizione di colonna. Quando viene inserita la riga, viene eseguita una conversione implicita del tipo. Nel secondo esempio, non esiste alcuna conversione implicita di tipi perché l'espressione definisce il tipo di dati della colonna.

Si noti inoltre che la colonna nel secondo esempio è stata definita come colonna NULLable, mentre nel primo esempio non è presente. Quando la tabella è stata creata nel primo esempio, il supporto di valori null della colonna è stato definito in modo esplicito. Nel secondo esempio è stato lasciato all'espressione e, per impostazione predefinita, verrebbe generata una definizione NULL.

Per risolvere questi problemi, è necessario impostare in modo esplicito la conversione del tipo e il supporto di valori null nella parte SELECT dell'istruzione CTAS. Non è possibile impostare queste proprietà in ' CREATE TABLE '.
Nell'esempio seguente viene illustrato come correggere il codice:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Tenere presente quanto segue:

* È possibile utilizzare CAST o CONVERT.
* Utilizzare ISNULL, non COALESCE, per forzare il supporto dei valori NULL. Vedere la nota seguente.
* ISNULL è la funzione più esterna.
* La seconda parte di ISNULL è una costante, 0.

> [!NOTE]
> Per impostare correttamente il supporto di valori null, è fondamentale usare ISNULL e non COALESCE. COALESCE non è una funzione deterministica e pertanto il risultato dell'espressione sarà sempre NULLable. ISNULL è diversa. È deterministica. Pertanto, quando la seconda parte della funzione ISNULL è una costante o un valore letterale, il valore risultante sarà NOT NULL.

La verifica dell'integrità dei calcoli è importante anche per il cambio della partizione di tabella. Si supponga di avere questa tabella definita come tabella dei fatti:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

Tuttavia, il campo Amount è un'espressione calcolata. Non fa parte dei dati di origine.

Per creare il set di dati partizionato, è consigliabile usare il codice seguente:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
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
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

La query verrebbe eseguita correttamente. Il problema si verifica quando si tenta di eseguire il cambio di partizione. Le definizioni di tabella non corrispondono. Per fare in modo che le definizioni di tabella corrispondano, modificare CTAS per aggiungere una funzione `ISNULL` per mantenere l'attributo di supporto dei valori null della colonna.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]   
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

È possibile osservare che la coerenza dei tipi e la gestione delle proprietà di supporto dei valori null in un CTAS è una procedura consigliata di progettazione. Consente di mantenere l'integrità nei calcoli e garantisce inoltre il cambio di partizione possibile.

CTAS è una delle istruzioni più importanti in SQL Data Warehouse. Assicurarsi di averla compresa completamente. Vedere la [documentazione di CTAs](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti relativi allo sviluppo, vedere la [panoramica sullo sviluppo](sql-data-warehouse-overview-develop.md).

