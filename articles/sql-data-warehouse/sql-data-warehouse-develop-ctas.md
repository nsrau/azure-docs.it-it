<properties
   pageTitle="Create Table As Select (CTAS) in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per la codifica con l’istruzione create table as select (CTAS) in SQL Data Warehouse di Azure per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Create Table As Select (CTAS) in SQL Data Warehouse
Create Table As Select o `CTAS` è una delle funzionalità più importanti disponibili in T-SQL. È un'operazione completamente parallelizzata che crea una nuova tabella basata sull'output di un'istruzione SELECT. `CTAS` è il modo più semplice e veloce di creare la copia di una tabella. Volendo, è possibile considerarla una versione potenziata di `SELECT..INTO`. Questo documento offre esempi e procedure consigliate per `CTAS`.

## Uso di CTAS per copiare una tabella

Probabilmente uno degli usi più comuni di `CTAS` consiste nel creare una copia di una tabella in modo da poter modificare il DDL. Se ad esempio la tabella è stata creata in origine come `ROUND_ROBIN` e ora si vuole convertirla in una tabella distribuita su una colonna, per modificare la colonna di distribuzione si usa `CTAS`. È possibile usare `CTAS` anche per modificare il partizionamento, l'indicizzazione o i tipi di colonna.

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

Ora si desidera creare una nuova copia di questa tabella con un indice cluster columnstore, in modo da poter sfruttare le prestazioni delle tabelle cluster columnstore. Si desidera anche distribuire la tabella in ProductKey, poiché si prevedono join per la colonna e si desidera evitare lo spostamento dei dati durante i join in ProductKey. Infine, si desidera aggiungere il partizionamento in OrderDateKey in modo da poter eliminare rapidamente i vecchi dati eliminando le vecchie partizioni. Di seguito è riportata l'istruzione CTAS per copiare la vecchia tabella in una nuova tabella.

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

> [AZURE.NOTE] SQL Data Warehouse di Azure non supporta ancora le statistiche di creazione automatica o aggiornamento automatico. Per ottenere le migliori prestazioni dalle query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o dopo eventuali modifiche sostanziali dei dati. Per una spiegazione dettagliata delle statistiche, vedere l'argomento [Statistiche][] nel gruppo di argomenti sullo sviluppo.

## Uso di CTAS per ovviare a funzionalità non supportate

È anche possibile usare `CTAS` per ovviare a problemi derivanti da alcune funzionalità non supportate elencate di seguito. Questo può spesso rivelarsi una situazione favorevole in quando non solo il codice sarà conforme, ma l’esecuzione sarà più veloce in SQL Data Warehouse. Si tratta del risultato della progettazione completamente parallelizzata. Gli scenari che è possibile trattare con CTAS includono:

- SELECT..INTO
- ANSI JOINS su UPDATE
- ANSI JOINs su DELETE
- Istruzione MERGE

> [AZURE.NOTE] Provare a considerare "prima CTAS". Se si ritiene che sia possibile risolvere un problema con `CTAS`, questo approccio in genere si rivela la scelta migliore, anche se è necessario scrivere più dati.
>

## SELECT..INTO
È possibile che `SELECT..INTO` compaia diverse volte nella soluzione che si è scelto di applicare.

Di seguito è riportato un esempio di un'istruzione `SELECT..INTO`:

```sql
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

Convertire l'istruzione precedente in `CTAS` è semplice:

```sql
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [AZURE.NOTE] CTAS attualmente richiede che venga specificata una colonna di distribuzione. Se non si sta intenzionalmente cercando di modificare la colonna di distribuzione, le prestazioni di `CTAS` risulteranno ottimizzate se si seleziona una colonna di distribuzione corrispondente a quella della tabella sottostante, in quanto si evita lo spostamento dei dati. Se si sta creando una tabella di piccole dimensioni per la quale le prestazioni non sono importanti, è possibile specificare `ROUND_ROBIN` per evitare di dover selezionare una colonna di distribuzione.

## Sostituzione di join ANSI per le istruzioni update

È possibile che si dispone di un aggiornamento complesso che unisce in join più di due tabelle utilizzando la sintassi di join ANSI per eseguire un’istruzione UPDATE o DELETE.

