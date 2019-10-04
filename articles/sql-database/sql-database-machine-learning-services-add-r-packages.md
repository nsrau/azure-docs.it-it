---
title: Aggiungere un pacchetto R in Machine Learning Services nel database SQL di Azure (anteprima)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Questo articolo spiega come installare un pacchetto R che non è già installato in Machine Learning Services nel database SQL di Azure (anteprima).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/29/2019
ms.openlocfilehash: f82408a6aaa7cf3a492f3036a6db5d8666b6f160
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598044"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Aggiungere un pacchetto R in Machine Learning Services nel database SQL di Azure (anteprima)

Questo articolo spiega come aggiungere un pacchetto R in Machine Learning Services nel database SQL di Azure (anteprima).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Installare [R](https://www.r-project.org) e [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) nel computer locale. R è disponibile per Windows, MacOS e Linux. Questo articolo presuppone che sia in uso Windows.

- L'articolo include un esempio di utilizzo di [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) per l'esecuzione di uno script R nel database SQL di Azure. È possibile eseguire gli script R tramite altri strumenti di gestione di database o di query, ma in questo esempio si userà Azure Data Studio o SSMS.
   
> [!NOTE]
> Non è possibile installare un pacchetto mediante l'esecuzione di uno script R usando **sp_execute_external_script** in Azure Data Studio o SSMS. È solo possibile installare e rimuovere pacchetti usando la riga di comando di R e RStudio come descritto in questo articolo. Dopo aver installato il pacchetto, è possibile accedere alle funzioni del pacchetto in uno script R usando **sp_execute_external_script**.

## <a name="list-r-packages"></a>Elencare i pacchetti R

Microsoft fornisce diversi pacchetti R preinstallati con Machine Learning Services nel database SQL.
Per visualizzare l'elenco dei pacchetti R installati, eseguire il comando seguente in Azure Data Studio o SSMS.

1. Aprire Azure Data Studio o SSMS e connettersi al database SQL di Azure.

1. Eseguire il comando seguente:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

L'output dovrebbe avere un aspetto simile al seguente.

**Risultati**

![Pacchetti installati in R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Aggiungere un pacchetto con sqlmlutils

Se è necessario usare un pacchetto che non è già installato nel database SQL di Azure, è possibile installarlo tramite [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** è un pacchetto progettato per aiutare gli utenti a interagire con i database SQL (SQL Server e database SQL di Azure) ed eseguire il codice R o Python in SQL da un client R o Python. Attualmente nel database SQL di Azure è supportata solo la versione di **sqlmlutils** per R.

Nell'esempio seguente verrà installato il pacchetto **[glue](https://cran.r-project.org/web/packages/glue/)** che può formattare e interpolare le stringhe. Questa procedura installerà **sqlmlutils** e **RODBCext** (un prerequisito per **sqlmlutils**) e il pacchetto **glue**.

### <a name="install-sqlmlutils"></a>Installare **sqlmlutils**

1. Scaricare il file ZIP di **sqlmlutils** più recente da https://github.com/Microsoft/sqlmlutils/tree/master/R/dist nel computer locale. Non è necessario decomprimere il file.

1. Aprire un **prompt dei comandi** ed eseguire il comando seguente per installare **RODBCext** e **sqlmlutils** nel computer locale. Sostituire il percorso completo del file ZIP di **sqlmlutils** scaricato (l'esempio presuppone che il file si trovi nella cartella Documenti).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    L'output visualizzato dovrebbe avere un aspetto simile al seguente.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Se viene restituito un errore che indica che "'R' non è riconosciuto come comando interno o esterno, un programma eseguibile o un file batch", è probabile che il percorso di R.exe non sia incluso nella variabile di ambiente **PATH** in Windows. È possibile aggiungere il percorso alla variabile di ambiente o passare alla cartella nel prompt dei comandi (ad esempio `cd C:\Program Files\R\R-3.5.3\bin`) e quindi provare a eseguire nuovamente il comando.

### <a name="add-the-package"></a>Aggiungere il pacchetto

1. Aprire RStudio e creare un nuovo file **Script R**. 

1. Usare il codice R seguente per installare il pacchetto **glue** tramite **sqlmlutils**. Sostituire le informazioni di connessione del database SQL di Azure personalizzate.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > L'ambito (**scope**) può essere **PUBLIC** o **PRIVATE**. L'ambito pubblico è utile all'amministratore del database per installare pacchetti che tutti gli utenti possono usare. L'ambito privato rende il pacchetto disponibile solo per l'utente che ne esegue l'installazione. Se non si specifica l'ambito, il valore predefinito è **PRIVATE**.

### <a name="verify-the-package"></a>Verificare il pacchetto

Per verificare che il pacchetto **glue** sia stato installato, eseguire lo script R seguente in RStudio. Usare la stessa **connessione** definita nel passaggio precedente.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Risultati**

![Contenuto della tabella RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Usare il pacchetto

Dopo aver installato il pacchetto, è possibile usarlo in uno script R tramite **sp_execute_external_script**.

1. Aprire Azure Data Studio o SSMS e connettersi al database SQL di Azure.

1. Eseguire il comando seguente:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    library(glue)
    
    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
    ''my age next year is {age + 1}, '',
    ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    
    print(text)
    ';
    ```

    Nella scheda **Messaggi** verrà visualizzato il risultato seguente.

    **Risultati**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Rimuovere il pacchetto

Se si vuole eliminare il pacchetto, eseguire lo script R seguente in RStudio. Usare la stessa **connessione** definita in precedenza.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Un altro modo per installare un pacchetto R nel database SQL di Azure consiste nel caricare il pacchetto R da un flusso di byte tramite l'istruzione **CREATE EXTERNAL LIBRARY** di T-SQL. Vedere [Creare una libreria da un flusso di byte](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) nella documentazione di riferimento per [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Machine Learning Services con R nel database SQL di Azure (anteprima), vedere gli articoli seguenti.

- [Machine Learning Services con R nel database SQL di Azure (anteprima)](sql-database-machine-learning-services-overview.md)
- [Scrivere le funzioni R avanzate nel Database SQL di Azure con Machine Learning Services (anteprima)](sql-database-machine-learning-services-functions.md)
- [Usare dati R e SQL in Machine Learning Services nel database SQL di Azure (anteprima)](sql-database-machine-learning-services-data-issues.md)