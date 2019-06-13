---
title: 'Esercitazione: Preparare i dati per eseguire il clustering in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Nella prima parte di questa serie di esercitazioni in tre parti verranno preparati i dati di un database SQL di Azure per eseguire il clustering in R con Machine Learning Services del database SQL di Azure (anteprima).
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
ms.date: 05/17/2019
ms.openlocfilehash: 83ef25f04012933c2665e63e4617d480eb336f7b
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419799"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Esercitazione: Preparare i dati per eseguire il clustering in R con Machine Learning Services di database SQL di Azure (anteprima)

Nella prima parte di questa serie di esercitazioni in tre parti verranno preparati i dati di un database SQL di Azure per eseguire il clustering in R con Machine Learning Services del database SQL di Azure (anteprima).

Per *clustering* si intende l'organizzazione dei dati in gruppi, i cui membri presentano analogie.
Per eseguire il clustering di clienti in un set di dati relativi ad acquisti e restituzioni di prodotti, si userà l'algoritmo **K-means**. Organizzando i clienti in cluster, è possibile definire iniziative di marketing più efficaci e mirate a gruppi specifici.
Il clustering K-means è un algoritmo di *apprendimento non supervisionato* che cerca nei dati modelli basati sulle analogie.

L'articolo spiega come:

> [!div class="checklist"]
> * Importare un database di esempio in un database SQL di Azure
> * Separare i clienti in base alle diverse dimensioni
> * Caricare i dati dal database SQL di Azure in un frame di dati con R

Nella [seconda parte](sql-database-tutorial-clustering-model-build.md) si apprenderà come creare ed eseguire il training di un modello di clustering K-means.

Nella [terza parte](sql-database-tutorial-clustering-model-deploy.md) si apprenderà come creare una stored procedure in un database SQL di Azure in grado di eseguire il clustering in base ai nuovi dati.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure - Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/) prima di iniziare.

* Server di database SQL di Azure con Machine Learning Services abilitati - Durante l'anteprima pubblica, Microsoft eseguirà l'onboarding e l'abilitazione dell'apprendimento automatico per il database nuovo o esistente. Seguire la procedura descritta in [Iscriversi per l'anteprima](sql-database-machine-learning-services-overview.md#signup).

* Pacchetto RevoScaleR - Vedere [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) per le opzioni di installazione del pacchetto a livello locale.

* IDE R - Questa esercitazione usa [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Strumento di query SQL - Questa esercitazione presuppone l'uso di [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) oppure [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importare il database di esempio

Il set di dati di esempio usato in questa esercitazione è stato salvato in un file di backup del database **.bacpac** per poter essere scaricato e usato. Questo set di dati deriva dal set di dati [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp) fornito da [Transaction Processing Performance Council (TPC)](http://www.tpc.org/default.asp).

1. Scaricare il file [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Seguire le istruzioni in [Importare un file BACPAC per creare un database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-import), usando questi dettagli:

   * Eseguire l'importazione dal file **tpcxbb_1gb.bacpac** scaricato
   * Durante l'anteprima pubblica, scegliere la configurazione **Gen5/vCore** per il nuovo database
   * Assegnare al nuovo database il nome "tpcxbb_1gb"

## <a name="separate-customers"></a>Separare i clienti

Creare un nuovo file RScript in RStudio ed eseguire lo script seguente.
Nella query SQL si separeranno i clienti in base alle dimensioni seguenti:

* **orderRatio** = rapporto ordini restituiti (numero totale di ordini parzialmente o totalmente restituiti rispetto al numero totale di ordini)
* **itemsRatio** = rapporto articoli restituiti (numero totale di articoli restituiti rispetto al numero di articoli acquistati)
* **monetaryRatio** = rapporto importi restituiti (totale importo monetario di articoli restituiti rispetto all'importo di articoli acquistati)
* **frequency** = frequenza di restituzione

Nella funzione **paste** sostituire **Server**, **UID** e **PWD** con le informazioni di connessione personali.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE 
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE 
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE 
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE 
            WHEN (returns_count IS NULL)
            THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
"
```

## <a name="load-the-data-into-a-data-frame"></a>Caricare i dati in un frame di dati

A questo punto usare lo script seguente per restituire i risultati della query in un frame di dati R usando la funzione **rxSqlServerData**.
Durante il processo si definirà il tipo delle colonne selezionate (con colClasses) per assicurarsi che i tipi vengano trasferiti correttamente a R.

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

Verranno visualizzati risultati simili ai seguenti.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Pulire le risorse

***Se non si intende proseguire con questa esercitazione***, eliminare il database tpcxbb_1gb dal server del database SQL di Azure.

Seguire questa procedura nel portale di Azure:

1. Dal menu a sinistra nel portale di Azure selezionare **Tutte le risorse** o **Database SQL**.
1. Nel campo **Filtra per nome** immettere **tpcxbb_1gb** e selezionare la sottoscrizione.
1. Selezionare il database **tpcxbb_1gb**.
1. Nella pagina **Panoramica** selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Nella prima parte di questa serie di esercitazioni sono stati completati questi passaggi:

* Importare un database di esempio in un database SQL di Azure
* Separare i clienti in base alle diverse dimensioni
* Caricare i dati dal database SQL di Azure in un frame di dati con R

Per creare un modello di Machine Learning che usa questi dati dei clienti, seguire la seconda parte di questa serie di esercitazioni:

> [!div class="nextstepaction"]
> [Esercitazione: Creare un modello predittivo in R con i Machine Learning Services del database SQL di Azure (anteprima)](sql-database-tutorial-clustering-model-build.md)
