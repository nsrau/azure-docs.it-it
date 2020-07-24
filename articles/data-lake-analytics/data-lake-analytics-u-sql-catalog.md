---
title: Usare il catalogo di U-SQL in Azure Data Lake Analytics
description: Informazioni su come usare il catalogo di U-SQL per condividere codice e dati. Creare funzioni con valori di tabella, creare visualizzazioni, creare tabelle ed eseguire query su di esse.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: how-to
ms.date: 05/09/2017
ms.openlocfilehash: 0ef1bff996589d09326e80b770953bd6b34966c5
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132433"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Introduzione al catalogo U-SQL di Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Creare una funzione con valori di tabella (TVF)

Nel precedente script U-SQL, è stato usato più volte l'oggetto EXTRACT per leggere da uno stesso file di origine. La funzione con valori di tabella (TVF) U-SQL consente di incapsulare i dati per un riutilizzo futuro.  

Lo script seguente crea una funzione TVF denominata `Searchlog()` nel database e nello schema predefiniti:

```usql
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
RETURN;
END;
```

Lo script seguente illustra come usare la funzione con valori di tabella definita nello script precedente:

```usql
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SearchLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Creare viste

Se si ha una sola espressione di query, anziché una funzione TVF è possibile usare una VISTA U-SQL per incapsulare l'espressione.

Lo script seguente crea una vista denominata `SearchlogView` nel database e nello schema predefiniti:

```usql
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

Lo script seguente illustra l'uso della vista definita:

```usql
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>Creare tabelle
Analogamente a una tabella di database relazionale, U-SQL consente di creare una tabella con uno schema predefinito oppure di creare una tabella e dedurre lo schema dalla query che popola la tabella (nota anche come istruzione CREATE TABLE AS SELECT o CTAS).

Lo script seguente crea un database e due tabelle:

```usql
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>Eseguire query su tabelle
È possibile eseguire una query su una tabella, come quelle create nello script precedente, nello stesso modo in cui si esegue su un file di dati. Anziché creare un set di righe usando l'istruzione EXTRACT, è possibile ora fare riferimento al nome della tabella.

Modificare lo script di trasformazione usato in precedenza in modo da leggere i dati direttamente dalle tabelle:

```usql
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >Non è attualmente possibile eseguire un'istruzione SELECT in una tabella presente nello stesso script in cui è stata creata la tabella.

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica di Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Sviluppare script U-SQL con Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorare e risolvere i problemi Azure Data Lake Analytics processi utilizzando portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
