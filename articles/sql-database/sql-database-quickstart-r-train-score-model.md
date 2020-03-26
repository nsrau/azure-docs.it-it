---
title: Creare ed eseguire il training di un modello predittivo in R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Creare un semplice modello predittivo in R con Machine Learning Services di database SQL di Azure (anteprima), quindi eseguire stime dei risultati usando i nuovi dati.
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
ms.openlocfilehash: a54d418f668d8c7292c8332c1b14c4df45e59308
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768455"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Guida introduttiva: Creare ed eseguire il training di un modello predittivo in R con Machine Learning Services di database SQL di Azure (anteprima)

In questo argomento di avvio rapido si userà R per creare un modello predittivo, che verrà sottoposto a training, quindi verrà salvato in una tabella del database e infine verrà usato per prevedere i valori dai nuovi dati tramite Machine Learning Services (con R) in Database SQL di Azure.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Un [database SQL di Azure](sql-database-single-database-get-started.md) con una [regola del firewall a livello di server](sql-database-server-level-firewall-rule.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) con R abilitato. [Iscriversi per l'anteprima](sql-database-machine-learning-services-overview.md#signup).
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!NOTE]
> Durante l'anteprima pubblica, Microsoft eseguirà l'onboarding e l'abilitazione dell'apprendimento automatico per il database nuovo o esistente.

Questo esempio usa un semplice modello di regressione per prevedere la distanza di arresto di un'automobile in base alla velocità usando il set di dati **cars** incluso in R.

> [!TIP]
> Il runtime di R include molti set di dati. Per ottenere un elenco di quelli installati, digitare `library(help="datasets")` al prompt dei comandi di R.

## <a name="create-and-train-a-predictive-model"></a>Creare ed eseguire il training di un modello predittivo

I dati relativi alla velocità delle automobili nel set di dati **cars** contiene due colonne, entrambe numeriche: **dist** e **speed**. I dati includono più osservazioni di arresto a velocità diverse. Da questi dati, si creerà un modello di regressione lineare che descrive la relazione tra la velocità dell'automobile e la distanza necessaria per l'arresto.

I requisiti di un modello lineare sono semplici:
- Definire una formula che descrive la relazione tra la variabile dipendente *speed* e la variabile indipendente *distance*.
- Fornire i dati di input da usare per eseguire il training del modello.

> [!TIP]
> Se è necessario rivedere le informazioni sui modelli lineari, vedere questa esercitazione che descrive il processo di adattamento di un modello tramite rxLinMod: [Fitting Linear Models](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model) (Adattamento di modelli lineari)

Nei passaggi seguenti verranno impostati i dati di training, creato un modello di regressione, eseguito il training del modello usando i dati di training, quindi il modello verrà salvato in una tabella SQL.

1. Aprire **SQL Server Management Studio** e connettersi al database SQL.

   Per assistenza con la connessione, vedere [Avvio rapido: Usare SQL Server Management Studio per connettersi a un database SQL di Azure ed eseguire query](sql-database-connect-query-ssms.md).

1. Creare una tabella **CarSpeed** in cui salvare i dati di training.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. Creare un modello di regressione usando `rxLinMod`. 

   Per compilare il modello, definire la formula all'interno del codice R e passare i dati di training **CarSpeed** come parametro di input.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     Il primo argomento per rxLinMod è il parametro *formula*, che definisce la distanza come dipendente dalla velocità. I dati di input vengono archiviati nella variabile `CarsData`, che viene popolata dalla query SQL.

1. Creare una tabella in cui archiviare il modello in modo che sia possibile usarlo successivamente per la stima. 

   L'output di un pacchetto R che crea un modello è in genere un **oggetto binario**, pertanto la tabella deve avere una colonna di tipo **VARBINARY(max)** .

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. A questo punto richiamare la stored procedure, generare il modello e salvarlo in una tabella.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Si noti che, se si esegue questo codice una seconda volta, viene visualizzato questo errore:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Un modo per evitare questo errore consiste nell'aggiornare il nome per ogni nuovo modello. Ad esempio, si può modificare il nome scegliendone uno più descrittivo e includere il tipo di modello, il giorno di creazione e così via.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Visualizzare la tabella dei coefficienti

In generale, l'output di R dalla stored procedure [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) è limitato a un singolo frame di dati. Tuttavia, oltre al frame di dati è possibile restituire output di altri tipi, ad esempio valori scalari.

Si supponga ad esempio di voler eseguire il training di un modello ma di voler visualizzare immediatamente la tabella dei coefficienti dal modello. È possibile creare la tabella dei coefficienti come set di risultati principale e restituire il modello con training in una variabile SQL. È possibile riutilizzare immediatamente il modello richiamando la variabile oppure salvarlo in una tabella come illustrato di seguito.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**Risultati**

![Modello con training con output aggiuntivo](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Assegnare punteggi ai nuovi dati usando il modello sottoposto a training

L'*assegnazione dei punteggi* è un concetto di data science che indica la generazione di stime, probabilità o altri valori in base ai nuovi dati inseriti in un modello sottoposto a training. Si userà il modello creato nella sezione precedente per assegnare punteggi alle stime in base ai nuovi dati.

Si noti che i dati di training originali terminano in corrispondenza di una velocità pari a 25 miglia orarie. Infatti, i dati originali sono basati su un esperimento del 1920. Ci si potrebbe chiedere quanto tempo è necessario a un'automobile del 1920 per arrestarsi, presupponendo che possa arrivare a 60 o persino 100 miglia all'ora. Per rispondere a questa domanda è possibile fornire alcuni nuovi valori di velocità al modello.

1. Creare una tabella con i nuovi dati di velocità.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. Stimare la distanza di arresto in base a questi nuovi valori di velocità.

   Poiché il modello è basato sull'algoritmo **rxLinMod** fornito come parte del pacchetto **RevoScaleR**, si chiama la funzione [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) anziché la funzione generica `predict` di R.

   Questo script di esempio:
   - Usa un'istruzione SELECT per ottenere un singolo modello dalla tabella
   - Lo passa come parametro di input
   - Chiama la funzione `unserialize` sul modello
   - Applica la funzione `rxPredict` con gli argomenti appropriati al modello
   - Fornisce i nuovi dati di input

   > [!TIP]
   > Per l'assegnazione dei punteggi in tempo reale, vedere [Serialization functions](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) (Funzioni di serializzazione) fornite da RevoScaleR.

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **Risultati**

   ![Set di risultati per la stima della distanza di arresto](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> In questo script di esempio, la funzione `str` viene aggiunta durante la fase di test per verificare lo schema dei dati restituiti da R. È possibile rimuovere l'istruzione in un secondo momento.
>
> I nomi di colonna usati nello script R non vengono necessariamente passati all'output della stored procedure. Qui la clausola WITH RESULTS definisca alcuni nuovi nomi di colonna.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Machine Learning Services con R nel database SQL di Azure (anteprima), vedere gli articoli seguenti.

- [Machine Learning Services con R nel database SQL di Azure (anteprima)](sql-database-machine-learning-services-overview.md)
- [Create and run simple R scripts in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md) Creare ed eseguire script R semplici nei Machine Learning Services nel database SQL di Azure (anteprima)
- [Scrivere le funzioni R avanzate nel Database SQL di Azure con Machine Learning Services (anteprima)](sql-database-machine-learning-services-functions.md)
- [Usare dati R e SQL in Machine Learning Services nel database SQL di Azure (anteprima)](sql-database-machine-learning-services-data-issues.md)
