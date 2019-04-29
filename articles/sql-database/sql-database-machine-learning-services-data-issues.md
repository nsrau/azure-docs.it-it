---
title: Lavora con tipi di dati SQL e R e oggetti
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Informazioni su come usare i tipi di dati e oggetti dati in R con Database SQL di Azure usando servizi di Machine Learning (anteprima), inclusi i problemi comuni che potrebbero verificarsi.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 069a2a5b3b26bf517b57034f05ab7080ab392319
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702521"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Usare i dati di R e SQL in Azure SQL Database servizi Machine Learning (anteprima)

Questo articolo illustra alcuni dei problemi comuni che possono verificarsi durante lo spostamento dei dati tra R e SQL Database in [Machine Learning Services (con R) nel Database SQL di Azure](sql-database-machine-learning-services-overview.md). L'esperienza che è possibile ottenere questo esercizio vengono fornite informazioni essenziali quando si lavora con i dati in uno script personalizzato.

Problemi comuni che possono verificarsi includono:

- Tipi di dati talvolta non corrispondono
- Le conversioni implicite potrebbero essere eseguito
- In alcuni casi sono necessarie operazioni cast e convert
- R e SQL usano oggetti dati diversi

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/) prima di iniziare.

- Per eseguire il codice di esempio in questi esercizi, è necessario disporre di un database SQL di Azure con Machine Learning Services (con R) abilitato. Durante l'anteprima pubblica, Microsoft eseguirà l'onboarding e l'abilitazione dell'apprendimento automatico per il database nuovo o esistente. Seguire la procedura descritta in [Iscriversi per l'anteprima](sql-database-machine-learning-services-overview.md#signup).

- Assicurarsi di avere installato la versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). È possibile eseguire gli script R tramite altri strumenti di gestione di database o di query, ma in questo Avvio rapido si userà SQL Server Management Studio.

## <a name="working-with-a-data-frame"></a>Utilizzo di un frame di dati

Quando lo script restituisce risultati da R a SQL, deve restituire i dati come una **frame**. Qualsiasi altro tipo di oggetto generato nello script, che può essere un elenco, fattore, vettore o dati binari - deve essere convertito in un frame di dati se si vuole restituirlo come parte dei risultati della stored procedure. Per fortuna, esistono più funzioni R per supportare la modifica di altri oggetti in un frame di dati. È anche possibile serializzare un modello binario e restituirlo in un frame di dati che verranno eseguite più avanti in questo articolo.

In primo luogo, è possibile provare a usare alcuni oggetti R di base - vettori, matrici ed elenchi - e vedere come la conversione in un frame di dati cambia l'output passato a SQL.

Confrontare questi due script "Hello World" in R. Gli script sembrano quasi identici, ma il primo restituisce una singola colonna di tre valori, mentre il secondo restituisce tre colonne con un singolo valore ciascuna.

**Esempio 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**Esempio 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Il motivo per cui sono riportati i risultati così diversi?

La risposta in genere sono reperibili usando R `str()` comando. Aggiungere la funzione `str(object_name)` in qualsiasi punto nello script R in modo che i dati dello schema dell'oggetto R specificato venga restituito come messaggio informativo. È possibile visualizzare i messaggi nel **messaggi** scheda in SSMS.

Per capire il motivo per cui esempio 1 e 2 producano risultati così diversi, inserire la riga `str(OutputDataSet)` alla fine del `@script` definizione di variabile in ogni istruzione, simile al seguente:

**Esempio 1 con la funzione str aggiunto**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Esempio 2 con la funzione str aggiunto**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

A questo punto, esaminare il testo nel **messaggi** per visualizzare il motivo per cui l'output è diverso.

