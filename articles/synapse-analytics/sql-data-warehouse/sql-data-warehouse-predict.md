---
title: Assegnare punteggi ai modelli di apprendimento automatico con PREDICT
description: Informazioni su come assegnare un punteggio ai modelli di apprendimento automatico usando la funzione T-SQL PREDICT nel pool SQL dedicato.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: anjangsh
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: ce77a169e28e21aa37be2a49997a58ee42c93807
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510829"
---
# <a name="score-machine-learning-models-with-predict"></a>Assegnare punteggi ai modelli di apprendimento automatico con PREDICT

Il pool SQL dedicato offre la possibilità di assegnare punteggi ai modelli di apprendimento automatico usando il linguaggio T-SQL familiare. Con la [stima](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)di T-SQL è possibile usare i modelli di machine learning esistenti con training con i dati cronologici e assegnare loro un punteggio entro i limiti di sicurezza del data warehouse. La funzione PREDICT accetta un modello [ONNX (Open Neural Network Exchange)](https://onnx.ai/) e i dati come input. Questa funzionalità Elimina la fase di trasferimento di dati importanti al di fuori dell'data warehouse per l'assegnazione dei punteggi. Mira a consentire ai professionisti dei dati di distribuire facilmente modelli di apprendimento automatico con l'interfaccia T-SQL familiare, nonché collaborare senza problemi con i data scientist che lavorano con il Framework appropriato per la propria attività.

> [!NOTE]
> Questa funzionalità non è attualmente supportata nel pool SQL senza server.

La funzionalità richiede che il modello venga sottoposto a training all'esterno di sinapsi SQL. Dopo aver compilato il modello, caricarlo nel data warehouse e assegnargli il punteggio con la sintassi di stima T-SQL per ottenere informazioni dettagliate dai dati.

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>Training del modello

Il pool SQL dedicato prevede un modello con training preliminare. Quando si esegue il training di un modello di apprendimento automatico utilizzato per eseguire stime in un pool SQL dedicato, tenere presenti i fattori seguenti.

- Il pool SQL dedicato supporta solo modelli di formato ONNX. ONNX è un formato di modello open source che consente di scambiare modelli tra diversi framework per consentire l'interoperabilità. È possibile convertire i modelli esistenti in formato ONNX usando Framework che lo supportano a livello nativo o che convertono i pacchetti disponibili. Ad esempio, il pacchetto [sklearn-Onnx](https://github.com/onnx/sklearn-onnx) converte i modelli Scikit-learn in Onnx. Il [repository GitHub ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format) fornisce un elenco di Framework e esempi supportati.

   Se si usa Machine Learning [automatiche](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) per il training, assicurarsi di impostare il parametro *enable_onnx_compatible_models* su true per produrre un modello di formato Onnx. [Automatic machine learning notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) Mostra un esempio di come usare AutoML per creare un modello di apprendimento automatico del formato ONNX.

- Per i dati di input sono supportati i tipi di dati seguenti:
    - int, bigint, Real, float
    - char, varchar, nvarchar

- I dati di assegnazione dei punteggi devono avere lo stesso formato dei dati di training. I tipi di dati complessi, ad esempio le matrici multidimensionali, non sono supportati da PREDICT. Per il training, quindi, assicurarsi che ogni input del modello corrisponda a una singola colonna della tabella di assegnazione dei punteggi anziché passare una singola matrice contenente tutti gli input.

- Verificare che i nomi e i tipi di dati degli input del modello corrispondano ai nomi di colonna e ai tipi di dati dei nuovi dati di stima. La visualizzazione di un modello ONNX con diversi strumenti open source disponibili online può ulteriormente aiutare il debug.

## <a name="loading-the-model"></a>Caricamento del modello

Il modello viene archiviato in una tabella utente del pool SQL dedicata come stringa esadecimale. Per identificare il modello, è possibile aggiungere altre colonne, ad esempio ID e descrizione, nella tabella del modello. Utilizzare varbinary (max) come tipo di dati della colonna del modello. Di seguito è riportato un esempio di codice per una tabella che può essere utilizzata per l'archiviazione dei modelli:

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

Una volta che il modello viene convertito in una stringa esadecimale e la definizione della tabella specificata, utilizzare il [comando copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) o la polibase per caricare il modello nella tabella del pool SQL dedicata. Nell'esempio di codice seguente viene utilizzato il comando Copy per caricare il modello.

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>Assegnazione di punteggi al modello

Una volta caricati i dati e il modello nel data warehouse, utilizzare la funzione di **stima T-SQL** per assegnare un punteggio al modello. Assicurarsi che i nuovi dati di input abbiano lo stesso formato dei dati di training utilizzati per la compilazione del modello. L'istruzione T-SQL PREDICT accetta due input: modello e nuovi dati di input di assegnazione dei punteggi e genera nuove colonne per l'output. Il modello può essere specificato come variabile, valore letterale o scalare sub_query. Utilizzare [con common_table_expression](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql?view=azure-sqldw-latest&preserve-view=true) per specificare un set di risultati denominato per il parametro data.

Nell'esempio seguente viene illustrata una query di esempio che utilizza la funzione di stima. Viene creata una colonna aggiuntiva con il *Punteggio* del nome e il tipo di dati *float* contenente i risultati della stima. Tutte le colonne di dati di input e le colonne di stima di output sono disponibili per la visualizzazione con l'istruzione SELECT. Per ulteriori informazioni, vedere [Predict (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true).

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d, RUNTIME = ONNX) WITH (Score float) AS p;
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla funzione PREDICT, vedere [Predict (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true).
