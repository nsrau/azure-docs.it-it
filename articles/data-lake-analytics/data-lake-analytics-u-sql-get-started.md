---
title: Introduzione al linguaggio U-SQL | Microsoft Docs
description: Informazioni sulle basi del linguaggio U-SQL.
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
ms.date: 06/23/2017
ms.author: saveenr
ms.openlocfilehash: 38c4e1b9bd24ef0b8a81f6154620f3f98d3b5ac1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-u-sql"></a>Introduzione a U-SQL
U-SQL è un linguaggio che combina SQL dichiarativo con C# imperativo per consentire di elaborare dati su qualsiasi scala. Le funzionalità di query scalabili e distribuite offerte da U-SQL consentono di analizzare con efficienza i dati presenti in archivi relazionali, ad esempio il database SQL di Azure. Con U-SQL è possibile elaborare i dati non strutturati applicando schemi in lettura e inserendo logiche personalizzate e funzioni definite dall'utente. Inoltre, U-SQL include estendibilità che consente un controllo specifico delle modalità di esecuzione su vasta scala. 

## <a name="learning-resources"></a>Risorse di formazione

* L'[esercitazione su U-SQL](http://aka.ms/usqltutorial) fornisce una procedura dettagliata guidata per la maggior parte del linguaggio U-SQL. Si consiglia la lettura di questo documento a tutti gli sviluppatori che vogliono apprendere il linguaggio U-SQL.
* Per informazioni dettagliate sulla **sintassi del linguaggio U-SQL**, vedere le [Informazioni di riferimento sul linguaggio U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).
* Per informazioni sulla **filosofia di progettazione alla base di U-SQL**, vedere il post sul blog di Visual Studio [Introducing U-SQL – A Language that makes Big Data Processing Easy](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/) (Introduzione a U-SQL: un linguaggio che facilita l'elaborazione dei Big Data).

## <a name="prerequisites"></a>Prerequisiti

Prima di analizzare gli esempi in U-SQL di questo documento, leggere e completare l'[Esercitazione: Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Questa esercitazione illustra la meccanica dell'uso di U-SQL con Strumenti Azure Data Lake per Visual Studio.

## <a name="your-first-u-sql-script"></a>Il primo script U-SQL

Lo script U-SQL seguente è semplice e ci consente di esplorare molti aspetti del linguaggio U-SQL.

```
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

OUTPUT @searchlog   
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Lo script non è stato sottoposto a molte procedure di trasformazione. Legge i dati dal file di origine denominato `SearchLog.tsv`, li schematizza e scrive il set di righe in un file denominato SearchLog-first-u-sql.csv.

Osservare il punto interrogativo accanto al tipo di dati del campo `Duration`. Significa che il campo `Duration` può avere anche un valore null.

### <a name="key-concepts"></a>Concetti chiave
* **Variabili del set di righe**: ad ogni espressione di query che produce un set di righe può essere assegnata una variabile. U-SQL segue il modello di denominazione delle variabili T-SQ, ad esempio `@searchlog`, nello script.
* La parola chiave **EXTRACT** legge i dati di un file e definisce lo schema sulla lettura. `Extractors.Tsv` è un estrattore di U-SQL integrato per i valori separati da tabulazioni. È possibile sviluppare estrattori personalizzati.
* **OUTPUT** scrive i dati da un set di righe a un file. `Outputters.Csv()` è un outputter di U-SQL integrato per creare un file con valori separati da virgole. È possibile sviluppare anche outputter personalizzati.

### <a name="file-paths"></a>Percorsi di file

Le istruzioni EXTRACT e OUTPUT usano percorsi di file. I percorsi di file possono essere assoluti o relativi:

Il seguente percorso di file assoluto fa riferimento a un file in un Data Lake Store denominato `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Il seguente percorso di file inizia con `"/"`. Fa riferimento a un file nell'account di Data Lake Store predefinito:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Usare variabili scalari

Per gestire più facilmente lo script, è possibile usare anche variabili scalari. Lo script U-SQL precedente può essere anche scritto come indicato di seguito:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Trasformare set di righe

Usare l'istruzione **SELECT** per trasformare set di righe:

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

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

La clausola WHERE usa un'[espressione booleana C#](https://msdn.microsoft.com/library/6a71f45d.aspx). È possibile usare il linguaggio di espressione C# per creare espressioni e funzioni personali. È anche possibile combinarle con congiunzioni (AND) e disgiunzioni (OR) logiche per eseguire operazioni di filtro più complesse.

Lo script seguente usa il metodo DateTime.Parse() e una congiunzione.

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

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datetime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >La seconda query fa riferimento al risultato del primo set di righe e il risultato è pertanto una composizione dei due filtri. È anche possibile riusare un nome di variabile poiché i nomi vengono definiti nell'ambito lessicale.

## <a name="aggregate-rowsets"></a>Aggregare set di righe
U-SQL consente di usare le istruzioni familiari ORDER BY e GROUP BY, nonché le aggregazioni.

La query seguente trova la durata totale per area e mostra quindi le prime cinque durate, elencate in ordine.

I set di righe U-SQL non mantengono l'ordine per la query successiva. Per ordinare un output, pertanto, è necessario aggiungere ORDER BY all'istruzione OUTPUT:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

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

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

La clausola U-SQL ORDER BY richiede l'uso della clausola FETCH in un'espressione SELECT.

È possibile usare la clausola U-SQL HAVING per limitare l'output ai gruppi che soddisfano la condizione HAVING:

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

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
        GROUP BY Region
        HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Per gli scenari avanzati di aggregazione, vedere la documentazione di riferimento U-SQL per le [funzioni di aggregazione, analisi e riferimento](https://msdn.microsoft.com/en-us/library/azure/mt621335.aspx)

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Sviluppare script U-SQL con Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
