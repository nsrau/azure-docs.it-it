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
ROBOTS: NOINDEX
ms.openlocfilehash: 6645b50a6cd2d2145f9510ca2e2de0ee702fc3ad
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053069"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Avvio rapido: Creare ed eseguire script R semplici nei Machine Learning Services nel database SQL di Azure (anteprima)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In questo argomento di avvio rapido viene creato ed eseguito un set di script R tramite Machine Learning Services (con R) in Database SQL di Azure.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Database nel database SQL di Azure](single-database-create-quickstart.md) con una [regola del firewall a livello di server](firewall-create-server-level-portal-quickstart.md)
- [Machine Learning Services](machine-learning-services-overview.md) con R abilitato.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

Questo esempio usa la stored procedure [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) per eseguire il wrapping di uno script R ben formato.

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

   Per assistenza con la connessione, vedere [Avvio rapido: Usare SQL Server Management Studio per connettersi a un database SQL di Azure ed eseguire query](connect-query-ssms.md).

1. Passare lo script R completo alla stored procedure [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   Lo script viene passato tramite l'argomento `@script`. Tutti gli elementi all'interno dell'argomento `@script` devono essere costituiti da codice R valido.

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
   <br>**GRANT EXECUTE ANY EXTERNAL SCRIPT TO** *\<username\>* .

2. Viene calcolato il risultato corretto e la funzione `print` di R restituisce il risultato nella finestra **Messaggi**.

   Il risultato sarà simile al seguente.

    **Risultati**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Eseguire uno script Hello World

Uno script di esempio tipico è quello che restituisce come output la stringa "Hello World". Eseguire il comando seguente.

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
| @language | Definisce l'estensione del linguaggio da chiamare, in questo caso R |
| @script | Definisce i comandi passati al runtime R. L'intero script R deve essere incluso in questo argomento, come testo Unicode. È anche possibile aggiungere il testo a una variabile di tipo **nvarchar** e quindi chiamare la variabile |
| @input_data_1 | Dati restituiti dalla query, passati al runtime R, che restituisce i dati come frame di dati |
|WITH RESULT SETS | Clausola che definisce lo schema della tabella dati restituita, aggiungendo "Hello World" come nome di colonna e **int** per il tipo di dati |

Il comando restituisce il testo seguente:

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Usare input e output

Per impostazione predefinita [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) accetta un singolo set di dati come input, che in genere viene fornito sotto forma di una query SQL valida. Restituisce quindi un singolo frame di dati R come output.

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

1. Usare l'istruzione `SELECT` per eseguire una query sulla tabella.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Risultati**

    ![Contenuto della tabella RTestData](./media/r-script-create-quickstart/select-rtestdata.png)

1. Eseguire lo script R seguente. Questo script recupera i dati dalla tabella usando l'istruzione `SELECT`, li passa nel runtime R e restituisce i dati come frame di dati. La clausola `WITH RESULT SETS` definisce lo schema della tabella di dati restituita per il database SQL, aggiungendo il nome di colonna *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Risultati**

    ![Output dello script R che restituisce i dati di una tabella](./media/r-script-create-quickstart/r-output-rtestdata.png)

1. Modificare ora i nomi delle variabili di input e di output. I nomi delle variabili di input e di output predefiniti sono **InputDataSet** e **OutputDataSet**. Questo script cambia i nomi in **SQL_in** e **SQL_out**:

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

1. È anche possibile generare i valori usando solo lo script R senza dati di input (`@input_data_1` è impostato su un valore vuoto).

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

    ![Risultati di query con @script come input](./media/r-script-create-quickstart/r-data-generated-output.png)

## <a name="check-r-version"></a>Controllare la versione di R

Per verificare la versione di R installata nel database SQL, eseguire lo script seguente.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

La funzione R `print` restituisce la versione nella finestra **Messaggi**. Nell'output di esempio seguente si può vedere che in questo caso nel database SQL è installata la versione 3.4.4 di R.

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

Per visualizzare un elenco dei pacchetti R installati, incluse le informazioni su versione, dipendenze, licenza e percorso della libreria, eseguire lo script seguente.

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

L'output proviene da `installed.packages()` in R e viene restituito come set di risultati.

**Risultati**

![Pacchetti installati in R](./media/r-script-create-quickstart/r-installed-packages.png)

## <a name="next-steps"></a>Passaggi successivi

Per creare un modello di Machine Learning tramite R nel Database SQL, seguire questo avvio rapido:

> [!div class="nextstepaction"]
> [Create and train a predictive model in R with Azure SQL Database Machine Learning Services (preview)](r-train-score-model-create-quickstart.md) (Creare ed eseguire il training di un modello predittivo in R con Machine Learning Services di database SQL di Azure (anteprima))

Per altre informazioni su Machine Learning Services con R nel database SQL di Azure (anteprima), vedere gli articoli seguenti.

- [Machine Learning Services con R nel database SQL di Azure (anteprima)](machine-learning-services-overview.md)
- [Scrivere le funzioni R avanzate nel Database SQL di Azure con Machine Learning Services (anteprima)](machine-learning-services-functions.md)
- [Usare dati R e SQL in Machine Learning Services nel database SQL di Azure (anteprima)](machine-learning-services-data-issues.md)
