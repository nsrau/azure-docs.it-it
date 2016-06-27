<properties 
   pageTitle="Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio | Azure" 
   description="Informazioni su come installare Strumenti di Data Lake per Visual Studio e come sviluppare e testare script U-SQL." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Esercitazione: Introduzione al linguaggio U-SQL di Analisi Data Lake di Azure

U-SQL è un linguaggio che unisce i vantaggi di SQL con la potenza espressiva del codice personale, in modo da poter elaborare tutti i tipi di dati a qualsiasi livello. Le funzionalità di query scalabili e distribuite offerte da U-SQL consentono di analizzare con efficienza i dati presenti nell'archivio e in archivi relazionali, quali il database SQL di Azure. Consentono inoltre di elaborare dati non strutturati applicando lo schema in fase di lettura, nonché inserire logica e funzioni UDF personalizzate e aggiungere estensibilità per permettere il controllo granulare sulle modalità di esecuzione in scala. Per altre informazioni sulla filosofia di progettazione alla base di U-SQL, fare riferimento a questo [post di blog su Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Esistono alcune differenze tra ANSI SQL e T-SQL. Parole chiave come SELECT, ad esempio devono essere scritte in MAIUSCOLO.

Si tratta di un sistema di tipi e un linguaggio di espressione all'interno di clausole SELECT, in cui i predicati sono scritti in C#. In questo modo, i tipi di dati sono tipi C# e usano la semantica NULL di C# e le operazioni di confronto all'interno di un predicato seguono la sintassi di C# (ad esempio, un "foo" = =). Tutti i valori, inoltre, sono oggetti .NET completi e consentono quindi di usare facilmente qualsiasi metodo per eseguire operazioni sull'oggetto (ad es. "f o o o".Split(' ') ).

Per altre informazioni, vedere la pagina di [riferimento su U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

###Prerequisiti

È necessario completare l'[Esercitazione: Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

Nell'esercitazione è stato eseguito un processo di Analisi Data Lake con lo script di U-SQL seguente:

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

Lo script non è stato sottoposto a molte procedure di trasformazione. Legge i dati dal file di origine denominato **SearchLog.tsv**, li schematizza e restituisce il set di righe in un file denominato **SearchLog-first-u-sql.csv**.

Osservare il punto interrogativo accanto al tipo di dati del campo Duration. Significa che il campo può essere anche Null.

Alcuni concetti e parole chiave ricavati dallo script:

- **Variabili del set di righe**: ad ogni espressione di query che produce un set di righe può essere assegnata una variabile. U-SQL segue il modello di denominazione delle variabili T-SQL; ad esempio, **@searchlog** nello script. L'assegnazione, tuttavia, non impone l'esecuzione, ma attribuisce un nome all'espressione e offre la possibilità di sviluppare espressioni più complesse.
- **EXTRACT** offre la possibilità di definire uno schema in fase di lettura. Lo schema viene specificato da un nome di colonna e da una coppia tipo-nome in C# per ogni colonna. Per estrarre file con estensione tsv, usa un cosiddetto **estrattore**; ad esempio, **Extractors.Tsv()** nello script. È possibile sviluppare estrattori personalizzati.
- **OUTPUT** accede a un set di righe e lo serializza. Outputters.Csv() genera quindi un file separato da virgole nel percorso specificato. È possibile sviluppare anche outputter personalizzati.
- Sebbene i due percorsi siano relativi, è possibile usare anche percorsi assoluti. Ad esempio 
    
        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    È necessario usare percorsi assoluti per accedere ai file presenti negli account di archiviazione collegati. La sintassi dei file presenti in un account di Archiviazione di Azure collegato è:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Non sono attualmente supportate autorizzazioni di accesso a contenitori pubblici o a contenitori BLOB di Azure con BLOB pubblici.

## Usare variabili scalari

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
      
## Trasformare set di righe

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

La clausola WHERE usa l'[espressione booleana C#](https://msdn.microsoft.com/library/6a71f45d.aspx). È possibile usare il linguaggio di espressione C# per creare espressioni e funzioni personali. È anche possibile combinarle con congiunzioni (AND) e disgiunzioni (OR) logiche per eseguire operazioni di filtro più complesse.

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
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();
        
Osservare come la seconda query faccia riferimento al risultato del primo set di righe e il risultato sia pertanto una composizione dei due filtri. È possibile riusare un nome di variabile poiché i nomi vengono definiti nell'ambito lessicale.

## Aggregare set di righe

U-SQL consente di usare le istruzioni familiari **ORDER BY** e **GROUP BY** e le aggregazioni.

La query seguente trova la durata totale per ogni area e genera quindi le prime 5 durate, elencate in ordine.

I set di righe U-SQL non mantengono l'ordine per la query successiva. Per ordinare un output, pertanto, è necessario aggiungere ORDER BY all'istruzione OUTPUT, come illustrato di seguito:

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
        
La clausola U-SQL ORDER BY deve essere combinata con la clausola FETCH in un'espressione SELECT.

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

## Unire dati

U SQL consente di usare operatori di join comuni, quali INNER JOIN, LEFT/RIGHT/FULL OUTER JOIN e SEMI JOIN, per unire tabelle e set di righe (anche quelli prodotti a partire da file).

Lo script seguente, ad esempio, unisce searchlog con un log relativo all'impressione dell'annuncio e offre gli annunci per la stringa di query entro una data specifica.

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


U-SQL supporta solo la sintassi di join conforme ad ANSI: predicato Rowset1 JOIN Rowset2 ON. NON è supportata la sintassi obsoleta: predicato FROM Rowset1, Rowset2 WHERE. Il predicato in un JOIN deve essere un join di uguaglianza e non un'espressione. Se si desidera usare un'espressione, è possibile aggiungerla alla clausola SELECT di un set di righe precedente. Se si desidera eseguire un confronto diverso, è possibile spostarlo nella clausola WHERE.

        
## Creare database, funzioni con valori di tabella, viste e tabelle

U-SQL consente di usare dati nel contesto di un database e uno schema. Pertanto, non è sempre necessario eseguire operazioni di lettura o scrittura su un file.

Ogni script U-SQL esegue un database predefinito (master) e uno schema predefinito (dbo) come contesto predefinito. È comunque possibile creare un database e/o uno schema personalizzati. Per modificare il contesto, usare l'istruzione **USE**.


### Creare una funzione con valore di tabella (TVF)

Nel precedente script U-SQL, è stato usato più volte l'oggetto EXTRACT per leggere da uno stesso file di origine. La funzione con valori di tabella U-SQL consente di incapsulare i dati per un riutilizzo futuro.

Lo script seguente crea una funzione TVF denominata *Searchlog()* nel database e nello schema predefiniti:

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
    
Lo script seguente illustra come usare la funzione TVF definita nello script precedente:

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
        
### Creare viste

Se si desidera astrarre una sola espressione di query, senza parametrizzarla, è possibile creare una vista anziché una funzione con valori di tabella.

Lo script seguente crea una vista denominata *SearchlogView* nel database e nello schema predefiniti:

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
    
Lo script seguente illustra l'uso della vista definita:

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

### Creare tabelle 

Analogamente a una tabella di database relazionale, U-SQL consente di creare una tabella con uno schema predefinito oppure creare una tabella e dedurre lo schema dalla query che popola la tabella (nota anche come istruzione CREATE TABLE AS SELECT o CTAS).

Lo script seguente crea un database e due tabelle:

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
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
                    PARTITIONED BY HASH (UserId)
    );
    
    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;
    
    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC) 
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### Eseguire query su tabelle

