---
title: Combinare set di righe con Join U-SQL in Azure Data Lake Analytics | Microsoft Docs
description: Informazioni su come U-SQL usa gli operatori Join per combinare set di righe in Azure Data Lake Analytics.
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
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 5ffc7116812e9f18f972eac2daff7935b00891f1
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="combine-rowsets-with-u-sql-joins"></a>Combinare set di righe con join U-SQL

U SQL consente di usare operatori di join comuni, quali INNER JOIN, LEFT/RIGHT/FULL OUTER JOIN e SEMI JOIN, per unire tabelle e set di righe (anche quelli prodotti a partire da file).

Lo script seguente, ad esempio, unisce ``@searchlog`` con un log relativo all'impressione dell'annuncio e offre gli annunci per la stringa di query entro una data specifica.

```
@adlog =
    EXTRACT UserId int,
            Ad string,
            Clicked int
    FROM "/Samples/Data/AdsLog.tsv"
    USING Extractors.Tsv();

@join =
    SELECT a.Ad, s.Query, s.Start AS Date
    FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                    ON a.UserId == s.UserId
    WHERE a.Clicked == 1;

OUTPUT @join   
    TO "/output/Searchlog-join.csv"
    USING Outputters.Csv();
```

U-SQL supporta solo la sintassi di join conforme ad ANSI: predicato Rowset1 JOIN Rowset2 ON. _Non_ è supportata la sintassi obsoleta: predicato FROM Rowset1, Rowset2 WHERE.
Il predicato in un JOIN deve essere un join di uguaglianza e non un'espressione. Se si desidera usare un'espressione, è possibile aggiungerla alla clausola SELECT di un set di righe precedente. Se si desidera eseguire un confronto diverso, è possibile spostarlo nella clausola WHERE.

### <a name="next-steps"></a>Passaggi successivi
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Sviluppare script U-SQL mediante Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Uso delle funzioni finestra di U-SQL per i processi di Analisi Azure Data Lake](data-lake-analytics-use-window-functions.md)