**Risultati - esempio 1**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Risultati - esempio 2**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Come può notare, una leggera modifica nella sintassi di R ha un effetto notevole sullo schema dei risultati. Per tutti i dettagli, le differenze nei tipi di dati R sono descritte dettagliatamente nella *strutture di dati* sezione ["Advanced R" di Hadley Wickham](http://adv-r.had.co.nz).

Per il momento, tenere semplicemente che è necessario verificare i risultati previsti quando si convertono oggetti R in frame di dati.

> [!TIP]
> È anche possibile usare funzioni di identità R, ad esempio `is.matrix`, `is.vector`per restituire informazioni sulla struttura dei dati interni.

## <a name="implicit-conversion-of-data-objects"></a>Conversione implicita di oggetti dati

Ogni oggetto dati R ha regole specifiche per la gestione dei valori in combinazione con altri oggetti dati se i due oggetti dati hanno lo stesso numero di dimensioni o se un oggetto dati contiene tipi di dati eterogenei.

Ad esempio, si supponga di che voler eseguire la moltiplicazione di matrici usando R. È necessario moltiplicare matrix a colonna singola con tre valori da una matrice con quattro valori e si prevede di conseguenza un 4x3 matrix.

Innanzitutto, creare una piccola tabella di dati di test.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

Ora eseguire lo script seguente.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

Dietro le quinte, la colonna di tre valori viene convertita in un oggetto matrix a colonna singola. Poiché una matrice è semplicemente un caso speciale di oggetto array in R, la matrice `y` viene implicitamente convertito in una matrice a colonna singola per rendere conformi i due argomenti.

**Risultati**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Si noti tuttavia cosa accade quando si modificano le dimensioni della matrice `y`.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

Adesso R restituisce un valore singolo come risultato.

**Risultati**
    
|Col1|
|---|
|1542|

Perché? In questo caso, poiché i due argomenti possono essere gestiti come vettori della stessa lunghezza, R restituisce il prodotto interno come matrice.  Si tratta del comportamento previsto in base alle regole dell'algebra lineare. Tuttavia, potrebbe causare problemi se l'applicazione downstream prevede lo schema di output non venga mai modificato.

## <a name="merge-or-multiply-columns-of-different-length"></a>Unire o moltiplicare colonne di lunghezza diversa

R offre una notevole flessibilità per l'uso di vettori di dimensioni diverse e per la combinazione di queste strutture di tipo colonna nei frame di dati. Gli elenchi dei vettori sono simili a una tabella, ma non seguono tutte le regole che controllano le tabelle del database.

Ad esempio, lo script seguente definisce una matrice numerica di lunghezza 6 e lo archivia nella variabile R `df1`. La matrice numerica viene quindi combinata con i numeri interi della tabella RTestData (creato in precedenza) che contiene tre (3) i valori, per creare un nuovo frame di dati, `df2`.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

Per compilare un frame di dati, R ripete gli elementi recuperati da RTestData per tutte le volte in base alle esigenze per corrispondere al numero di elementi nella matrice `df1`.

**Risultati**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Tenere presente che un frame di dati solo una tabella è simile, ma è effettivamente un elenco di vettori.

## <a name="cast-or-convert-sql-data"></a>Cast o convert dati SQL

R e SQL non usano gli stessi tipi di dati, in modo che quando si esegue una query in SQL per ottenere i dati e quindi passarli al runtime di R, qualche tipo di conversione implicita in genere viene eseguita. Un altro set di conversioni ha luogo quando si restituiscono i dati da R a SQL.

- SQL inserisce i dati dalla query per il processo R e lo converte in una rappresentazione interna per una maggiore efficienza.
- Il runtime R carica i dati in una variabile di frame di dati ed esegue la propria operazioni sui dati.
- Il motore di database restituisce i dati in SQL usando una connessione interna protetta e presenta i dati in termini di tipi di dati SQL.
- Ottenere i dati tramite la connessione a SQL usando una libreria client o di rete che può eseguire query SQL e gestire set di dati tabulari. Questa applicazione client può influire sui dati in altri modi.

Per visualizzarne il funzionamento, eseguire una query come quella nel [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) data warehouse di. Questa vista restituisce i dati di vendita usati nella creazione delle previsioni.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> È possibile usare qualsiasi versione di AdventureWorks, o creare una query diversa usando un database proprio. Il punto è possibile gestire alcuni dati che contiene testo, data/ora e valori numerici.

A questo punto, provare a usare questa query come input per la stored procedure.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

Se si verifica un errore, si sarà probabilmente necessario apportare alcune modifiche al testo della query. Ad esempio, il predicato stringa nella clausola WHERE deve essere racchiuso tra due set di virgolette singole.

Dopo avere ottenuto la query funzioni, esaminare i risultati del `str` funzione per visualizzare il modo in cui R considera i dati di input.

**Risultati**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- La colonna datetime è stata elaborata usando il tipo di dati R **POSIXct**.
- La colonna di testo "ProductSeries" è stata identificata come una **factor**, vale a dire una variabile di categoria. Per impostazione predefinita, i valori stringa vengono gestiti come fattori. Se si passa una stringa a R, viene convertito in un numero intero per uso interno e quindi rimappata alla stringa nell'output.

## <a name="summary"></a>Riepilogo

Anche questi esempi brevi, si noterà la necessità di verificare gli effetti della conversione dei dati durante il passaggio SQL esegue una query come input. Perché alcuni tipi di dati SQL non sono supportati da R, prendere in considerazione questi modi per evitare errori:

- Testare i dati in anticipo e verificare le colonne o valori nello schema che è stato possibile creare problemi quando vengono passati al codice R.
- Specificare le colonne nell'origine dati di input singolarmente, anziché utilizzare `SELECT *`e sapere come verrà gestita ogni colonna.
- Quando si preparano i dati di input, per evitare sorprese, eseguire cast espliciti in base alle esigenze.
- Evitare il passaggio di dati delle colonne (ad esempio GUID o ROWGUID) che generano errori e non è utili per la modellazione.

Per altre informazioni sui tipi di dati R supportati e non supportati, vedere [tipi di dati e le librerie R](/sql/advanced-analytics/r/r-libraries-and-data-types.md).
