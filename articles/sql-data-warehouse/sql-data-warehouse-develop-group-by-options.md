<properties
   pageTitle="Opzioni group by in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l’implementazione delle opzioni group by in SQL Data Warehouse di Azure per lo sviluppo di soluzioni."
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
   ms.date="01/04/2016"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Opzioni Group by in SQL Data Warehouse

La clausola [GROUP BY] viene utilizzata per aggregare i dati a un set di righe di riepilogo. Include inoltre alcune opzioni che ne estendono la funzionalità che devono essere evitate in quanto non sono supportate direttamente SQL Data Warehouse di Azure.

Queste opzioni sono: GROUP BY WITH ROLLUP, GROUPING SETS e GROUP BY WITH CUBE

## Opzioni di rollup e raggruppamento di set
L'opzione più semplice consiste nell'utilizzare `UNION ALL` invece di eseguire il rollup anziché utilizzare la sintassi esplicita. Il risultato è esattamente lo stesso

Di seguito è riportato un esempio di istruzione group by utilizzando l’opzione `ROLLUP`:

```
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

Tramite ROLLUP sono state richieste le aggregazioni seguenti: paese e regione, paese, totale complessivo

Per sostituire questo valore è necessario utilizzare `UNION ALL`; specificando le aggregazioni necessarie in modo esplicito per restituire gli stessi risultati:

```
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

Per GROUPING SETS è necessario adottare la stessa entità e creare solo sezioni UNION ALL per i livelli di aggregazione che si desidera visualizzare.

## Opzioni Cube
È possibile creare un GROUP BY WITH CUBE utilizzando l'approccio UNION ALL. Il problema è che il codice può risultare complesso e difficile da gestire. Per risolvere questo problema è possibile utilizzare questo approccio avanzato.

Utilizzare l'esempio precedente.

Il primo passaggio consiste nel definire il cubo che definisce tutti i livelli di aggregazione che si desidera creare. È importante tenere conto del CROSS JOIN delle due tabelle derivate. In tal modo tutti i livelli vengono generati automaticamente. Il resto del codice è disponibile per la formattazione.

```
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

Di seguito sono illustrati i risultati del CTAS:

![][1]

Il secondo passaggio consiste nel specificare una tabella di destinazione per archiviare i risultati temporanei:

```
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

```
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

```
SELECT * 
FROM #Results
ORDER BY 1,2,3
;
```

Suddividendo il codice in sezioni e generando un costrutto di ciclo il codice diventa più gestibile e gestibile e sostenibile.


## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[GROUP BY]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0107_2016-->