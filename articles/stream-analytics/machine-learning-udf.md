---
title: Integrare analisi di flusso di Azure con Azure Machine Learning
description: Questo articolo descrive come integrare un processo di analisi di flusso di Azure con modelli di Azure Machine Learning.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 07fa72f086b676723279ee4b8efd927beb2692f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81481978"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integrare analisi di flusso di Azure con Azure Machine Learning (anteprima)

È possibile implementare i modelli di apprendimento automatico come funzione definita dall'utente nei processi di analisi di flusso di Azure per eseguire punteggi e stime in tempo reale sui dati di input di streaming. [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) consente di usare qualsiasi strumento open source noto, ad esempio Tensorflow, Scikit-learn o PyTorch, per preparare, eseguire il training e distribuire i modelli.

> [!NOTE]
> Questa funzionalità è in anteprima pubblica. È possibile accedere a questa funzionalità nel portale di Azure solo usando il [collegamento di anteprima del portale di analisi di flusso](https://aka.ms/asaportalpreview). Questa funzionalità è disponibile anche nella versione più recente di [strumenti di analisi di flusso per Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi seguenti prima di aggiungere un modello di machine learning come funzione al processo di analisi di flusso:

1. Usare Azure Machine Learning per [distribuire il modello come servizio Web](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

2. Lo script di assegnazione dei punteggi deve disporre di [input e output di esempio](../machine-learning/how-to-deploy-and-where.md#example-entry-script) usati da Azure Machine Learning per generare una specifica dello schema. Analisi di flusso usa lo schema per comprendere la firma della funzione del servizio Web.

3. Verificare che il servizio Web accetti e restituisca dati serializzati JSON.

4. Distribuire il modello nel [servizio Azure Kubernetes](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) per le distribuzioni di produzione su larga scala. Se il servizio Web non è in grado di gestire il numero di richieste provenienti dal processo, le prestazioni del processo di analisi di flusso saranno degradate, il che influisca sulla latenza. I modelli distribuiti nelle istanze di contenitore di Azure non sono attualmente supportati ma diventeranno disponibili nei prossimi mesi.

## <a name="add-a-machine-learning-model-to-your-job"></a>Aggiungere un modello di Machine Learning al processo

È possibile aggiungere Azure Machine Learning funzioni al processo di analisi di flusso direttamente dall'portale di Azure.

1. Passare al processo di analisi di flusso nel portale di Azure e selezionare **funzioni** in **topologia processo**. Quindi, selezionare **servizio Azure ml** dal menu a discesa **+ Aggiungi** .

   ![Aggiungere la funzione definita dall'utente di Azure ML](./media/machine-learning-udf/add-azureml-udf.png)

2. Compilare il form della **funzione del servizio Azure Machine Learning** con i valori di proprietà seguenti:

   ![Configurare la funzione definita dall'utente di Azure ML](./media/machine-learning-udf/configure-azureml-udf.png)

La tabella seguente descrive ogni proprietà delle funzioni del servizio Azure ML in analisi di flusso.

|Proprietà|Descrizione|
|--------|-----------|
|Alias di funzione|Immettere un nome per richiamare la funzione nella query.|
|Subscription|Sottoscrizione di Azure.|
|Area di lavoro di Azure ML|L'area di lavoro Azure Machine Learning utilizzata per distribuire il modello come servizio Web.|
|Deployments|Servizio Web che ospita il modello.|
|Firma funzione|Firma del servizio Web dedotta dalla specifica dello schema dell'API. Se non è possibile caricare la firma, verificare di aver specificato l'input e l'output di esempio nello script di assegnazione dei punteggi per generare automaticamente lo schema.|
|Numero di richieste parallele per partizione|Si tratta di una configurazione avanzata per ottimizzare la velocità effettiva a scalabilità elevata. Questo numero rappresenta le richieste simultanee inviate da ogni partizione del processo al servizio Web. I processi con sei unità di streaming (SU) e inferiori hanno una partizione. I processi con 12 unità di streaming hanno due partizioni, 18 unità di streaming hanno tre partizioni e così via.<br><br> Se, ad esempio, il processo dispone di due partizioni e si imposta questo parametro su quattro, saranno presenti otto richieste simultanee dal processo al servizio Web. Al momento dell'anteprima pubblica, il valore predefinito è 20 e non può essere aggiornato.|
|Numero massimo di batch|Si tratta di una configurazione avanzata per l'ottimizzazione della velocità effettiva su larga scala. Questo numero rappresenta il numero massimo di eventi che devono essere raggruppati in una singola richiesta inviata al servizio Web.|

## <a name="supported-input-parameters"></a>Parametri di input supportati

Quando la query di analisi di flusso richiama un Azure Machine Learning UDF, il processo crea una richiesta serializzata JSON al servizio Web. La richiesta è basata su uno schema specifico del modello. Per [generare automaticamente uno schema](../machine-learning/how-to-deploy-and-where.md), è necessario fornire un input e un output di esempio nello script di assegnazione dei punteggi. Lo schema consente a analisi di flusso di costruire la richiesta serializzata JSON per qualsiasi tipo di dati supportato, ad esempio NumPy, Pandas e PySpark. È possibile raggruppare più eventi di input in un'unica richiesta.

La query di analisi di flusso seguente è un esempio di come richiamare un Azure Machine Learning UDF:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Analisi di flusso supporta solo il passaggio di un parametro per le funzioni Azure Machine Learning. Potrebbe essere necessario preparare i dati prima di passarli come input per la funzione definita dall'utente di machine learning.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Passare più parametri di input alla funzione definita dall'utente

Esempi più comuni di input per i modelli di apprendimento automatico sono matrici numpy e dataframe. È possibile creare una matrice usando una funzione definita dall'utente JavaScript e creare un dataframe serializzato in JSON `WITH` usando la clausola.

### <a name="create-an-input-array"></a>Creare una matrice di input

È possibile creare una funzione definita dall'utente JavaScript che accetta *N* numero di input e crea una matrice che può essere usata come input per la funzione definita dall'utente Azure Machine Learning.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Dopo aver aggiunto la funzione definita dall'utente di JavaScript al processo, è possibile richiamare la Azure Machine Learning UDF usando la query seguente:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

Il codice JSON seguente è una richiesta di esempio:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Creare un dataframe Pandas o PySpark

È possibile usare la `WITH` clausola per creare un dataframe serializzato JSON che può essere passato come input alla funzione definita dall'utente Azure machine learning come illustrato di seguito.

La query seguente crea un frame di dati selezionando i campi necessari e usa il frame di dati come input per la funzione definita dall'utente Azure Machine Learning.

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

Il codice JSON seguente è una richiesta di esempio della query precedente:

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

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Ottimizzare le prestazioni per Azure Machine Learning UDF

Quando si distribuisce il modello nel servizio Azure Kubernetes, è possibile [profilare il modello per determinare l'utilizzo delle risorse](../machine-learning/how-to-deploy-and-where.md#profilemodel). È anche possibile [abilitare App Insights per le distribuzioni](../machine-learning/how-to-enable-app-insights.md) per comprendere la frequenza delle richieste, i tempi di risposta e le percentuali di errore.

Se si dispone di uno scenario con velocità effettiva elevata, potrebbe essere necessario modificare i parametri seguenti in analisi di flusso per ottenere prestazioni ottimali con latenze end-to-end minime:

1. Numero massimo batch.
2. Numero di richieste parallele per partizione.

### <a name="determine-the-right-batch-size"></a>Determinare le dimensioni del batch corrette

Dopo aver distribuito il servizio Web, è possibile inviare una richiesta di esempio con diverse dimensioni di batch a partire da 50 e aumentando l'ordine di centinaia. Ad esempio, 200, 500, 1000, 2000 e così via. Si noterà che dopo una determinata dimensione del batch, aumenta la latenza della risposta. Il punto dopo il quale la latenza di risposta aumenta dovrebbe essere il numero massimo di batch per il processo.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Determinare il numero di richieste parallele per partizione

Con la scalabilità ottimale, il processo di analisi di flusso dovrebbe essere in grado di inviare più richieste parallele al servizio Web e ottenere una risposta entro pochi millisecondi. La latenza della risposta del servizio Web può influito direttamente sulla latenza e sulle prestazioni del processo di analisi di flusso. Se la chiamata dal processo al servizio Web richiede molto tempo, è probabile che si verifichi un aumento del ritardo della filigrana e che venga visualizzato anche un aumento del numero di eventi di input con backlog.

Per evitare tale latenza, assicurarsi che sia stato effettuato il provisioning del cluster del servizio Kubernetes di Azure (AKS) con il [numero corretto di nodi e repliche](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). È fondamentale che il servizio Web sia a disponibilità elevata e che restituisca risposte con esito positivo. Se il processo riceve una risposta del servizio non disponibile (503) dal servizio Web, verrà ripetutamente ritentata con il backup esponenziale. Qualsiasi risposta diversa da Success (200) e servizio non disponibile (503) provocherà il passaggio del processo a uno stato di errore.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: funzioni JavaScript definite dall'utente per l'Analisi di flusso di Azure](stream-analytics-javascript-user-defined-functions.md)
* [Ridimensionare il processo di analisi di flusso con la funzione Azure Machine Learning Studio (classica)](stream-analytics-scale-with-machine-learning-functions.md)

