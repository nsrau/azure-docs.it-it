---
title: "Come usare la funzionalità di Raccolta dati di Gestione modelli in Azure Machine Learning Workbench | Microsoft Docs"
description: "Questo documento indica come usare la funzionalità di Raccolta dati di Gestione modelli in Azure Machine Learning Workbench"
services: machine-learning
author: aashishb
ms.author: aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: e751e2f0ec812de43a03749e04ff165fa62ec649
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-collect-model-data-using-data-collection"></a>Come raccogliere i dati del modello tramite la raccolta dati

La funzionalità di raccolta dati del modello consente di archiviare gli input e le previsioni del modello da un servizio web di machine learning. Questo documento illustra gli aspetti seguenti della raccolta dati del modello:

- Come installare il pacchetto di raccolta dati
- Come usare la raccolta di dati
- Come visualizzare e usare i dati raccolti

## <a name="how-to-install-the-data-collection-package"></a>Come installare il pacchetto di raccolta dati
La libreria della raccolta dati può essere installata in modo nativo su Linux e Windows.

### <a name="windows"></a>Windows
Su Windows, è possibile installare il modulo dell'agente di raccolta dati con il comando seguente:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
Su Linux, è necessario installare prima la libreria libxml++. Eseguire il comando seguente, che deve essere rilasciato in sudo:

    sudo apt-get install libxml++2.6-2v5

Quindi eseguire il comando seguente:

    pip install azureml.datacollector
## <a name="how-to-use-data-collection"></a>Come usare la raccolta di dati

Per usare la raccolta dei dati del modello, è necessario apportare le modifiche seguenti al file di assegnazione dei punteggi:

1. Aggiungere il codice seguente all'inizio del file
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Aggiungere le righe di codice seguenti alla funzione `init()`,
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Aggiungere le righe di codice seguenti alla funzione `run(input_df)`,
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Assicurarsi che le variabili `input_df` e `pred` (valore di previsione da `model.predict()`) vengono inizializzati prima di chiamare la funzione `collect()` su di essi.

4. Usare il comando `az ml service create realtime` con il commutatore `--collect-model-data true` per creare un servizio web in tempo reale. Ciò assicura che i dati del modello vengano raccolti quando il servizio è in esecuzione.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Per testare la raccolta dati, eseguire `az ml service run realtime`,

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="how-to-view-and-consume-the-collected-data"></a>Come visualizzare e usare i dati raccolti
Per visualizzare i dati raccolti nell'archiviazione blob:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Altri servizi**.
3. Nella casella di ricerca digitare `Storage accounts` e premere **Invio**
4. Dal pannello di ricerca **Account di archiviazione**, selezionare la risorsa **Account di archiviazione**. Per determinare l'account di archiviazione, usare la procedura seguente,

    a. Passare al Workbench di Azure ML, selezionare il progetto su cui si sta lavorando e aprire un prompt della riga di comando dal menu **File**
    
    b. Digitare `az ml env show -v` e controllare il valore *storage_account*. Questo è il nome dell'account di archiviazione

5. Fare clic su **Contenitori** nel menu del pannello di risorse, quindi sul contenitore con nome **modeldata**. Potrebbe essere necessario attendere fino a 10 minuti dopo la prima richiesta di servizio web per visualizzare i dati che iniziano a propagarsi nell'account di archiviazione. I flussi di dati in BLOB con il seguente percorso del contenitore:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

I dati possono essere usati dal BLOB di Azure in diversi modi, tramite il software Microsoft e degli strumenti open source. Di seguito sono riportati alcuni esempi:
 - Workbench di Azure ML - aprire il file csv in Azure ML Workbench aggiungendo un file csv come origine dati
 - Excel - aprire i file csv giornalieri come un foglio di calcolo
 - [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/) - creare grafici con i dati estratti dai dati csv nei BLOB
 - [Spark](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-overview) - creare un frame di dati con gran parte dei dati csv
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started) - caricare i dati csv in una tabella hive ed eseguire query SQL direttamente su BLOB

