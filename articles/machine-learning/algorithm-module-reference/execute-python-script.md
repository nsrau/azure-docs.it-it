---
title: 'Esecuzione di python script: riferimento al moduloExecute Python Script: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Esegui script Python in Azure Machine Learning per eseguire codice Python.Learn how to use the Execute Python Script module in Azure Machine Learning to run Python code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 0f86d1ad03062797764af6a0d49beacaa3458a8f
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365559"
---
# <a name="execute-python-script-module"></a>Eseguire il modulo Python Script

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per eseguire il codice Python. Per ulteriori informazioni sull'architettura e sui principi di progettazione di Python, vedere [l'articolo seguente.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Con Python, è possibile eseguire attività che non sono attualmente supportate da moduli esistenti, ad esempio:

+ Visualizzazione dei dati tramite`matplotlib`
+ Uso delle librerie Python per enumerare set di dati e modelli nell'area di lavoro
+ Lettura, caricamento e manipolazione di dati da origini non supportate dal modulo [Importa dati](./import-data.md)
+ Esegui il tuo codice di deep learning 


Azure Machine Learning usa la distribuzione Anaconda di Python, che include molte utilità comuni per l'elaborazione dei dati. Aggiorneremo automaticamente la versione di Anaconda. La versione attuale è:
 -  Distribuzione di Anaconda 4.5 per Python 3.6 

I pacchetti preinstallati sono:
-    adal 1.2.2
-    Applicationinsights : 0,11,9
-    orss : 19,3,0
-    azure-comune: 1.1.25
-    Azure-core : 1.3.0
-    azure-graphrbac: 0,61,1
-    Azure-identità : 1.3.0
-    Azure-mgmt-authorization -0,60.0
-    azure-mgmt-containerregistry - 2.8.0
-    azure-mgmt-keyvault 2.2.0
-    Azure-mgmt-risorsa: 8.0.1
-    Azure-mgmt-storage: 8.0.0
-    azure-storage-blob: 1.5.0
-    azure-storage-common: 1.4.2
-    azureml-core 1.1.5.5
-    azureml-dataprep-native
-    azureml-dataprep
-    Azureml-defaults: 1.1.5.1
-    azureml-designer-classic-modules
-    azureml-designer-core
-    azureml-designer-interno
-    azureml-model-management-sdk
-    azureml-pipeline-core
-    azureml-telemetry: 1.1.5.3
-    backports.tempfile : 1,0
-    backports.weakref'1.0.post1
-    boto3 ,1,12,29
-    botocore 1,15,29
-    cachetools 4.0.0
-    certifi 2019.11.28
-    cffi 1,12,3
-    Chardet : 3,0,4 USD
-    fare clic su 7.1.1
-    cloudpickle 1.3.0
-    configparser 3.7.4
-    librazione di contesto2: 0,6.0.post1
-    crittografia 2,8 USD
-    cycler 0,10,0
-    dil: 0,3,1,1
-    distro 1.4.0
-    docker : 4.2.0
-    docutils 0,15,2
-    dotnetcore2 : 2.1.13
-    fiaschetta 1,0,3
-    fusepy :3.0.1
-    gensim 3,8,1
-    Google-api-core
-    l'autenticazione di google 1.12.0
-    google-cloud-core
-    Archiviazione di google-cloud: 1.26.0
-    google-resumable-media 0.5.0
-    googleapis-common-protos
-    gunicorn 19,9,0
-    2,9
-    apprendimento squilibrato- 0,4,3
-    isodata : 0,6,0
-    il suo pericoloso 1.1.0
-    jeepney 0,4,3
-    Jinja2 : 2,11,1
-    Jmespath: 0,9,5
-    joblib: 0,14,0
-    json-logging-py
-    jsonpickle 1,3
-    jsonschema : 3.0.1
-    kiwisolver 1,1,0
-    liac-arff
-    lightgbm 2.2.3
-    markupsafe 1.1.1
-    matplotlib
-    more-itertools
-    msal-extensions : 0.1.3
-    il valore msal è 1.1.0
-    il valore msrest è 0,6,11
-    msrestazure : 0,6,3
-    ndg-httpsclient : 0,5,1
-    nimbusml 1,6,1
-    numpy: 1,18,2
-    oauthlib
-    Panda : 0,25,3
-    percorsospec: 0,7,0
-    pip: 20,0,2
-    portalocker 1.6.0
-    protobufi: 3,11,3
-    freccia : 0,16,0
-    pyasn1-modules -0.2.8
-    piasn1 - 0,4,8
-    pycparser 2,20 USD
-    pycryptodomex - 3.7.3
-    piregina 1,7,1
-    pyopenssl 19.1.0
-    pyparsing - 2.4.6
-    pilstent
-    python-dateutil - 2,8,1
-    il valore di pytz 2019,3
-    Richieste di richiesta-oauthlib
-    Richiesta di richiesta : 2,23,0
-    RSA 4,0
-    ruamel.yaml 0,15,89
-    s3trasferimento: 0,3,3
-    scikit-learn -0,22,2
-    scipy
-    secretstorage : 3.1.2
-    setuptools: 46.1.1.post20200323
-    sei: 1,14,0
-    smart-open : 1.10.0
-    URLlib3 :1.25.8
-    websocket-client 0,57,0
-    werkzeug 0,16,1
-    ruota: 0,34,2

 Per installare altri pacchetti non contenuti nell'elenco preinstallato, ad esempio *scikit-misc*, aggiungere il codice seguente allo script: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Caricare file
