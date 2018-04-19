---
title: "Usare la funzionalità di raccolta dati di modello in Azure Machine Learning Workbench | Microsoft Docs"
description: "L'articolo illustra come usare la funzionalità di raccolta dati di modello in Azure Machine Learning Workbench"
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 2251f8d241f3ec47d46c04160caf997bcd025124
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="collect-model-data-by-using-data-collection"></a>Raccogliere i dati di modello tramite la raccolta dati

La funzionalità di raccolta dati di modello in Azure Machine Learning consente di archiviare gli input e le previsioni del modello da un servizio Web.

## <a name="install-the-data-collection-package"></a>Installare il pacchetto di raccolta dati
È possibile installare la libreria di raccolta dati in modo nativo su Linux e Windows.

### <a name="windows"></a>Windows
Su Windows installare il modulo dell'agente di raccolta dati usando il comando seguente:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
Su Linux installare prima la libreria libxml++. Eseguire il comando seguente, che deve essere rilasciato in sudo:

    sudo apt-get install libxml++2.6-2v5

Quindi, eseguire il comando seguente:

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>Impostare le variabili di ambiente

La raccolta dati di modello dipende da due variabili di ambiente. AML_MODEL_DC_STORAGE_ENABLED deve essere impostata su **true** (tutto in lettere minuscole) e AML_MODEL_DC_STORAGE deve essere impostata sulla stringa di connessione dell'account di Archiviazione di Azure in cui si desidera archiviare i dati.

Tali variabili di ambiente sono già impostate automaticamente quando il servizio Web è in esecuzione in un cluster in Azure. Quando il servizio è in esecuzione in locale, è necessario impostare le variabili manualmente. In Docker, usare il parametro -e del comando run del docker per passare le variabili di ambiente.

## <a name="collect-data"></a>Raccogliere i dati

Per usare la raccolta dati di modello, è necessario apportare le modifiche seguenti al file dei punteggi:

1. Aggiungere il codice seguente all'inizio del file:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Aggiungere le righe di codice seguenti alla funzione `init()`:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Aggiungere le righe di codice seguenti alla funzione `run(input_df)`:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Assicurarsi che le variabili `input_df` e `pred` (valore di previsione da `model.predict()`) vengano inizializzate prima di chiamare la funzione `collect()` su di esse.

4. Usare il comando `az ml service create realtime` con l'opzione `--collect-model-data true` per creare un servizio Web in tempo reale. Questo passaggio assicura che i dati del modello vengano raccolti quando il servizio è in esecuzione.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Per testare la raccolta dati, eseguire il comando `az ml service run realtime`:

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Visualizzare i dati raccolti
Per visualizzare i dati raccolti nell'archiviazione blob:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi**.
3. Nella casella di ricerca digitare **Account di archiviazione** e premere INVIO.
4. Dal pannello di ricerca **Account di archiviazione**, selezionare la risorsa **Account di archiviazione**. Per determinare l'account di archiviazione, usare la procedura seguente:

    a. Passare a Azure Machine Learning Workbench, selezionare il progetto su cui si sta lavorando e aprire un prompt dei comandi dal menu **File**.
    
    b. Digitare `az ml env show -v` e controllare il valore di *storage_account*. Questo è il nome dell'account di archiviazione.

5. Selezionare **Contenitori** nel menu del pannello delle risorse e quindi il contenitore denominato **modeldata**. Per visualizzare i dati che iniziano a propagarsi nell'account di archiviazione potrebbe essere necessario attendere una decina di minuti dalla prima richiesta del servizio Web. I dati vengono trasmessi nei BLOB con il percorso del contenitore seguente:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

I dati possono essere usati dai BLOB di Azure in diversi modi, sia tramite il software Microsoft che tramite strumenti open source. Di seguito sono riportati alcuni esempi:
- Azure Machine Learning Workbench: aprire il file CSV in Azure Machine Learning Workbench aggiungendolo come origine dati.
- Excel: aprire i file CSV giornalieri come foglio di calcolo.
- [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): creare grafici con i dati estratti dai dati CSV nei BLOB.
- [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): creare un frame di dati con una parte significativa dei dati CSV.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): caricare i dati CSV in una tabella Hive ed eseguire query SQL direttamente sul BLOB.

