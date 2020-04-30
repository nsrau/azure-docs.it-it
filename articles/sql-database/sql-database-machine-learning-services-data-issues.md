---
title: Usare oggetti e tipi di dati R e SQL
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Informazioni su come usare i tipi di dati e gli oggetti dati in R con il database SQL di Azure usando Machine Learning Services (anteprima), inclusi i problemi comuni che potrebbero verificarsi.
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
ROBOTS: NOINDEX
ms.openlocfilehash: e81cca3e20d5b6c050489e80b91d013d5e934cce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453200"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Usare dati R e SQL in Machine Learning Services nel database SQL di Azure (anteprima)

Questo articolo illustra alcuni problemi comuni che possono verificarsi quando si trasferiscono i dati tra R e il database SQL in [Machine Learning Services (con R) nel database SQL di Azure](sql-database-machine-learning-services-overview.md). L'esperienza acquisita con questo esercizio fornisce informazioni di base essenziali per usare i dati in uno script personalizzato.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

I problemi comuni che è possibile riscontrare includono:

- Tipi di dati che talvolta non corrispondono
- Conversioni implicite che possono venire eseguite
- In alcuni casi sono necessarie operazioni cast e convert
- R e SQL usano oggetti dati differenti

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/) prima di iniziare.

- Per eseguire il codice di esempio in questi esercizi, è necessario prima di tutto usare il [database SQL di Azure con Machine Learning Services (con R)](sql-database-machine-learning-services-overview.md) abilitato.

- Assicurarsi di avere installato la versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). È possibile eseguire gli script R tramite altri strumenti di gestione di database o di query, ma in questo Avvio rapido si userà SQL Server Management Studio.

## <a name="working-with-a-data-frame"></a>Uso di un frame di dati

Quando lo script restituisce i risultati da R a SQL, deve restituire i dati come **Data. frame**. Qualsiasi altro tipo di oggetto generato nello script (elenco, fattore, vettore o dati binari) deve essere convertito in un frame di dati se si vuole restituirlo come parte dei risultati della stored procedure. A questo scopo, sono disponibili più funzioni R per il supporto della modifica di altri oggetti in un frame di dati. È anche possibile serializzare un modello binario e restituirlo in un frame di dati, che verrà più avanti in questo articolo.

Per prima cosa, è possibile provare con alcuni oggetti R di base, vettori, matrici ed elenchi, per vedere in che modo la conversione in un frame di dati modifica l'output passato a SQL.

Confrontare questi due script "Hello World" in R. Il loro aspetto è quasi identico, ma il primo restituisce una singola colonna di tre valori, mentre il secondo restituisce tre colonne con un singolo valore ciascuna.

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

Perché i risultati sono così diversi?

La risposta può essere trovata usando il comando `str()` di R. Aggiungere la funzione `str(object_name)` in qualsiasi punto dello script R per fare in modo che lo schema dati dell'oggetto R specificato venga restituito come messaggio informativo. È possibile visualizzare i messaggi nella scheda **messaggi** in SSMS.

Per capire come mai l'Esempio 1 e l'Esempio 2 producano risultati così diversi, inserire la riga `str(OutputDataSet)` alla fine della definizione di variabile `@script` in ogni istruzione, come riportato di seguito:

**Esempio 1 con la funzione str aggiunta**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Esempio 2 con la funzione str aggiunta**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

A questo punto, riesaminare il testo in **Messaggi** per capire perché l'output è differente.

