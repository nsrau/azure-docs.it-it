---
title: 'Esercitazione: Distribuire un modello predittivo in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Nella terza parte di questa esercitazione in tre parti, è possibile distribuire un modello predittivo in R con Machine Learning Services del database SQL di Azure (anteprima).
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
ms.openlocfilehash: 9fa816b2a8e736f03c99b66b898f48bd2a483b31
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596766"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Esercitazione: Distribuire un modello predittivo in R con Machine Learning Services del database SQL di Azure (anteprima)

Nella terza parte di questa esercitazione in tre parti verrà distribuito un modello predittivo, sviluppato in R, in un database SQL usando Machine Learning Services di Database SQL di Azure (anteprima).

Si creerà una stored procedure con uno script R incorporato che esegue le stime usando il modello. Poiché il modello viene eseguito nel database SQL di Azure, è possibile eseguire facilmente il training con i dati archiviati nel database.

In questo articolo, usando gli script R sviluppati nella prima e nella seconda parte, verrà descritto come:

> [!div class="checklist"]
> * Creare una stored procedure che genera il modello di Machine Learning
> * Memorizzare il modello in una tabella di database
> * Creare una stored procedure per le stime in base al modello
> * Eseguire il modello con nuovi dati

Nella [prima parte](sql-database-tutorial-predictive-model-prepare-data.md) si è appreso come importare un database di esempio e quindi preparare i dati da usare per il training di un modello predittivo in R.

Nella [seconda parte](sql-database-tutorial-predictive-model-build-compare.md) si è appreso come creare ed eseguire il training di più modelli di Machine Learning in R e quindi scegliere quello più accurato.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Per la terza parte di questa serie di esercitazioni è necessario aver completato la [**prima parte**](sql-database-tutorial-predictive-model-prepare-data.md) e la [**seconda parte**](sql-database-tutorial-predictive-model-build-compare.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Creare una stored procedure per la generazione del modello

Nella seconda parte di questa serie di esercitazioni, si è scelto come più accurato un albero delle decisioni (dtree). Adesso, usando gli script R sviluppati, creare una stored procedure (`generate_rental_rx_model`) per il training e la generazione del modello dtree usando rxDTree del pacchetto RevoScaleR.

Eseguire i seguenti comandi in Azure Data Studio o SSMS.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>Memorizzare il modello in una tabella di database

Creare una tabella nel database TutorialDB e quindi salvare il modello di tabella.

1. Creare una tabella (`rental_rx_models`) per memorizzare il modello.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. Salvare il modello di tabella come oggetto binario con il nome "rxDTree".

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Creare una stored procedure per le stime

Creare una stored procedure (`predict_rentalcount_new`) per le stime in base al modello di cui è stato eseguito il training e un set di nuovi dati.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>Eseguire il modello con nuovi dati

Ora è possibile utilizzare la stored procedure `predict_rentalcount_new` per stimare il numero di noleggio dai nuovi dati.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

Il risultato visualizzato sarà simile al seguente.

```results
RentalCount_Predicted
332.571428571429
```

È stato creato, eseguito il training e distribuito correttamente un modello nel database SQL di Azure. Questo modello è stato quindi usato in una stored procedure per stimare i valori in base ai nuovi dati.

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver usato il database Tutorial DB, eliminarlo dal server di database SQL di Azure.

Seguire questa procedura nel portale di Azure:

1. Dal menu a sinistra nel portale di Azure selezionare **Tutte le risorse** o **Database SQL**.
1. Nel campo **Filtra per nome...** immettere **TutorialDB** e selezionare la sottoscrizione.
1. Selezionare il proprio database TutorialDB.
1. Nella pagina **Panoramica** selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Nella seconda parte di questa serie di esercitazioni sono stati completati questi passaggi:

* Creare una stored procedure che genera il modello di Machine Learning
* Memorizzare il modello in una tabella di database
* Creare una stored procedure per le stime in base al modello
* Eseguire il modello con nuovi dati

Per altre informazioni su come usare R in Machine Learning Services nel database SQL di Azure (anteprima), vedere:

* [Scrivere le funzioni R avanzate nel Database SQL di Azure con Machine Learning Services (anteprima)](sql-database-machine-learning-services-functions.md)
* [Usare dati R e SQL in Machine Learning Services nel database SQL di Azure (anteprima)](sql-database-machine-learning-services-data-issues.md)
* [Aggiungere un pacchetto R in Machine Learning Services nel database SQL di Azure (anteprima)](sql-database-machine-learning-services-add-r-packages.md)
