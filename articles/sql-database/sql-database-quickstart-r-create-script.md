---
title: Creare ed eseguire script R semplici
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Eseguire script R semplici nei Machine Learning Services nel database SQL di Azure (anteprima).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: ada09959391c551a9eff4d96b186be29c1e3b7a8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013104"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Creare ed eseguire script R semplici nei Machine Learning Services nel database SQL di Azure (anteprima)

In questo avvio rapido viene creato ed eseguito un set di script R semplici tramite l'anteprima pubblica di [Machine Learning Services (con R) nel Database SQL di Azure](sql-database-machine-learning-services-overview.md). Si apprenderà come eseguire il wrapping di uno script R ben formato nella stored procedure [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) ed eseguire lo script in un database SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/) prima di iniziare.

- Per eseguire il codice di esempio in questi esercizi, è necessario disporre di un database SQL di Azure con Machine Learning Services (con R) abilitato. Durante l'anteprima pubblica, Microsoft eseguirà l'onboarding e l'abilitazione dell'apprendimento automatico per il database nuovo o esistente. Seguire la procedura descritta in [Iscriversi per l'anteprima](sql-database-machine-learning-services-overview.md#signup).

- Assicurarsi di avere installato la versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). È possibile eseguire gli script R tramite altri strumenti di gestione di database o di query, ma in questo avvio rapido si userà SQL Server Management Studio.

- Questo avvio rapido richiede di configurare una regola del firewall a livello di server. Per informazioni su come eseguire questa operazione, vedere [Creare una regola del firewall a livello di server](sql-database-server-level-firewall-rule.md).

## <a name="run-a-simple-script"></a>Eseguire uno script semplice