È possibile eseguire una query su una tabella (creata nello script precedente) nello stesso modo in cui si esegue su un file di dati. Anziché creare un set di righe usando l'istruzione EXTRACT, è possibile ora semplicemente fare riferimento al nome della tabella.

Lo script di trasformazione usato in precedenza viene modificato in modo da leggere i dati direttamente dalle tabelle:

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

Non è attualmente possibile eseguire un'istruzione SELECT in una tabella presente nello stesso script in cui è stata creata la tabella.


##Conclusioni

Gli argomenti coperti in questa esercitazione costituiscono solo una piccola parte delle potenzialità di U-SQL. A causa dell'ambito in cui si svolge questa esercitazione, non è possibile trattare tutti gli elementi, ad esempio:

- Usare CROSS APPLY per decomprimere parti di stringhe, matrici e mappe in righe.
- Usare set di dati partizionati (set di file e tabelle partizionate).
- Sviluppare in C# operatori definiti dall'utente, ad esempio estrattori, outputter, processori e aggregatori definiti dall'utente.
- Usare le funzioni di windowing di U-SQL.
- Gestire il codice U-SQL con visualizzazioni, funzioni con valori di tabella e stored procedure.
- Eseguire codice personalizzato arbitrario sui propri nodi di elaborazione. 
- Connettersi a database SQL di Azure e attuare federazioni di query al loro interno, nonché in U-SQL e nei dati di Azure Data Lake.

## Vedere anche 

- [Panoramica di Analisi Data Lake di Microsoft Azure](data-lake-analytics-overview.md)
- [Sviluppare script U-SQL mediante Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Uso delle funzioni finestra di U-SQL per i processi di Analisi Azure Data Lake](data-lake-analytics-use-window-functions.md)
- [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## Feedback

- [Suggerire un nuovo backlog di documentazione](data-lake-analytics-documentation-backlog.md)
- [Inviare una richiesta di funzionalità](http://aka.ms/adlafeedback)
- [Ottenere informazioni sui forum](http://aka.ms/adlaforums)
- [Fornire feedback su U-SQL](http://aka.ms/usqldiscuss)

<!---HONumber=AcomDC_0615_2016-->