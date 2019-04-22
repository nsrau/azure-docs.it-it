---
title: CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse | Microsoft Docs
description: SPIEGAZIONE ed esempi dell'istruzione CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: jrasnick
ms.custom: seoapril2019
ms.openlocfilehash: c8e9f3ccdfaee64f75443f6a4eb89a3df7c48b0e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680095"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse

Questo articolo illustra l'istruzione T-SQL CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse per lo sviluppo di soluzioni. L'articolo offre anche esempi di codice.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

Il [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) istruzione (CTAS) è una delle più importanti funzionalità T-SQL disponibili. CTAS è un'operazione parallela che crea una nuova tabella basata sull'output di un'istruzione SELECT. CTAS è il modo più semplice e veloce per creare e inserire i dati in una tabella con un unico comando.

## <a name="selectinto-vs-ctas"></a>SELEZIONA... IN Visual Studio. CTAS

CTAS è una versione di un livello di personalizzazione di [Seleziona... IN](/sql/t-sql/queries/select-into-clause-transact-sql) istruzione.

Di seguito è riportato un esempio di un'istruzione SELECT semplice... IN:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

SELEZIONA... IN non consente di modificare il metodo di distribuzione o il tipo di indice come parte dell'operazione. Si crea `[dbo].[FactInternetSales_new]` utilizzando il tipo di distribuzione predefinito di ROUND_ROBIN e la struttura della tabella predefinita dell'indice COLUMNSTORE cluster.

Con CTAS, d'altra parte, è possibile specificare sia la distribuzione dei dati della tabella, nonché il tipo di struttura di tabella. Per convertire l'esempio precedente in un'istruzione CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
   ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [!NOTE]
> Se si sta solo tentando di modificare l'indice nell'operazione CTAS e la tabella di origine è con distribuita hash, mantenere lo stesso tipo di dati e colonna di distribuzione. Ciò consente di evitare lo spostamento dei dati tra distribuzione durante l'operazione, che risulta più efficiente.

## <a name="use-ctas-to-copy-a-table"></a>Usare CTAS per copiare una tabella

Forse uno degli usi più comuni di CTAS è creare una copia di una tabella per modificare l'istruzione DDL. È possibile ad esempio originariamente creata la tabella come `ROUND_ROBIN`e vuole trasformarla in una tabella distribuita per una colonna. CTAS è come modificare la colonna di distribuzione. È anche possibile usare CTAS per modificare i tipi di partizionamento, indicizzazione o colonna.

È possibile ad esempio è stata creata questa tabella usando il tipo di distribuzione predefinito della `ROUND_ROBIN`, non specificare una colonna di distribuzione di `CREATE TABLE`.

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

A questo punto si desidera creare una nuova copia di questa tabella, con un `Clustered Columnstore Index`, pertanto è possibile sfruttare le prestazioni delle tabelle Columnstore cluster. Si desidera distribuire la tabella sul `ProductKey`, perché si sta usare join per questa colonna e si vuole evitare lo spostamento dei dati durante i join su `ProductKey`. Infine, si vuole aggiungere il partizionamento in `OrderDateKey`, in modo da poter eliminare rapidamente i dati precedenti eliminando le vecchie partizioni. Ecco l'istruzione CTAS che consente di copiare la vecchia tabella in una nuova tabella.

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

Infine, è possibile rinominare le tabelle, per scambiare alla nuova tabella e quindi eliminare la tabella precedente.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Usare CTAS per ovviare a funzionalità non supportate

È anche possibile usare CTAS per ovviare numerose funzionalità non supportate elencate di seguito. Questo metodo può spesso rivelarsi utile, perché non solo il codice sarà conforme, ma sarà spesso eseguita più velocemente in SQL Data Warehouse. Queste prestazioni sono il risultato della progettazione completamente parallelizzata. Gli scenari includono:

* ANSI JOINS su UPDATE
* ANSI JOINs su DELETE
* Istruzione MERGE

> [!TIP]
> Provare a pensare "CTAS prima." Per risolvere un problema usando CTAS è in genere un buon approccio, anche se si scrivono più dati di conseguenza.

## <a name="ansi-join-replacement-for-update-statements"></a>Sostituzione di join ANSI per le istruzioni update

Si noterà la presenza di un aggiornamento complesso. L'aggiornamento unisce più di due tabelle usando la sintassi join ANSI per eseguire l'aggiornamento o eliminazione.

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

La query originale era simile a questo esempio:

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

SQL Data Warehouse non supporta i join ANSI nella `FROM` clausola di un `UPDATE` istruzione, non è possibile utilizzare l'esempio precedente senza modificarlo.

È possibile utilizzare una combinazione di CTAS e un join implicito per sostituire l'esempio precedente:

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