Per eseguire uno script R, passarlo come argomento alla stored procedure di sistema [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

Nei seguenti passaggi, si eseguirà questo script R di esempio nel database SQL:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Aprire **SQL Server Management Studio** e connettersi al database SQL.

   Per assistenza con la connessione, vedere [Avvio rapido: Usare SQL Server Management Studio per connettersi a un database SQL di Azure ed eseguire query](sql-database-connect-query-ssms.md).

1. Passare lo script R completo alla stored procedure [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   Lo script viene passato tramite l'argomento `@script`. Tutto il contenuto all'interno dell'argomento `@script` deve essere codice R valido.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Se si verificano errori, è possibile che l'anteprima pubblica di Machine Learning Services (con R) non sia abilitata per il database SQL in uso. Vedere i [prerequisiti](#prerequisites) riportati sopra.

   > [!NOTE]
   > Gli amministratori possono eseguire il codice esterno automaticamente. È possibile concedere autorizzazioni ad altri utenti usando il comando:
   <br>**GRANT EXECUTE ANY EXTERNAL SCRIPT TO** *\<username\>*.

2. Viene calcolato il risultato corretto e la funzione `print` di R restituisce il risultato nella finestra **Messaggi**.

   Dovrebbe essere visualizzata una schermata analoga alla seguente.

    **Risultati**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Eseguire uno script Hello World

Uno script di esempio tipico è quello che restituisce semplicemente la stringa "Hello World". Eseguire il comando indicato di seguito.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Gli input per questa stored procedure includono:

| | |
|-|-|
|*@language* | definisce l'estensione del linguaggio da chiamare, in questo caso, R |
|*@script* | definisce i comandi passati al runtime di R. L'intero script R deve essere incluso in questo argomento, come testo Unicode. È anche possibile aggiungere il testo a una variabile di tipo **nvarchar** e quindi richiamare i dati della variabile |
|*@input_data_1* | restituiti dalla query, passati al runtime di R, che restituisce i dati a SQL Server come frame di dati |
|WITH RESULT SETS | clausola che definisce lo schema della tabella dati restituita per SQL Server, aggiungendo "Hello World" come nome della colonna, **int** per il tipo di dati |

Il comando restituisce il testo seguente:

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Uso di input e output

Per impostazione predefinita [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) accetta un singolo set di dati come input, che in genere viene fornito sotto forma di una query SQL valida. Restituisce quindi un singolo frame di dati R come output.

È possibile passare come parametro un solo set di dati di input e si può restituire un solo set di dati. Tuttavia, è possibile chiamare altri set di dati dall'interno del codice R e restituire output di altri tipi in aggiunta al set di dati. È anche possibile aggiungere la parola chiave OUTPUT a qualsiasi parametro per fare in modo che venga restituito con i risultati.

Per il momento verranno usate le variabili di input e output predefinite di [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** e **OutputDataSet**.

1. Creare una piccola tabella di dati di test.

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

1. Usare l'istruzione `SELECT` per eseguire query sulla tabella.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Risultati**

    ![Contenuto della tabella RTestData](./media/sql-database-connect-query-r/select-rtestdata.png)

1. Eseguire lo script R seguente. Recupera i dati dalla tabella usando l'istruzione `SELECT`, li passa attraverso il runtime di R e restituisce i dati come frame di dati. La clausola `WITH RESULT SETS` definisce lo schema della tabella di dati restituita per il database SQL, aggiungendo il nome di colonna *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Risultati**

    ![Output dello script R che restituisce i dati da una tabella](./media/sql-database-connect-query-r/r-output-rtestdata.png)

1. Si modificherà ora il nome delle variabili di input e di output. Il nomi predefiniti delle variabili di input e di output sono **InputDataSet** e **OutputDataSet**; questo script permette di modificare i nomi in **SQL_in** e **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Si noti che R fa distinzione tra maiuscole e minuscole. Le variabili di input e di output utilizzate nello script R (**SQL_out**, **SQL_in**) devono corrispondere ai valori definiti con `@input_data_1_name` e `@output_data_1_name`, incluse le maiuscole/minuscole.

   > [!TIP]
   > È possibile passare come parametro un solo set di dati di input e si può restituire un solo set di dati. Tuttavia, è possibile chiamare altri set di dati dall'interno del codice R e restituire output di altri tipi in aggiunta al set di dati. È anche possibile aggiungere la parola chiave OUTPUT a qualsiasi parametro per fare in modo che venga restituito con i risultati.

1. È anche possibile generare valori usando semplicemente lo script R senza dati di input (`@input_data_1` è impostato su vuoto).

   Lo script seguente restituisce il testo "hello" e "world".

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Risultati**

    ![Risultati della query usando @script come input](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Verificare la versione di R

Per verificare la versione di R installata nel database SQL, eseguire lo script seguente.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

La funzione `print` di R restituisce la versione nella finestra **Messaggi**. Nell'output di esempio seguente si può vedere che in questo caso nel database SQL è installata la versione 3.4.4 di R.

**Risultati**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>Elencare i pacchetti R

Microsoft fornisce una serie di pacchetti R preinstallati con Machine Learning Services nel database SQL.

Per visualizzare un elenco dei pacchetti R installati, incluse informazioni su versione, dipendenze, licenza e percorso della libreria, eseguire lo script seguente.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

L'output viene da `installed.packages()` in R ed è restituito come set di risultati.

**Risultati**

![Pacchetti installati in R](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="next-steps"></a>Passaggi successivi

Per creare un modello di Machine Learning tramite R nel Database SQL, seguire questo avvio rapido:

> [!div class="nextstepaction"]
> [Create and train a predictive model in R with Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-train-score-model.md) (Creare ed eseguire il training di un modello predittivo in R con Machine Learning Services di database SQL di Azure (anteprima))

Per altre informazioni su Machine Learning Services, vedere gli articoli seguenti. Anche se alcuni di questi articoli sono per SQL Server, la maggior parte delle informazioni è applicabile anche a Machine Learning Services (con R) nel database SQL di Azure.

- [Machine Learning Services (con R) nel database SQL di Azure](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Esercitazione: Informazioni sull'analisi nel database con R in SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [End-to-end data science walkthrough for R and SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough) (Procedura dettagliata di data science end-to-end per R e SQL Server)
- [Esercitazione: Usare funzioni R di RevoScaleR con dati di SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
