---
title: CREATE TABLE AS SELECT (CTAS)
description: Spiegazione ed esempi dell'istruzione CREATE TABLE AS SELECT (CTAS) in Synapse SQL per lo sviluppo di soluzioni.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: e5dc8835a6d5f235cf269edd4e9f069c904e1b7e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582155"
---
# <a name="create-table-as-select-ctas"></a>CREATE TABLE AS SELECT (CTAS)

In questo articolo viene illustrata l'istruzione T-SQL CREATE TABLE AS SELECT (CTAS) in Synapse SQL per lo sviluppo di soluzioni. L'articolo include anche esempi di codice.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

L'istruzione [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS) è una delle funzionalità T-SQL più importanti disponibili. CTAS è un'operazione parallela che crea una nuova tabella in base all'output di un'istruzione SELECT. CTAS è il modo più semplice e veloce per creare e inserire dati in una tabella con un singolo comando.

## <a name="selectinto-vs-ctas"></a>Selezionare... INTO e CTAS

CTAS è una versione più personalizzabile [dell'istruzione SELECT... Istruzione INTO.](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Di seguito è riportato un esempio di un semplice SELECT... In:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Selezionare... INTO non consente di modificare il metodo di distribuzione o il tipo di indice come parte dell'operazione. `[dbo].[FactInternetSales_new]` Creare utilizzando il tipo di distribuzione predefinito di ROUND_ROBIN e la struttura di tabella predefinita di CLUSTERED COLUMNSTORE INDEX.

Con CTAS, d'altra parte, è possibile specificare sia la distribuzione dei dati della tabella che il tipo di struttura della tabella. Per convertire l'esempio precedente in CTAS:

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
> Se si sta solo tentando di modificare l'indice nell'operazione CTAS e la tabella di origine è sottoposta a hash, mantenere la stessa colonna di distribuzione e lo stesso tipo di dati. In questo modo si evita lo spostamento dei dati tra distribuzione durante l'operazione, che è più efficiente.

## <a name="use-ctas-to-copy-a-table"></a>Usare CTAS per copiare una tabella

Forse uno degli usi più comuni di CTAS è la creazione di una copia di una tabella per modificare il DDL. Si supponga che la tabella `ROUND_ROBIN`sia stata creata in origine come e che ora si desideri modificarla in una tabella distribuita in una colonna. CTAS è come si modifica la colonna di distribuzione. È anche possibile usare CTAS per modificare i tipi di partizionamento, indicizzazione o colonna.

Si supponga di aver creato questa tabella utilizzando `ROUND_ROBIN`il tipo di distribuzione `CREATE TABLE`predefinito di , senza specificare una colonna di distribuzione nel file .

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

A questo punto si desidera creare una `Clustered Columnstore Index`nuova copia di questa tabella, con un oggetto , in modo da poter sfruttare le prestazioni delle tabelle Columnstore in cluster. Si desidera inoltre distribuire `ProductKey`questa tabella in , perché si prevede i join in questa colonna `ProductKey`e si desidera evitare lo spostamento dei dati durante i join in . Infine, si desidera anche aggiungere `OrderDateKey`il partizionamento su , in modo da poter eliminare rapidamente i vecchi dati eliminando le vecchie partizioni. Ecco l'istruzione CTAS, che copia la vecchia tabella in una nuova tabella.

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

Infine, è possibile rinominare le tabelle, per scambiare nella nuova tabella e quindi eliminare la tabella precedente.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Usare CTAS per risolvere le funzionalità non supportate

È inoltre possibile utilizzare CTAS per aggirare alcune delle funzionalità non supportate elencate di seguito. Questo metodo può spesso rivelarsi utile, perché non solo il codice sarà conforme, ma spesso verrà eseguito più velocemente su Synapse SQL. Questa performance è il risultato del suo design completamente parallelizzato. Gli scenari includono:

* ANSI JOINS su UPDATE
* ANSI JOINs su DELETE
* Istruzione MERGE

> [!TIP]
> Prova a pensare "CTAS prima". La risoluzione di un problema tramite CTAS è in genere un buon approccio, anche se si stanno scrivendo più dati di conseguenza.

## <a name="ansi-join-replacement-for-update-statements"></a>Sostituzione di join ANSI per istruzioni UPDATE

Potresti scoprire di avere un aggiornamento complesso. L'aggiornamento unisce più di due tabelle utilizzando la sintassi di join ANSI per eseguire l'istruzione UPDATE o DELETE.

Si supponga di dover aggiornare questa tabella:

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

La query originale potrebbe avere un aspetto simile a questo esempio:The original query might have looked something like this example:

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

Synapse SQL non supporta join ANSI `FROM` nella `UPDATE` clausola di un'istruzione, pertanto non è possibile utilizzare l'esempio precedente senza modificarlo.

È possibile utilizzare una combinazione di un CTAS e un join implicito per sostituire l'esempio precedente:You can use a combination of a CTAS and an implicit join to replace the previous example:

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

A volte l'approccio migliore per l'eliminazione dei dati consiste nell'utilizzare CTAS, in particolare per `DELETE` le istruzioni che utilizzano la sintassi di join ANSI. Ciò è dovuto al fatto che Synapse `FROM` SQL non `DELETE` supporta join ANSI nella clausola di un'istruzione. Anziché eliminare i dati, selezionare i dati che si desidera mantenere.

Di seguito è riportato `DELETE` un esempio di istruzione convertita:

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

È possibile sostituire le istruzioni di unione, almeno in parte, utilizzando CTAS. È possibile `INSERT` combinare `UPDATE` e in una singola istruzione. Tutti i record eliminati `SELECT` devono essere limitati dall'istruzione da omettere dai risultati.

L'esempio seguente `UPSERT`è per un :

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

Durante la migrazione del codice, è possibile che venga eseguito questo tipo di modello di codifica:When migrating code, you might find you run across this type of coding pattern:

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

Si potrebbe pensare che è necessario eseguire la migrazione di questo codice a CTAS e si sarebbe corretto. Tuttavia, c'è un problema nascosto qui.

Il codice seguente non restituisce lo stesso risultato:The following code doesn't yield the same result:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Si noti che la colonna del risultato riporta i valori relativi a tipo di dati e supporto dei valori Null dell'espressione. Portare avanti il tipo di dati può causare sottili variazioni nei valori se non si sta attenti.

Prova questo esempio:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

Il valore archiviato per il risultato è diverso. Quando il valore persistente nella colonna di risultato viene utilizzato in altre espressioni, l'errore diventa ancora più significativo.

![Screenshot dei risultati di CTAS](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

Questo è importante per le migrazioni dei dati. Anche se la seconda query è probabilmente più accurata, c'è un problema. I dati sarebbero diversi rispetto al sistema di origine e ciò porta a questioni di integrità nella migrazione. Questo è uno dei rari casi in cui la risposta "sbagliata" in effetti è quella giusta.

Il motivo per cui vediamo una disparità tra i due risultati è dovuto al cast implicito del tipo. Nel primo esempio, la tabella definisce la definizione di colonna. Quando la riga viene inserita, si verifica una conversione implicita del tipo. Nel secondo esempio, non esiste alcuna conversione implicita del tipo poiché l'espressione definisce il tipo di dati della colonna.

Si noti inoltre che la colonna nel secondo esempio è stata definita come colonna NULLable, mentre nel primo esempio non è presente. Quando la tabella è stata creata nel primo esempio, il supporto di valori Null della colonna è stato definito in modo esplicito. Nel secondo esempio, è stato lasciato all'espressione e per impostazione predefinita risulterebbe in una definizione NULL.

Per risolvere questi problemi, è necessario impostare in modo esplicito la conversione del tipo e il supporto di valori Null nella parte SELECT dell'istruzione CTAS. Non è possibile impostare queste proprietà in 'CREATE TABLE'.
Nell'esempio seguente viene illustrato come correggere il codice:The following example demonstrates how to fix the code:

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
* Utilizzare ISNULL, non COALESCE, per forzare la conversione in formato NULL. Vedere la nota seguente.
* ISNULL è la funzione più esterna.
* La seconda parte di ISNULL è una costante, 0.

> [!NOTE]
> Affinché il supporto dei valori Null venga impostato correttamente, è fondamentale utilizzare ISNULL e non COALESCE. COALESCE non è una funzione deterministica e pertanto il risultato dell'espressione sarà sempre nuLLable. ISNULL è diversa. È deterministico. Pertanto, quando la seconda parte della funzione ISNULL è una costante o un valore letterale, il valore risultante sarà NOT NULL.

Garantire l'integrità dei calcoli è importante anche per il cambio di partizione della tabella. Si supponga di avere questa tabella definita come tabella dei fatti:Immaginate di avere questa tabella definita come una tabella dei fatti:

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

Tuttavia, il campo dell'importo è un'espressione calcolata. Non fa parte dei dati di origine.

Per creare il dataset partizionato, è possibile usare il codice seguente:To create your partitioned dataset, you might want to use the following code:

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

La query verrebbe eseguita perfettamente. Il problema si verifica quando si tenta di eseguire l'opzione di partizione. Le definizioni delle tabelle non corrispondono. Per fare in modo che le definizioni `ISNULL` di tabella corrispondano, modificare il CTAS per aggiungere una funzione per mantenere l'attributo di supporto dei valori Null della colonna.

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

È possibile vedere che la coerenza del tipo e la gestione delle proprietà di supporto di valori Null in un CTAS è una procedura consigliata di progettazione. Consente di mantenere l'integrità nei calcoli e garantisce inoltre che sia possibile il cambio di partizione.

CTAS è una delle istruzioni più importanti in Synapse SQL. Assicurarsi di averla compresa completamente. Vedere la [documentazione di CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti relativi allo sviluppo, vedere la [panoramica sullo sviluppo](sql-data-warehouse-overview-develop.md).