Si immagini di dover aggiornare questa tabella:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(	[EnglishProductCategoryName]	NVARCHAR(50)	NOT NULL
,	[CalendarYear]					SMALLINT		NOT NULL
,	[TotalSalesAmount]				MONEY			NOT NULL
)
WITH
(
	DISTRIBUTION = ROUND_ROBIN
)
;
```

La query originale potrebbe essere simile alla seguente:

```sql
UPDATE	acs
SET		[TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM	[dbo].[AnnualCategorySales] 	AS acs
JOIN	(
		SELECT	[EnglishProductCategoryName]
		,		[CalendarYear]
		,		SUM([SalesAmount])				AS [TotalSalesAmount]
		FROM	[dbo].[FactInternetSales]		AS s
		JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
		JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
		JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
		JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
		WHERE 	[CalendarYear] = 2004
		GROUP BY
				[EnglishProductCategoryName]
		,		[CalendarYear]
		) AS fis
ON	[acs].[EnglishProductCategoryName]	= [fis].[EnglishProductCategoryName]
AND	[acs].[CalendarYear]				= [fis].[CalendarYear]
;
```

Poiché SQL Data Warehouse non supporta i join ANSI nella clausola `FROM` di un'istruzione `UPDATE`, non è possibile copiare questo codice senza modificarlo leggermente.

È possibile combinare `CTAS` con un join implicito per sostituire questo codice:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT	ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)	AS [EnglishProductCategoryName]
,		ISNULL(CAST([CalendarYear] AS SMALLINT),0) 						AS [CalendarYear]
,		ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)						AS [TotalSalesAmount]
FROM	[dbo].[FactInternetSales]		AS s
JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
WHERE 	[CalendarYear] = 2004
GROUP BY
		[EnglishProductCategoryName]
,		[CalendarYear]
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

## Sostituzione di join ANSI per le istruzioni delete
Talvolta l'uso di `CTAS` si rivela l'approccio migliore per l'eliminazione dei dati. Invece di eliminare i dati, si selezionano semplicemente i dati da mantenere. Ciò vale soprattutto per le istruzioni `DELETE` che usano la sintassi di join ANSI, in quanto SQL Data Warehouse non supporta i join ANSI nella clausola `FROM` di un'istruzione `DELETE`.

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

## Sostituzione delle istruzioni merge
Le istruzioni merge possono essere sostituite, almeno in parte, usando `CTAS`. È possibile consolidare `INSERT` e `UPDATE` in un'unica istruzione. Tutti i record eliminati dovevano essere chiusi in una seconda istruzione.

Un esempio di `UPSERT` è riportato di seguito:

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
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## Raccomandazione CTAS: dichiarare in modo esplicito il tipo di dati e il supporto di valori null di output

Durante la migrazione del codice, è possibile imbattersi in questo tipo di modello di codifica:

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

Il valore archiviato per il risultato è diverso. Poiché il valore persistente della colonna result viene utilizzato in altre espressioni, l'errore diventa ancora più significativo.

![][1]

Ciò è particolarmente importante per le migrazioni di dati. Anche se la seconda query è senza dubbio più accurata, esiste un problema. I dati sono diversi rispetto al sistema di origine e ciò comporta problemi di integrità della migrazione. Questo è uno dei rari casi in cui la risposta "errata" è effettivamente quella corretta.

Il motivo per cui esiste questa differenza tra i due risultati è costituito dal cast implicito del tipo. Nel primo esempio la tabella specifica la definizione di colonna. Quando la riga viene inserita si verifica una conversione implicita del tipo. Nel secondo esempio non esiste alcuna conversione implicita del tipo poiché l'espressione definisce il tipo di dati della colonna. Si noti inoltre che la colonna nel secondo esempio è stata definita come una colonna che ammette valori null, mentre nel primo esempio non lo è. Quando la tabella è stata creata nel primo esempio, il supporto di valori null della colonna è stato definito in modo esplicito. Nel secondo esempio, è stato affidato all'espressione che per impostazione predefinita comporterebbe una definizione NULL.

Per risolvere questi problemi è necessario impostare esplicitamente la conversione del tipo e il supporto di valori Null nella parte `SELECT` dell'istruzione `CTAS`. Non è possibile impostare queste proprietà nella parte relativa alla creazione della tabella.

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
- Era possibile usare CAST o CONVERT
- ISNULL viene usato per forzare NULLability e non COALESCE
- ISNULL è la funzione più esterna
- La seconda parte di ISNULL è una costante, ad esempio 0

> [AZURE.NOTE] Per impostare correttamente il supporto dei valori Null è fondamentale usare `ISNULL` e non `COALESCE`. `COALESCE` non è una funzione deterministica e pertanto il risultato dell'espressione sarà sempre NULLable. `ISNULL` è diverso. È deterministico. Pertanto quando la seconda parte della funzione `ISNULL` è una costante o un valore letterale, il valore risultante sarà NOT NULL.

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

Per creare il set di dati partizionati è possibile eseguire questa operazione:

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

La query verrebbe eseguita correttamente. Il problema sorge quando si tenta di eseguire il cambio di partizione. Le definizioni di tabella non corrispondono. Per far corrispondere le definizioni di tabella è necessario modificare CTAS.

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

Fare riferimento a MSDN per ulteriori informazioni sull'utilizzo di [CTAS][]. È una delle istruzioni più importanti di SQL Data Warehouse di Azure. Assicurarsi di averla compresa completamente.

## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md
[Statistiche]: ./sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[CTAS]: https://msdn.microsoft.com/library/mt204041.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0420_2016-->