**Risultati - Esempio 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Risultati - Esempio 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Come si può notare, una leggera modifica nella sintassi di R ha un effetto notevole sullo schema dei risultati. Per tutti i dettagli, le differenze nei tipi di dati R sono illustrate in dettaglio nella sezione *strutture dati* in ["Advanced R" di Hadley Wickham](http://adv-r.had.co.nz).

Per il momento, tenere semplicemente presente che è necessario verificare i risultati previsti quando si convertono oggetti R in frame di dati.

> [!TIP]
> È anche possibile usare le funzioni di identità R, `is.matrix`ad `is.vector`esempio, per restituire informazioni sulla struttura dei dati interna.

## <a name="implicit-conversion-of-data-objects"></a>Conversione implicita di oggetti dati

Ogni oggetto dati R ha regole specifiche per la gestione dei valori in combinazione con altri oggetti dati se i due oggetti dati hanno lo stesso numero di dimensioni o se un oggetto dati contiene tipi di dati eterogenei.

Si supponga, ad esempio, di voler eseguire la moltiplicazione di matrici usando R. Si desidera moltiplicare una matrice a colonna singola con tre valori per una matrice con quattro valori e prevedere come risultato una matrice 4x3.

Creare prima di tutto una piccola tabella di dati di test.

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

A questo punto, eseguire lo script seguente.

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

La colonna di tre valori viene convertita automaticamente in un oggetto matrix a colonna singola. Poiché un oggetto matrix è semplicemente un caso speciale di oggetto array in R, l'oggetto array `y` viene convertito in modo implicito in un oggetto matrix a colonna singola per rendere conformi i due argomenti.

**Risultati**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Si noti tuttavia cosa accade se si modificano le dimensioni dell'oggetto array `y`.

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

Adesso R restituisce come risultato un valore singolo.

**Risultati**
    
|Col1|
|---|
|1542|

Perché? In questo caso, dal momento che i due argomenti possono essere gestiti come vettori della stessa lunghezza, R restituisce il prodotto interno come oggetto matrix.  Questo è il comportamento previsto in base alle regole di algebra lineare. Tuttavia, può causare problemi se l'applicazione downstream prevede che lo schema di output non venga mai modificato.

## <a name="merge-or-multiply-columns-of-different-length"></a>Unire o moltiplicare colonne di lunghezza diversa

R offre una notevole flessibilità per l'uso di vettori di dimensioni diverse e per la combinazione di queste strutture di tipo colonna nei frame di dati. Gli elenchi dei vettori sono simili a una tabella, ma non seguono tutte le regole delle tabelle del database.

Ad esempio, lo script seguente definisce un oggetto array numerico di lunghezza 6 e lo archivia nella variabile R `df1`. La matrice numerica viene quindi combinata con i numeri interi della tabella RTestData (creata sopra), che contiene tre (3) valori, per creare un nuovo frame `df2`di dati,.

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

Per riempire il frame di dati, R ripete gli elementi recuperati da RTestData per il numero di volte necessario per corrispondere al numero di elementi nell'oggetto array `df1`.

**Risultati**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Tenere presente che un frame di dati è simile a una tabella, ma in realtà è un elenco di vettori.

## <a name="cast-or-convert-sql-data"></a>Eseguire il cast o convertire i dati SQL

R e SQL non usano gli stessi tipi di dati. Pertanto, quando si esegue una query in SQL per recuperare i dati e passarli al runtime di R, in genere viene eseguita una conversione implicita. Quando si restituiscono dati da R a SQL, si verifica un altro set di conversioni.

- SQL inserisce i dati dalla query al processo R e li converte in una rappresentazione interna per una maggiore efficienza.
- Il runtime R carica i dati in una variabile data.frame ed esegue a sua volta operazioni sui dati.
- Il motore di database restituisce i dati a SQL utilizzando una connessione interna protetta e Visualizza i dati in termini di tipi di dati SQL.
- È possibile ottenere i dati connettendosi a SQL usando un client o una libreria di rete che può eseguire query SQL e gestire set di dati tabulari. Questa applicazione client può influire sui dati in altri modi.

Per visualizzarne il funzionamento, eseguire una query come questa sul data warehouse [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks). Questa vista restituisce i dati di vendita usati per la creazione delle previsioni.

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
> È possibile usare qualsiasi versione di AdventureWorks o creare una query diversa usando un database personalizzato. Il punto consiste nel tentare di gestire alcuni dati contenenti valori di tipo text, DateTime e numeric.

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

Se si verifica un errore, probabilmente sarà necessario apportare alcune modifiche al testo della query. Ad esempio, il predicato stringa nella clausola WHERE deve essere racchiuso tra due set di virgolette singole.

Dopo avere verificato che la query funzioni, esaminare i risultati della funzione `str` per visualizzare la modalità di gestione dei dati di input in R.

**Risultati**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- La colonna datetime è stata elaborata usando il tipo di dati R **POSIXct**.
- La colonna di testo "ProductSeries" è stata identificata come **fattore**, vale a dire una variabile di categoria. Per impostazione predefinita, i valori stringa sono gestiti come fattori. Se si passa una stringa a R, viene convertita in un numero intero per uso interno e quindi rimappata alla stringa nell'output.

## <a name="summary"></a>Riepilogo

Da questi brevi esempi è possibile capire la necessità di controllare gli effetti della conversione dei dati quando si passano query SQL come input. Poiché alcuni tipi di dati SQL non sono supportati da R, considerare questi modi per evitare errori:

- Testare i dati in anticipo e verificare le colonne o i valori nello schema che potrebbero creare problemi quando vengono passati al codice R.
- Specificare le colonne nell'origine dati di input singolarmente, anziché usare `SELECT *`, per vedere in che modo verrà gestita ogni colonna.
- Quando si preparano i dati di input, eseguire cast espliciti in base alle necessità per evitare sorprese.
- Evitare di passare colonne di dati, ad esempio GUID o RowGuid, che provocano errori e che non sono utili per la modellazione.

Per altre informazioni sui tipi di dati R supportati e non supportati, vedere [librerie e tipi di dati](/sql/advanced-analytics/r/r-libraries-and-data-types)r.
