---
title: Introduzione al catalogo di U-SQL | Microsoft Docs
description: Informazioni su come usare il catalogo di U-SQL per condividere codice e dati.
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/09/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 08364c6c7bea53807844e3b1cc327dc3742e0487
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
<a id="get-started-with-the-u-sql-catalog" class="xliff"></a>

# Introduzione al catalogo di U-SQL

<a id="create-a-tvf" class="xliff"></a>

## Creare una funzione con valori di tabella (TVF)

Nel precedente script U-SQL, è stato usato più volte l'oggetto EXTRACT per leggere da uno stesso file di origine. La funzione con valori di tabella (TVF) U-SQL consente di incapsulare i dati per un riutilizzo futuro.  

Lo script seguente crea una funzione TVF denominata `Searchlog()` nel database e nello schema predefiniti:

```
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

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SerachLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

<a id="create-views" class="xliff"></a>

## Creare viste

Se si ha una sola espressione di query, anziché una funzione TVF è possibile usare una VISTA U-SQL per incapsulare l'espressione.

Lo script seguente crea una vista denominata `SearchlogView` nel database e nello schema predefiniti:

```
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

```
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

<a id="create-tables" class="xliff"></a>

## Creare tabelle
Analogamente a una tabella di database relazionale, U-SQL consente di creare una tabella con uno schema predefinito oppure di creare una tabella e dedurre lo schema dalla query che popola la tabella (nota anche come istruzione CREATE TABLE AS SELECT o CTAS).

Lo script seguente crea un database e due tabelle:

```
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

<a id="query-tables" class="xliff"></a>

## Eseguire query su tabelle
È possibile eseguire una query su una tabella, come quelle create nello script precedente, nello stesso modo in cui si esegue su un file di dati. Anziché creare un set di righe usando l'istruzione EXTRACT, è possibile ora fare riferimento al nome della tabella.

Modificare lo script di trasformazione usato in precedenza in modo da leggere i dati direttamente dalle tabelle:

```
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

<a id="next-steps" class="xliff"></a>

## Passaggi successivi
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Sviluppare script U-SQL con Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

