---
title: Integrare Analisi di flusso di Azure con Azure Machine Learning
description: Questo articolo descrive come integrare un processo di Analisi di flusso di Azure con i modelli di Azure Machine Learning.This article describes how to integrate an Azure Stream Analytics job with Azure Machine Learning models.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 07fa72f086b676723279ee4b8efd927beb2692f0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481978"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integrare Analisi di flusso di Azure con Azure Machine Learning (anteprima)Integrate Azure Stream Analytics with Azure Machine Learning (Preview)

È possibile implementare modelli di apprendimento automatico come funzione definita dall'utente (UDF) nei processi di Analisi di flusso di Azure per eseguire il punteggio e le stime in tempo reale sui dati di input di streaming. [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) consente di usare qualsiasi strumento open source popolare, ad esempio Tensorflow, scikit-learn o PyTorch, per preparare, eseguire il training e distribuire i modelli.

> [!NOTE]
> Questa funzionalità è in anteprima pubblica. È possibile accedere a questa funzionalità nel portale di Azure solo utilizzando il collegamento Anteprima portale di [Analisi di flusso.](https://aka.ms/asaportalpreview) Questa funzionalità è disponibile anche nella versione più recente degli strumenti di Analisi di [flusso per Visual Studio.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi seguenti prima di aggiungere un modello di Machine Learning come funzione al processo di Analisi di flusso:Complete the following steps before you add a machine learning model as a function to your Stream Analytics job:

1. Usare Azure Machine Learning per [distribuire il modello come servizio Web.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)

2. Lo script di assegnazione del punteggio deve avere [input e output di esempio](../machine-learning/how-to-deploy-and-where.md#example-entry-script) usati da Azure Machine Learning per generare una specifica dello schema. Analisi di flusso usa lo schema per comprendere la firma della funzione del servizio Web.

3. Assicurarsi che il servizio Web accetti e restituisca i dati serializzati JSON.

4. Distribuire il modello nel [servizio Azure Kubernetes](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) per distribuzioni di produzione su larga scala. Se il servizio Web non è in grado di gestire il numero di richieste provenienti dal processo, le prestazioni del processo di Analisi di flusso diminuiranno, con un impatto sulla latenza. I modelli distribuiti nelle istanze del contenitore di Azure non sono attualmente supportati, ma saranno disponibili nei prossimi mesi.

## <a name="add-a-machine-learning-model-to-your-job"></a>Aggiungere un modello di apprendimento automatico al processoAdd a machine learning model to your job

È possibile aggiungere funzioni di Azure Machine Learning al processo di Analisi di flusso direttamente dal portale di Azure.You can add Azure Machine Learning functions to your Stream Analytics job directly from the Azure portal.

1. Passare al processo Analisi di flusso nel portale di Azure e selezionare **Funzioni** in **Topologia processo**. Selezionare quindi Servizio Azure ML dal menu a discesa Aggiungi.Then, select **Azure ML Service** from the **.Add** dropdown menu.

   ![Aggiungere Azure ML UDFAdd Azure ML UDF](./media/machine-learning-udf/add-azureml-udf.png)

2. Compilare il modulo **della funzione Servizio Di Azure Machine Learning** con i valori delle proprietà seguenti:

   ![Configurare Azure ML UDFConfigure Azure ML UDF](./media/machine-learning-udf/configure-azureml-udf.png)

Nella tabella seguente viene descritta ogni proprietà delle funzioni del servizio Azure ML in Analisi di flusso.

|Proprietà|Descrizione|
|--------|-----------|
|Alias di funzione|Immettere un nome per richiamare la funzione nella query.|
|Subscription|La sottoscrizione di Azure..|
|Area di lavoro di Azure MLAzure ML workspace|Area di lavoro di Azure Machine Learning usata per distribuire il modello come servizio Web.The Azure Machine Learning workspace you used to deploy your model as a web service.|
|Deployments|Servizio Web che ospita il modello.|
|Firma funzione|La firma del servizio Web dedotta dalla specifica dello schema dell'API. Se la firma non viene caricata, verificare di aver fornito input e output di esempio nello script di assegnazione dei punteggi per generare automaticamente lo schema.|
|Numero di richieste parallele per partizione|Si tratta di una configurazione avanzata per ottimizzare la velocità effettiva su larga scala. Questo numero rappresenta le richieste simultanee inviate da ogni partizione del processo al servizio Web. I processi con sei unità di streaming (SU) e inferiori hanno una partizione. I processi con 12 di gestione di dominio dispongono di due partizioni, 18 di ssu hanno tre partizioni e così via.<br><br> Ad esempio, se il processo dispone di due partizioni e si imposta questo parametro su quattro, saranno presenti otto richieste simultanee dal processo al servizio Web. In questo momento di anteprima pubblica, questo valore predefinito è 20 e non può essere aggiornato.|
|Numero massimo di batch|Si tratta di una configurazione avanzata per l'ottimizzazione della velocità effettiva su larga scala. Questo numero rappresenta il numero massimo di eventi da raggruppare in un unico messaggio in una singola richiesta inviata al servizio Web.|

## <a name="supported-input-parameters"></a>Parametri di input supportati

Quando la query di Analisi di flusso richiama una funzione definita dall'utente di Azure Machine Learning, il processo crea una richiesta serializzata JSON al servizio Web.When your Stream Analytics query invokes an Azure Machine Learning UDF, the job creates a JSON serialized request to the web service. La richiesta si basa su uno schema specifico del modello. È necessario fornire un input e un output di esempio nello script di assegnazione dei punteggi per [generare automaticamente uno schema.](../machine-learning/how-to-deploy-and-where.md) Lo schema consente ad Analisi di flusso di costruire la richiesta serializzata JSON per uno dei tipi di dati supportati, ad esempio numpy, pandas e PySpark. Più eventi di input possono essere raggruppati in un'unica richiesta.

La query di Analisi di flusso seguente è un esempio di come richiamare una funzione definita dall'utente di Azure Machine Learning:The following Stream Analytics query is an example of how to invoke an Azure Machine Learning UDF:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Analisi di flusso supporta solo il passaggio di un parametro per le funzioni di Azure Machine Learning.Stream Analytics only supports passing one parameter for Azure Machine Learning functions. Potrebbe essere necessario preparare i dati prima di passarne come input alla funzione definita dall'utente di apprendimento automatico.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Passare più parametri di input alla funzione definita dall'utente

Esempi più comuni di input per i modelli di apprendimento automatico sono le matrici numpy e i frame di dati. È possibile creare una matrice usando una funzione definita dall'utente JavaScript e creare un frame di dati serializzato JSON usando la `WITH` clausola.

### <a name="create-an-input-array"></a>Creare un array di inputCreate an input array

È possibile creare una funzione definita dall'utente JavaScript che accetta N numero di input e crea una matrice che può essere usata come input per la funzione definita dall'utente di Azure Machine Learning.You can create a JavaScript UDF which accepts *N* number of inputs and creates an array that can be used as input to your Azure Machine Learning UDF.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Dopo aver aggiunto la funzione definita dall'utente JavaScript al processo, è possibile richiamare la funzione definita dall'utente di Azure Machine Learning usando la query seguente:Once you have added the JavaScript UDF to your job, you can invoke your Azure Machine Learning UDF using the following query:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

Il codice JSON seguente è una richiesta di esempio:The following JSON is an example request:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Creare un frame di dati Panda o PySparkCreate a Pandas or PySpark DataFrame

È possibile `WITH` usare la clausola per creare un frame di dati serializzato JSON che può essere passato come input alla funzione definita dall'utente di Azure Machine Learning, come illustrato di seguito.

The following query creates a DataFrame by selecting the necessary fields and uses the DataFrame as input to the Azure Machine Learning UDF.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM input
```

Il codice JSON seguente è una richiesta di esempio dalla query precedente:The following JSON is an example request from the previous query:

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Ottimizzare le prestazioni per le funzioni definite dall'utente di Azure Machine LearningOptimize the performance for Azure Machine Learning UDFs

Quando si distribuisce il modello nel servizio Azure Kubernetes, è possibile [profilare il modello per determinare l'utilizzo delle risorse.](../machine-learning/how-to-deploy-and-where.md#profilemodel) È anche possibile [abilitare App Insights per le distribuzioni](../machine-learning/how-to-enable-app-insights.md) per comprendere la frequenza delle richieste, i tempi di risposta e la frequenza degli errori.

Se si dispone di uno scenario con velocità effettiva di eventi elevata, potrebbe essere necessario modificare i parametri seguenti in Analisi di flusso per ottenere prestazioni ottimali con latenze end-to-end basse:If you have a scenario with high event throughput, you may need to change the following parameters in Stream Analytics to achieve optimal performance with low end-to-end latenees:

1. Numero massimo di batch.
2. Numero di richieste parallele per partizione.

### <a name="determine-the-right-batch-size"></a>Determinare la dimensione del batch corretta

Dopo aver distribuito il servizio Web, si invia una richiesta di esempio con dimensioni batch variabili a partire da 50 e aumentandola in ordine di centinaia. Ad esempio, 200, 500, 1000, 2000 e così via. Si noterà che dopo una determinata dimensione del batch, la latenza della risposta aumenta. Il punto dopo il quale aumenta la latenza della risposta deve essere il numero massimo di batch per il processo.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Determinare il numero di richieste parallele per partizioneDetermine the number of parallel requests per partition

Con una scalabilità ottimale, il processo di Analisi di flusso deve essere in grado di inviare più richieste parallele al servizio Web e ottenere una risposta entro pochi millisecondi. La latenza della risposta del servizio Web può influire direttamente sulla latenza e sulle prestazioni del processo di Analisi di flusso. Se la chiamata dal processo al servizio Web richiede molto tempo, è probabile che si verifichi un aumento del ritardo della filigrana e potrebbe verificarsi anche un aumento del numero di eventi di input con backlog.

Per evitare tale latenza, verificare che sia stato eseguito il provisioning del cluster di servizio Azure Kubernetes (AKS) con il [numero corretto di nodi e repliche.](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli) È fondamentale che il servizio Web sia altamente disponibile e restituisca risposte riuscite. Se il processo riceve una risposta non disponibile del servizio (503) dal servizio Web, verrà continuamente riprovare con il back off esponenziale. Qualsiasi risposta diversa dall'esito positivo (200) e il servizio non disponibile (503) causeranno lo stato di errore del processo.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: funzioni JavaScript definite dall'utente per l'Analisi di flusso di Azure](stream-analytics-javascript-user-defined-functions.md)
* [Ridimensionare il processo di Analisi di flusso con la funzione Azure Machine Learning Studio (classica)Scale your Stream Analytics job with Azure Machine Learning Studio (classic) function](stream-analytics-scale-with-machine-learning-functions.md)