**Lo script Execute Python** supporta il caricamento di file tramite Azure Machine Learning Python [SDK.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-)

L'esempio seguente mostra come caricare un file di immagine nel modulo **Execute Python Script:**

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Al termine dell'esecuzione della pipeline, è possibile visualizzare l'anteprima dell'immagine nel pannello di destra del modulo

> [!div class="mx-imgBorder"]
> ![Immagine caricata](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Come configurare Esegui Python Script

Il modulo **Execute Python Script** contiene codice Python di esempio che è possibile usare come punto di partenza. Per configurare il modulo **Execute Python Script,** si fornisce un set di input e codice Python da eseguire nella casella di testo **dello script Python.**

1. Aggiungere il modulo **Esegui script Python** alla pipeline.

2. Aggiungere e connettere in **Dataset1** tutti i set di dati della finestra di progettazione che si desidera utilizzare per l'input. Fare riferimento a questo set di dati nello script Python come **Frame di dati1**.

    L'uso di un set di dati è facoltativo, se si desidera generare dati utilizzando Python, oppure utilizzare il codice Python per importare i dati direttamente nel modulo.

    Questo modulo supporta l'aggiunta di un secondo set di dati in **Dataset2**. Fare riferimento al secondo set di dati nello script Python come Frame2.Reference the second dataset in your Python script as DataFrame2.

    I set di dati archiviati in Azure Machine Learning vengono convertiti automaticamente in **pandas** data.frames quando vengono caricati con questo modulo.

    ![Eseguire la mappa di input Python](media/module/python-module.png)

4. Per includere nuovi pacchetti o codice Python, aggiungere il file compresso contenente queste risorse personalizzate in **Script bundle**. L'input nel **bundle di script** deve essere un file compresso caricato nell'area di lavoro come tipo di file Dataset.The input to Script bundle must be a zipped file uploaded to your workspace as a File type Dataset. È possibile caricare il set di dati nella pagina **Asset dataset** e trascinare il modulo del set di dati dall'elenco Set **di dati** personali nell'albero del modulo sinistro nella pagina di creazione della finestra di progettazione. 

    Qualsiasi file contenuto nell'archivio compresso caricato può essere utilizzato durante l'esecuzione della pipeline. Se l'archivio include una struttura di directory, la struttura viene mantenuta, ma è necessario anteporre una directory denominata **src** al percorso.

5. Nella casella di testo **Script Python** digitare o incollare uno script Python valido.

    La casella di testo **dello script Python** è prepopolata con alcune istruzioni nei commenti e codice di esempio per l'accesso ai dati e l'output. È necessario modificare o sostituire questo codice. Assicurati di seguire le convenzioni Python sul rientro e l'uso di maiuscole e minuscole.

    + Lo script deve contenere una funzione denominata `azureml_main` come punto di ingresso per questo modulo.
    + La funzione del punto di ingresso `Param<dataframe1>` può contenere fino a due argomenti di input: e`Param<dataframe2>`
    + I file compressi collegati alla terza porta di input `.\Script Bundle`vengono decompressi e `sys.path`memorizzati nella directory , , che viene aggiunta anche a Python . 

    Pertanto, se il `mymodule.py`file zip `import mymodule`contiene , importarlo utilizzando .

    + È possibile restituire due set di dati alla finestra `pandas.DataFrame`di progettazione, che deve essere una sequenza di tipo . È possibile creare altri output nel codice Python e scriverli direttamente nell'archiviazione di Azure.You can create other outputs in your Python code and write them directly to Azure storage.

6. Inviare la pipeline oppure selezionare il modulo e fare clic su **Esegui selezionato** per eseguire solo lo script Python.

    Tutti i dati e il codice vengono caricati in una macchina virtuale ed eseguiti utilizzando l'ambiente Python specificato.

## <a name="results"></a>Risultati

I risultati di tutti i calcoli eseguiti dal codice Python incorporato devono essere forniti come panda. DataFrame, che viene convertito automaticamente nel formato di set di dati di Azure Machine Learning, in modo da poter usare i risultati con altri moduli nella pipeline.

Il modulo restituisce due set di dati:The module returns two datasets:  
  
+ **Risultati Dataset 1**, definito dal primo frame di dati panda restituito nello script Python

+ **Result Dataset 2**, definito dal secondo frame di dati panda restituito nello script Python


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 