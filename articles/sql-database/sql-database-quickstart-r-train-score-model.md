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
ms.openlocfilehash: 97309a24c0ab12720f968409856a16cab4ff7ac7
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013096"
---
# <a name="create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Creare ed eseguire il training di un modello predittivo in R con Machine Learning Services di database SQL di Azure (anteprima)

In questo avvio rapido verrà creato ed eseguito il training di un modello predittivo con R, si salverà il modello in una tabella nel database SQL, quindi si userà il modello per stimare i valori dei nuovi dati usando l'anteprima pubblica di [Machine Learning Services (con R) nel Database SQL di Azure ](sql-database-machine-learning-services-overview.md). 

Il modello usato in questo avvio rapido è un semplice modello di regressione che stima la distanza di arresto di un'automobile in base alla velocità. Si userà il set di dati **cars** incluso in R, perché è piccolo e facile da comprendere.

> [!TIP]
> Molti set di dati di piccole e grandi dimensioni sono inclusi con il runtime di R. Per ottenere un elenco dei set di dati installati con R, digitare `library(help="datasets")` da un prompt dei comandi di R.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/) prima di iniziare.

- Per eseguire il codice di esempio in questi esercizi, è necessario disporre di un database SQL di Azure con Machine Learning Services (con R) abilitato. Durante l'anteprima pubblica, Microsoft eseguirà l'onboarding e l'abilitazione dell'apprendimento automatico per il database nuovo o esistente. Seguire la procedura descritta in [Iscriversi per l'anteprima](sql-database-machine-learning-services-overview.md#signup).

- Assicurarsi di avere installato la versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). È possibile eseguire gli script R tramite altri strumenti di gestione di database o di query, ma in questo avvio rapido si userà SQL Server Management Studio.

- Questo avvio rapido richiede di configurare una regola del firewall a livello di server. Per informazioni su come eseguire questa operazione, vedere [Creare una regola del firewall a livello di server](sql-database-server-level-firewall-rule.md).

## <a name="create-and-train-a-predictive-model"></a>Creare ed eseguire il training di un modello predittivo

I dati relativi alla velocità delle automobili nel set di dati **cars** contiene due colonne, entrambe numeriche: **dist** e **speed**. I dati includono più osservazioni di arresto a velocità diverse. Da questi dati, si creerà un modello di regressione lineare che descrive la relazione tra la velocità dell'automobile e la distanza necessaria per l'arresto.

I requisiti di un modello lineare sono semplici:
- Definire una formula che descrive la relazione tra la variabile dipendente *speed* e la variabile indipendente *distance*.
- Fornire i dati di input da usare per eseguire il training del modello.

> [!TIP]
> Se occorre un ripasso sui modelli lineari, questa esercitazione descrive il processo di adattamento di un modello tramite rxLinMod: [Fitting Linear Models](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model) (Adattamento di modelli lineari)

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

   L'output di un pacchetto R che crea un modello è in genere un **oggetto binario**, pertanto la tabella deve avere una colonna di tipo **VARBINARY(max)**.

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

![Modello con training con output aggiuntivo](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Assegnare un punteggio ai nuovi dati usando il modello con training

*Assegnazione dei punteggi* è un termine usato nella data science per indicare la generazione di stime, probabilità o altri valori in base a nuovi dati inseriti in un modello con training. Il modello creato nella sezione precedente verrà usato per assegnare un punteggio alle stime in base a nuovi dati.

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

   ![Set di risultati per la stima della distanza di arresto](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> In questo script di esempio, la funzione `str` viene aggiunta durante la fase di test per verificare lo schema dei dati restituiti da R. È possibile rimuovere l'istruzione in un secondo momento.
>
> I nomi di colonna usati nello script R non vengono necessariamente passati all'output della stored procedure. Qui la clausola WITH RESULTS definisca alcuni nuovi nomi di colonna.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Machine Learning Services, vedere gli articoli seguenti. Anche se alcuni di questi articoli sono per SQL Server, la maggior parte delle informazioni è applicabile anche a Machine Learning Services (con R) nel database SQL di Azure.

- [Machine Learning Services (con R) nel database SQL di Azure](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Esercitazione: Informazioni sull'analisi nel database con R in SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [End-to-end data science walkthrough for R and SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough) (Procedura dettagliata di data science end-to-end per R e SQL Server)
- [Esercitazione: Usare funzioni R di RevoScaleR con dati di SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
