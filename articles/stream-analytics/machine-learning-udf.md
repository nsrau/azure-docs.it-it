---
title: Integrare Analisi di flusso di Azure con Azure Machine Learning
description: Questo articolo descrive come integrare un processo di Analisi di flusso di Azure con i modelli di Azure Machine Learning.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: b138d93b400c16837c250ede1e264b54a851327c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84488750"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integrare Analisi di flusso di Azure con Azure Machine Learning (anteprima)

È possibile implementare i modelli di Machine Learning come funzione definita dall'utente (UDF, User-Defined Function) nei processi di Analisi di flusso di Azure per eseguire punteggi e stime in tempo reale sui dati di input del flusso. [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) consente di usare gli strumenti open source più comuni, ad esempio Tensorflow, scikit-learn o PyTorch, per preparare, eseguire il training e distribuire i modelli.

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi seguenti prima di aggiungere un modello di Machine Learning come funzione al processo di Analisi di flusso di Azure:

1. Usare Azure Machine Learning per [distribuire il modello come servizio Web](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

2. Lo script di assegnazione dei punteggi deve contenere [input e output di esempio](../machine-learning/how-to-deploy-and-where.md#example-entry-script) che vengono usati da Azure Machine Learning per generare una specifica relativa allo schema. Analisi di flusso di Azure usa lo schema per comprendere la firma della funzione del servizio Web. Questa [definizione di spavalderia di esempio](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/AzureML/swagger-example.json) può essere usata come riferimento per assicurarsi che sia stata configurata correttamente.

3. Assicurarsi che il servizio Web accetti e restituisca i dati serializzati JSON.

4. Distribuire il modello nel [servizio Azure Kubernetes](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) per distribuzioni di produzione su larga scala. Se il servizio Web non è in grado di gestire il numero di richieste provenienti dal processo, le prestazioni del processo di Analisi di flusso di Azure risulteranno compromesse, con conseguenze per la latenza. I modelli distribuiti nelle istanze di Azure Container sono supportati solo se si usa il portale di Azure.

## <a name="add-a-machine-learning-model-to-your-job"></a>Aggiungere un modello di apprendimento automatico al processo

È possibile aggiungere le funzioni di Azure Machine Learning al processo di Analisi di flusso di Azure direttamente dal portale di Azure.

1. Passare al processo di Analisi di flusso di Azure nel portale di Azure e selezionare **Funzioni** in **Topologia processo**. Selezionare quindi **Servizio Azure ML** dal menu a discesa **+ Aggiungi**.

   ![Aggiungere la funzione definita dall'utente di Azure ML](./media/machine-learning-udf/add-azureml-udf.png)

2. Compilare il modulo della **funzione del servizio Azure Machine Learning** immettendo i valori di proprietà seguenti:

   ![Configurare la funzione definita dall'utente di Azure ML](./media/machine-learning-udf/configure-azureml-udf.png)

La tabella seguente descrive le proprietà delle funzioni del servizio Azure ML in Analisi di flusso di Azure.

|Proprietà|Descrizione|
|--------|-----------|
|Alias di funzione|Immettere un nome per richiamare la funzione nella query.|
|Subscription|La sottoscrizione di Azure.|
|Area di lavoro di Azure ML|L'area di lavoro di Azure Machine Learning usata per distribuire il modello come servizio Web.|
|Deployments|Il servizio Web che ospita il modello.|
|Firma della funzione|La firma del servizio Web dedotta dalla specifica dello schema dell'API. Se non è possibile caricare la firma, verificare di aver specificato l'input e l'output di esempio nello script di assegnazione dei punteggi per la generazione automatica dello schema.|
|Numero di richieste parallele per partizione|Si tratta di una configurazione avanzata per ottimizzare la velocità effettiva a scalabilità elevata. Questo numero rappresenta le richieste simultanee inviate da ogni partizione del processo al servizio Web. I processi con sei unità di streaming (SU, Streaming Unit) o meno hanno una partizione. I processi con 12 unità di streaming hanno due partizioni, quelli con 18 unità di streaming ne hanno tre e così via.<br><br> Se, ad esempio, il processo ha due partizioni e si imposta questo parametro sul valore quattro, saranno presenti otto richieste simultanee dal processo al servizio Web. In questa fase dell'anteprima pubblica, il valore predefinito è 20 e non può essere aggiornato.|
|Numero massimo di batch|Si tratta di una configurazione avanzata per l'ottimizzazione della velocità effettiva a scalabilità elevata. Questo numero rappresenta il numero massimo di eventi raggruppati in una singola richiesta inviata al servizio Web.|

## <a name="supported-input-parameters"></a>Parametri di input supportati

Quando la query di Analisi di flusso di Azure richiama una funzione definita dall'utente di Azure Machine Learning, il processo crea una richiesta serializzata JSON per il servizio Web. La richiesta è basata su uno schema specifico del modello. È necessario specificare un input e un output di esempio nello script di assegnazione dei punteggi per [generare automaticamente uno schema](../machine-learning/how-to-deploy-and-where.md). Lo schema consente ad Analisi di flusso di Azure di costruire la richiesta serializzata JSON per qualsiasi tipo di dati supportato, ad esempio NumPy, pandas e PySpark. È possibile raggruppare più eventi di input in un'unica richiesta.

La query di Analisi di flusso di Azure seguente rappresenta un esempio di come richiamare una funzione definita dall'utente di Azure Machine Learning:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Analisi di flusso di Azure supporta solo il passaggio di un parametro per le funzioni di Azure Machine Learning. Potrebbe essere necessario preparare i dati prima di passarli come input alla funzione definita dall'utente di Machine Learning.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Passare più parametri di input alla funzione definita dall'utente

Gli esempi di input ai modelli di Machine Learning più comuni sono le matrici NumPy e DataFrame. È possibile creare una matrice usando una funzione definita dall'utente JavaScript e creare un DataFrame serializzato JSON usando la clausola `WITH`.

### <a name="create-an-input-array"></a>Creare una matrice di input

È possibile creare una funzione definita dall'utente JavaScript in grado di accettare un numero *N* di input e di creare una matrice che può essere usata come input per la funzione definita dall'utente di Azure Machine Learning.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Dopo aver aggiunto la funzione definita dall'utente JavaScript al processo, è possibile richiamare la funzione definita dall'utente di Azure Machine Learning usando la query seguente:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

Di seguito è riportata una richiesta JSON di esempio:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Creare un DataFrame Pandas o PySpark

È possibile usare la clausola `WITH` per creare un DataFrame serializzato JSON che può essere passato come input alla funzione definita dall'utente di Azure Machine Learning, come illustrato di seguito.

La query seguente crea un DataFrame selezionando i campi necessari e lo usa come input per la funzione definita dall'utente di Azure Machine Learning.

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

Di seguito è riportata una richiesta JSON di esempio dalla query precedente:

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

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Ottimizzare le prestazioni delle funzioni definite dall'utente di Azure Machine Learning

Quando si distribuisce il modello nel servizio Azure Kubernetes, è possibile [profilare il modello per determinare l'utilizzo delle risorse](../machine-learning/how-to-deploy-and-where.md#profilemodel). È anche possibile [abilitare App Insights per le distribuzioni](../machine-learning/how-to-enable-app-insights.md) al fine di comprendere la frequenza delle richieste, i tempi di risposta e le percentuali di errore.

In uno scenario con velocità effettiva degli eventi elevata, potrebbe essere necessario modificare i parametri seguenti in Analisi di flusso di Azure per ottenere prestazioni ottimali con latenze end-to-end minime:

1. Numero massimo di batch.
2. Numero di richieste parallele per partizione.

### <a name="determine-the-right-batch-size"></a>Determinare le dimensioni corrette della batch

Dopo aver distribuito il servizio Web, inviare la richiesta di esempio con diverse dimensioni di batch partendo da 50 con incrementi su base cento. Ad esempio, 200, 500, 1000, 2000 e così via. Si noterà che dopo una determinata dimensione di batch, aumenterà la latenza della risposta. Il punto dopo il quale la latenza della risposta aumenta dovrebbe corrispondere al numero massimo di batch del processo.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Determinare il numero di richieste parallele per partizione

In condizioni di scalabilità ottimale, il processo di Analisi di flusso di Azure dovrebbe essere in grado di inviare più richieste parallele al servizio Web e ottenere una risposta entro pochi millisecondi. La latenza della risposta del servizio Web può influire direttamente sulla latenza e sulle prestazioni del processo di Analisi di flusso di Azure. Se la chiamata dal processo al servizio Web impiega molto tempo, è probabile che si verifichi un aumento del ritardo limite e anche del numero di eventi di input con backlog.

Per evitare tale latenza, assicurarsi che il provisioning del cluster del servizio Azure Kubernetes (AKS) sia stato eseguito con il [numero corretto di nodi e repliche](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). È fondamentale che il servizio Web disponga di disponibilità elevata e restituisca risposte con esito positivo. Se il processo riceve una risposta di servizio non disponibile (503) dal servizio Web, continuerà a riprovare con back-off esponenziale. Qualsiasi risposta diversa da Operazione riuscita (200) e Servizio non disponibile (503) causerà lo stato di errore del processo.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Funzioni JavaScript definite dall'utente in Analisi di flusso di Azure](stream-analytics-javascript-user-defined-functions.md)
* [Ridimensionare il processo di Analisi di flusso di Azure con la funzione di Azure Machine Learning Studio (versione classica)](stream-analytics-scale-with-machine-learning-functions.md)