In alcuni casi l'approccio migliore per l'eliminazione dei dati è usare CTAS, soprattutto per `DELETE` istruzioni che utilizzano ANSI join di sintassi. Infatti, SQL Data Warehouse non supporta i join ANSI nella `FROM` clausola di un `DELETE` istruzione. Anziché eliminare i dati, selezionare i dati che si desidera mantenere.

Di seguito è riportato un esempio di un oggetto convertito `DELETE` istruzione:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
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

È possibile sostituire le istruzioni merge, almeno in parte, usando CTAS. È possibile combinare le `INSERT` e il `UPDATE` in un'unica istruzione. Sarebbe opportuno limitare eventuali record eliminati il `SELECT` omettere dai risultati dell'istruzione.

L'esempio seguente è per un `UPSERT`:

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

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>dichiarare in modo esplicito il tipo di dati e il supporto dei valori Null di output

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

Si potrebbe pensare che è necessario eseguire la migrazione di questo codice in un'istruzione CTAS e sarebbe corretta. Tuttavia, è un problema di nascosto.

Il codice seguente non produce lo stesso risultato:

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

Si noti che la colonna "result" porta avanti i valori del tipo di dati e del supporto di valori null dell'espressione. Che trasportano i dati di tipo forward può causare lievi variazioni nei valori se non si presta attenzione.

Provare a eseguire questo esempio:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Il valore archiviato per il risultato è diverso. Poiché il valore persistente nella colonna dei risultati viene usato in altre espressioni, l'errore diventa ancora più significativo.

![Schermata di CTAS risultati](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Questo è importante per le migrazioni dei dati. Anche se la seconda query probabilmente è più precisa, si è verificato un problema. I dati risulterebbero diversi rispetto al sistema di origine e ciò comporta problemi di integrità della migrazione. Questo è uno dei rari casi in cui la risposta "errata" è effettivamente quella corretta.

Noteremo una disparità tra i due risultati, infatti a causa di un cast di tipo implicito. Nel primo esempio, la tabella definisce la definizione di colonna. Quando viene inserita la riga, si verifica una conversione implicita del tipo. Nel secondo esempio, non vi è alcuna conversione implicita del tipo poiché l'espressione definisce il tipo di dati della colonna.

Si noti anche che la colonna nel secondo esempio è stata definita come una colonna che ammette valori null, mentre nel primo esempio di questa operazione non è. Quando la tabella è stata creata nel primo esempio, supporto valori null nelle colonne è stato definito in modo esplicito. Nel secondo esempio, è stato lasciato all'espressione e per impostazione predefinita restituirà una definizione NULL.

Per risolvere questi problemi, è necessario impostare esplicitamente la conversione del tipo e l'ammissione di valori null nella parte SELECT dell'istruzione CTAS. È possibile impostare queste proprietà in 'CREATE TABLE'.
Nell'esempio seguente viene illustrato come correggere il codice:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Tenere presente quanto segue:

* È possibile utilizzare CAST o CONVERTIRE.
* Utilizzare ISNULL, COALESCE non, per forzare l'ammissione di valori null. Vedere la nota seguente.
* ISNULL è la funzione più esterna.
* La seconda parte di ISNULL è una costante, 0.

> [!NOTE]
> Per i valori null sia impostato correttamente, è fondamentale usare ISNULL e COALESCE non. COALESCE non è una funzione deterministica e pertanto il risultato dell'espressione sarà sempre NULLable. ISNULL è diversa. È deterministica. Pertanto, quando la seconda parte della funzione ISNULL è una costante o un valore letterale, il valore risulta sarà non NULL.

È anche importante per il cambio partizione della tabella l'integrità dei calcoli. Si supponga di che avere questa tabella sono definiti come una tabella dei fatti:

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

Tuttavia, il campo quantità è un'espressione calcolata. Non è parte dell'origine dati.

Per creare il set di dati partizionati, si potrebbe voler usare il codice seguente:

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

La query verrebbe eseguita correttamente. Il problema si verifica quando si tenta di effettuare il cambio di partizione. Le definizioni di tabella non corrispondono. Per rendere le definizioni delle tabelle corrispondenti, modificare l'istruzione CTAS per aggiungere un `ISNULL` funzione per mantenere l'attributo di supporto di valori null della colonna.

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

Si noterà che la coerenza dei tipi e la gestione delle proprietà di supporto di valori null in CTAS è una progettazione ottimale. Consente di mantenere l'integrità dei calcoli e assicura anche che sia possibile cambio della partizione.

CTAS è una delle istruzioni più importanti in SQL Data Warehouse. Assicurarsi di averla compresa completamente. Vedere le [documentazione di CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti relativi allo sviluppo, vedere la [panoramica sullo sviluppo](sql-data-warehouse-overview-develop.md).

