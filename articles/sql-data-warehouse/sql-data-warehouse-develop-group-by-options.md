---
title: Uso delle opzioni group by in Azure SQL Data Warehouse | Microsoft Docs
description: Suggerimenti per l’implementazione delle opzioni group by in SQL Data Warehouse di Azure per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: b158048929d3db8672d76027666331448a91a0a8
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861796"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Opzioni Group by in SQL Data Warehouse
Suggerimenti per l’implementazione delle opzioni group by in SQL Data Warehouse di Azure per lo sviluppo di soluzioni.

## <a name="what-does-group-by-do"></a>Qual è la funzione di GROUP BY?

La clausola T-SQL [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) viene usata per aggregare i dati in un set di righe di riepilogo. GROUP BY include alcune opzioni non supportate da SQL Data Warehouse. Per queste opzioni esistono soluzioni alternative.

Queste opzioni sono

* GROUP BY con ROLLUP
* GROUPING SETS
* GROUP BY con CUBE

## <a name="rollup-and-grouping-sets-options"></a>Opzioni di rollup e raggruppamento di set
L'opzione più semplice consiste nell'usare UNION ALL per eseguire il rollup anziché basarsi sulla sintassi esplicita. Il risultato è esattamente lo stesso

Nell'esempio seguente viene usata l'istruzione GROUP BY con l'opzione ROLLUP:
```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Quando si usa ROLLUP, sono necessarie le aggregazioni seguenti per l'esempio precedente:

* Paese e area geografica
* Paese
* Grand Total

Per sostituire ROLLUP e restituire gli stessi risultati, è possibile usare UNION ALL e specificare in modo esplicito le aggregazioni necessarie:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Per sostituire GROUPING SETS, si applica lo stesso principio. È sufficiente creare sezioni UNION ALL per i livelli di aggregazione che si vuole visualizzare.

## <a name="cube-options"></a>Opzioni Cube
È possibile creare un GROUP BY WITH CUBE utilizzando l'approccio UNION ALL. Il problema è che il codice può risultare complesso e difficile da gestire. Per risolvere questo problema è possibile usare questo approccio più avanzato.

Utilizzare l'esempio precedente.

Il primo passaggio consiste nel definire il cubo che definisce tutti i livelli di aggregazione che si desidera creare. È importante tenere conto del CROSS JOIN delle due tabelle derivate. In tal modo tutti i livelli vengono generati automaticamente. Il resto del codice è disponibile per la formattazione.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Di seguito vengono illustrati i risultati dell'operazione CTAS:

![Raggruppare per cubo](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

Il secondo passaggio consiste nel specificare una tabella di destinazione per archiviare i risultati temporanei:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Il terzo passaggio consiste nell’eseguire il ciclo del cubo delle colonne per eseguire l'aggregazione. La query verrà eseguita una volta per ogni riga nella tabella temporanea #Cube e i risultati verranno archiviati nella tabella temporanea #Results

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Infine è possibile restituire i risultati semplicemente leggendo dalla tabella temporanea #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Suddividendo il codice in sezioni e generando un costrutto di ciclo il codice diventa più gestibile e sostenibile.

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).

