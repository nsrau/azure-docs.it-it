---
title: 'Esercitazione: Preparare i dati per eseguire il training di un modello predittivo in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Nella prima parte di questa serie di esercitazioni in tre parti, è possibile preparare i dati da un database del database SQL di Azure per eseguire il training di un modello predittivo in R con Machine Learning Services del database SQL di Azure (anteprima).
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
ms.date: 07/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 698cc089f770d60b6399864c9832fbc8d104c16f
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253801"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Esercitazione: Preparare i dati per eseguire il training di un modello predittivo in R con Machine Learning Services di database SQL di Azure (anteprima)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Nella prima parte di questa serie di esercitazioni in tre parti, si importeranno e prepareranno i dati di un database del database SQL di Azure usando R. Più avanti nella serie, questi dati verranno usati per eseguire il training e la distribuzione di un modello di Machine Learning predittivo in R con Machine Learning Services del database SQL di Azure (anteprima).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Per questa serie di esercitazioni, supporre di essere i proprietari di un'attività di noleggio di sci e di voler stimare il numero di noleggi che verranno richiesti in una data futura. Queste informazioni consentiranno di preparare correttamente le scorte, il personale e le strutture.

Nella prima e nella seconda parte di questa serie si svilupperanno alcuni script R in RStudio per preparare i dati ed eseguire il training di un modello di Machine Learning. Nella terza parte questi script R verranno quindi eseguiti all'interno di un database tramite stored procedure.

In questo articolo si apprenderà come:

> [!div class="checklist"]
>
> * Importare un database di esempio in un database nel database SQL di Azure con R
> * Caricare i dati del database in un frame di dati R
> * Preparare i dati in R identificando alcune colonne come categoriche

Nella [seconda parte](predictive-model-build-compare-tutorial.md) si apprenderà come creare ed eseguire il training di più modelli di Machine Learning in R e quindi scegliere quello più accurato.

Nelle [terza parte](predictive-model-deploy-tutorial.md) si spiegherà come archiviare il modello in un database e quindi creare le stored procedure dagli script R sviluppati nella prima e nella seconda parte. Le stored procedure verranno eseguite in un database per effettuare stime basate sui nuovi dati.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure - Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/) prima di iniziare.

* [Database SQL di Azure con Machine Learning Services (con R)](machine-learning-services-overview.md) abilitato.

* Pacchetto RevoScaleR - Vedere [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) per le opzioni di installazione del pacchetto a livello locale.

* IDE R - Questa esercitazione usa [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Strumento di query SQL - Questa esercitazione presuppone l'uso di [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) oppure [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importare il database di esempio

Il set di dati di esempio usato in questa esercitazione è stato salvato in un file di backup del database **.bacpac** per poter essere scaricato e usato.

1. Scaricare il file [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Seguire le istruzioni in [Importare un file BACPAC nel database del database SQL di Azure o in Istanza gestita di SQL](../../azure-sql/database/database-import.md), usando questi dettagli:

   * Importare dal file **TutorialDB.bacpac** scaricato
   * Durante l'anteprima pubblica, scegliere la configurazione **Gen5/vCore** per il nuovo database
   * Denominare il nuovo database "TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>Caricare i dati in un frame di dati

Per usare i dati in R, si caricheranno i dati dal database in un frame di dati (`rentaldata`).

Creare un nuovo file RScript in RStudio ed eseguire lo script seguente. Sostituire **Server**, **UID** e **PWD** con le proprie informazioni di connessione.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Logical SQL server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

I risultati visualizzati saranno simili ai seguenti:

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>Preparare i dati

In questo database di esempio, la maggior parte della preparazione è già stata completata, ma sarà necessario completare un'altra preparazione qui.
Usare lo script R seguente per identificare tre colonne come *categorie* modificando i tipi di dati in *fattore*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

I risultati visualizzati saranno simili ai seguenti:

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

I dati sono ora pronti per il training.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare con questa esercitazione, eliminare il database TutorialDB dal server.

Seguire questa procedura nel portale di Azure:

1. Dal menu a sinistra nel portale di Azure selezionare **Tutte le risorse** o **Database SQL**.
1. Nel campo **Filtra per nome...** immettere **TutorialDB** e selezionare la sottoscrizione.
1. Selezionare il proprio database TutorialDB.
1. Nella pagina **Panoramica** selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Nella prima parte di questa serie di esercitazioni sono stati completati i passaggi seguenti:

* Importare un database di esempio in un database nel database SQL di Azure con R
* Caricare i dati del database in un frame di dati R
* Preparare i dati in R identificando alcune colonne come categoriche

Per creare un modello di Machine Learning che usa i dati dal database TutorialDB, attenersi alla seconda parte di questa serie di esercitazioni:

> [!div class="nextstepaction"]
> [Esercitazione: Creare un modello predittivo in R con i Machine Learning Services del database SQL di Azure (anteprima)](predictive-model-build-compare-tutorial.md)
