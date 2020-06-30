---
title: 'Esercitazione: Distribuire un modello di clustering in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Nella terza parte di questa serie di esercitazioni in tre parti verrà distribuito un modello di clustering in R con Machine Learning Services del database SQL di Azure (anteprima).
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
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2a90003c9de2cf0e942b518c6d4e9c3569039caf
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85251421"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Esercitazione: Distribuire un modello di clustering in R con Machine Learning Services del database SQL di Azure (anteprima)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Nella terza parte di questa serie di esercitazioni in tre parti verrà distribuito un modello di clustering, sviluppato in R, in un database SQL del database SQL di Azure usando Machine Learning Services del database SQL di Azure (anteprima).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Si creerà una stored procedure con uno script R incorporato che esegue il clustering. Poiché il modello viene eseguito nel database SQL di Azure, è possibile eseguire facilmente il training con i dati archiviati nel database.

In questo articolo si apprenderà come:

> [!div class="checklist"]
>
> * Creare una stored procedure che genera il modello
> * Eseguire il clustering nel database SQL
> * Usare le informazioni sul clustering

Nella [prima parte](clustering-model-prepare-data-tutorial.md) si è appreso come preparare i dati di un database per il clustering.

Nella [seconda parte](clustering-model-build-tutorial.md) si è appreso come creare ed eseguire il training di un modello di clustering K-means in R.

## <a name="prerequisites"></a>Prerequisiti

* Per la terza parte di questa serie di esercitazioni è necessario aver completato la [**prima parte**](clustering-model-prepare-data-tutorial.md) e la [**seconda parte**](clustering-model-build-tutorial.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Creare una stored procedure che genera il modello

Eseguire lo script T-SQL seguente per creare la stored procedure. La stored procedure ricrea i passaggi sviluppati nella prima e nella seconda parte di questa serie di esercitazioni:

* Classificare i clienti in base alla cronologia degli acquisti e dei resi
* Generare quattro cluster di clienti usando un algoritmo K-Means

La stored procedure archivia i mapping dei cluster di clienti risultanti nella tabella di database **customer_return_clusters**.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE
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
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>Eseguire il clustering nel database SQL

A questo punto, dopo aver creato la stored procedure, eseguire lo script seguente per il clustering.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Verificare che funzioni e che restituisca realmente l'elenco di clienti e i relativi mapping ai cluster.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>Usare le informazioni sul clustering

Poiché la stored procedure di clustering è stata archiviata nel database, può eseguire il clustering in modo efficiente sui dati dei clienti archiviati nello stesso database. È possibile eseguire la stored procedure ogni volta che i dati dei clienti vengono aggiornati e usare le informazioni sul clustering aggiornate.

Si supponga di voler inviare un messaggio di posta elettronica promozionale ai clienti inclusi nei cluster 3, ovvero il gruppo che contiene il comportamento di restituzione più attivo (per una descrizione dei quattro cluster, vedere la [seconda parte](clustering-model-build-tutorial.md#analyze-the-results)). Il codice seguente consente di selezionare gli indirizzi di posta elettronica dei clienti inclusi nel cluster 3.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

È possibile modificare il valore **r.cluster** in modo che restituisca gli indirizzi di posta elettronica per i clienti in altri cluster.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa esercitazione, è possibile eliminare il database tpcxbb_1gb dal server.

Seguire questa procedura nel portale di Azure:

1. Dal menu a sinistra nel portale di Azure selezionare **Tutte le risorse** o **Database SQL**.
1. Nel campo **Filtra per nome** immettere **tpcxbb_1gb** e selezionare la sottoscrizione.
1. Selezionare il database **tpcxbb_1gb**.
1. Nella pagina **Panoramica** selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Nella terza parte di questa serie di esercitazioni sono stati completati i passaggi seguenti:

* Creare una stored procedure che genera il modello
* Eseguire il clustering nel database SQL
* Usare le informazioni sul clustering

Per altre informazioni su come usare R in Machine Learning Services nel database SQL di Azure (anteprima), vedere:

* [Esercitazione: Preparare i dati per eseguire il training di un modello predittivo in R con Machine Learning Services di database SQL di Azure (anteprima)](predictive-model-prepare-data-tutorial.md)
* [Scrivere le funzioni R avanzate nel Database SQL di Azure con Machine Learning Services (anteprima)](machine-learning-services-functions.md)
* [Usare dati R e SQL in Machine Learning Services nel database SQL di Azure (anteprima)](machine-learning-services-data-issues.md)
* [Aggiungere un pacchetto R in Machine Learning Services nel database SQL di Azure (anteprima)](machine-learning-services-add-r-packages.md)
