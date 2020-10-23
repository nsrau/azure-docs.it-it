---
title: 'Esegui script Python: informazioni di riferimento sui moduli'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Execute Python script (Esegui script Python) in Azure Machine Learning per eseguire il codice Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: devx-track-python
author: likebupt
ms.author: keli19
ms.date: 10/21/2020
ms.openlocfilehash: d4934d784e871988b5bc30f7b7cf8c09651576e2
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330368"
---
# <a name="execute-python-script-module"></a>Eseguire il modulo di script Python

Questo articolo descrive il modulo Execute Python script in Azure Machine Learning Designer.

Usare questo modulo per eseguire il codice Python. Per altre informazioni sull'architettura e i principi di progettazione di Python, vedere [come eseguire il codice Python in Azure machine learning designer](../how-to-designer-python.md).

Con Python è possibile eseguire attività che i moduli esistenti non supportano, ad esempio:

+ Visualizzazione dei dati tramite `matplotlib` .
+ Uso delle librerie Python per enumerare i set di impostazioni e i modelli nell'area di lavoro.
+ Lettura, caricamento e manipolazione di dati da origini non supportate dal modulo [Import Data](./import-data.md) .
+ Esegui il tuo codice di apprendimento avanzato. 

## <a name="supported-python-packages"></a>Pacchetti Python supportati

Azure Machine Learning usa la distribuzione anaconda di Python, che include molte utilità comuni per l'elaborazione dei dati. La versione Anaconda verrà aggiornata automaticamente. La versione corrente è:
 -  Distribuzione di Anaconda 4.5 + per Python 3,6 

Per un elenco completo, vedere la sezione [pacchetti Python preinstallati](#preinstalled-python-packages).

Per installare i pacchetti che non sono inclusi nell'elenco preinstallato (ad esempio, *Scikit-varie*), aggiungere il codice seguente allo script: 

```python
import os
os.system(f"pip install scikit-misc")
```

Usare il codice seguente per installare i pacchetti per ottenere prestazioni migliori, in particolare per l'inferenza:
```python
import importlib.util
package_name = 'scikit-misc'
spec = importlib.util.find_spec(package_name)
if spec is None:
    import os
    os.system(f"pip install scikit-misc")
```

> [!NOTE]
> Se la pipeline contiene più moduli Execute Python script che richiedono pacchetti non inclusi nell'elenco preinstallato, installare i pacchetti in ogni modulo.

> [!WARNING]
> Il modulo di script Python di excute non supporta l'installazione di pacchetti che dipendono da librerie native aggiuntive con comando come "apt-get", ad esempio Java, PyODBC e così via. Questo è dovuto al fatto che questo modulo viene eseguito in un ambiente semplice con Python preinstallato e con autorizzazioni non amministrative.  

## <a name="upload-files"></a>Caricare file
Il modulo Execute Python script supporta il caricamento di file tramite il [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true#upload-file-name--path-or-stream-).

L'esempio seguente illustra come caricare un file di immagine nel modulo Execute Python script:

```Python

# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
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
    # For example:
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Al termine dell'esecuzione della pipeline, è possibile visualizzare l'anteprima dell'immagine nel riquadro di destra del modulo.

> [!div class="mx-imgBorder"]
> ![Anteprima dell'immagine caricata](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Come configurare Execute Python script

Il modulo Execute Python script contiene codice Python di esempio che è possibile usare come punto di partenza. Per configurare il modulo Execute Python script (Esegui script Python), fornire un set di input e codice Python da eseguire nella casella di testo **script Python** .

1. Aggiungere il modulo **Execute Python script (Esegui script Python** ) alla pipeline.

2. Aggiungere e connettersi a **DataSet1** tutti i set di dati della finestra di progettazione che si desidera utilizzare per l'input. Fare riferimento a questo set di dati nello script Python come **DataFrame1**.

    L'utilizzo di un set di dati è facoltativo. Usare questa impostazione se si vuole generare dati tramite Python o usare il codice Python per importare i dati direttamente nel modulo.

    Questo modulo supporta l'aggiunta di un secondo set di dati in **Dataset2**. Fare riferimento al secondo set di dati nello script Python come **DataFrame2**.

    I set di dati archiviati in Azure Machine Learning vengono convertiti automaticamente in frame di dati Pandas quando vengono caricati con questo modulo.

    ![Mappa di input di Execute Python](media/module/python-module.png)

4. Per includere nuovi pacchetti o codice Python, connettere il file compresso che contiene queste risorse personalizzate alla porta del **bundle di script** . In alternativa, se lo script è superiore a 16 KB, utilizzare la porta **bundle script** per evitare errori come *CommandLine supera il limite di 16597 caratteri*. 

    
    1. Aggregare lo script e altre risorse personalizzate in un file zip.
    1. Caricare il file zip come **set di dati di file** in studio. 
    1. Trascinare il modulo set di dati dall'elenco *set* di dati nel riquadro modulo sinistro della pagina Creazione e modifica della finestra di progettazione. 
    1. Connettere il modulo DataSet alla porta del **bundle di script** del modulo **Execute R script** .
    
    Qualsiasi file contenuto nell'archivio compresso caricato può essere usato durante l'esecuzione della pipeline. Se l'archivio include una struttura di directory, la struttura viene mantenuta.
    
    Di seguito è riportato un esempio di bundle di script, che contiene un file di script Python e un file txt:
      
    > [!div class="mx-imgBorder"]
    > ![Esempio di bundle di script](media/module/python-script-bundle.png)  

    Di seguito è riportato il contenuto di `my_script.py` :

    ```python
    def my_func(dataframe1):
    return dataframe1
    ```
    Di seguito è riportato il codice di esempio che illustra come utilizzare i file nel bundle di script:    

    ```python
    import pandas as pd
    from my_script import my_func
 
    def azureml_main(dataframe1 = None, dataframe2 = None):
 
        # Execution logic goes here
        print(f'Input pandas.DataFrame #1: {dataframe1}')
 
        # Test the custom defined python function
        dataframe1 = my_func(dataframe1)
 
        # Test to read custom uploaded files by relative path
        with open('./Script Bundle/my_sample.txt', 'r') as text_file:
            sample = text_file.read()
    
        return dataframe1, pd.DataFrame(columns=["Sample"], data=[[sample]])
    ```

5. Nella casella di testo **script Python** Digitare o incollare uno script Python valido.

    > [!NOTE]
    >  Prestare attenzione durante la scrittura dello script. Verificare che non siano presenti errori di sintassi, ad esempio l'uso di variabili non dichiarate o di moduli o funzioni non importati. Prestare particolare attenzione all'elenco dei moduli preinstallati. Per importare i moduli non elencati, installare i pacchetti corrispondenti nello script, ad esempio:
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    La casella di testo **script Python** viene prepopolata con alcune istruzioni nei commenti e il codice di esempio per l'accesso ai dati e l'output. È necessario modificare o sostituire questo codice. Seguire le convenzioni Python per rientro e maiuscole/minuscole:

    + Lo script deve contenere una funzione denominata `azureml_main` come punto di ingresso per questo modulo.
    + La funzione del punto di ingresso deve avere due argomenti di input, `Param<dataframe1>` e `Param<dataframe2>` , anche quando questi argomenti non vengono usati nello script.
    + I file compressi connessi alla terza porta di input vengono decompressi e archiviati nella directory `.\Script Bundle` , che viene anche aggiunta a Python `sys.path` . 

    Se il file con estensione zip contiene `mymodule.py` , importarlo usando `import mymodule` .

    Due set di impostazioni possono essere restituiti alla finestra di progettazione, che deve essere una sequenza di tipo `pandas.DataFrame` . È possibile creare altri output nel codice Python e scriverli direttamente in archiviazione di Azure.

    > [!WARNING]
    > **Non** è consigliabile connettersi a un database o ad altre archiviazioni esterne nel **modulo Execute Python script**. È possibile usare il modulo [Import Data](./import-data.md) ed [Export Data](./export-data.md)     

6. Inviare la pipeline.

    Tutti i dati e il codice vengono caricati in una macchina virtuale ed eseguiti usando l'ambiente Python specificato.

## <a name="results"></a>Risultati

I risultati di qualsiasi calcolo dal codice Python incorporato devono essere forniti come `pandas.DataFrame` , che viene automaticamente convertito nel formato del set di dati Azure Machine Learning. È quindi possibile usare i risultati con altri moduli della pipeline.

Il modulo restituisce due set di impostazioni:  
  
+ Il set di dati **dei risultati 1**, definito dal primo frame di dati Pandas restituito in uno script Python.

+ Set di dati di **risultati 2**, definito dal secondo frame di dati Pandas restituito in uno script Python.

## <a name="preinstalled-python-packages"></a>Pacchetti Python preinstallati
I pacchetti preinstallati sono:
-    adal==1.2.2
-    applicationinsights==0.11.9
-    attrs = = 19.3.0
-    Azure-comune = = 1.1.25
-    Azure-Core = = 1.3.0
-    azure-graphrbac==0.61.1
-    Azure-Identity = = 1.3.0
-    azure-mgmt-authorization==0.60.0
-    azure-mgmt-containerregistry==2.8.0
-    Azure-Mgmt-insieme di credenziali delle credenziali = = 2.2.0
-    Azure-Mgmt-Resource = = 8.0.1
-    Azure-Mgmt-storage = = 8.0.0
-    Azure-storage-BLOB = = 1.5.0
-    Azure-storage-comune = = 1.4.2
-    azureml-Core = = 1.1.5.5
-    azureml-dataprep-native = = 14.1.0
-    azureml-dataprep = = 1.3.5
-    azureml-defaults = = 1.1.5.1
-    azureml-Designer-Classic-Modules = = 0.0.118
-    azureml-designer-Core = = 0.0.31
-    azureml-designer-Internal = = 0.0.18
-    azureml-Model-Management-SDK = = 1.0.1 B6. post1
-    azureml-pipeline-Core = = 1.1.5
-    azureml-telemetria = = 1.1.5.3
-    backports.tempfile==1.0
-    backports.weakref==1.0.post1
-    boto3 = = 1.12.29
-    botocore = = 1.15.29
-    cachetools = = 4.0.0
-    Certified = = 2019.11.28
-    cffi==1.12.3
-    chardet==3.0.4
-    fare clic = = 7.1.1
-    cloudpickle = = 1.3.0
-    ConfigParser = = 3.7.4
-    contextlib2 = = 0.6.0. post1
-    crittografia = = 2.8
-    cycler==0.10.0
-    dill==0.3.1.1
-    distro==1.4.0
-    Docker = = 4.2.0
-    docutils==0.15.2
-    dotnetcore2 = = 2.1.13
-    Flask = = 1.0.3
-    fusepy==3.0.1
-    gensim==3.8.1
-    Google-API-Core = = 1.16.0
-    Google-auth = = 1.12.0
-    Google-Cloud-Core = = 1.3.0
-    Google-Cloud-Storage = = 1.26.0
-    Google-ripristinabile-media = = 0.5.0
-    googleapis-Common-Protos = = 1.51.0
-    gunicorn = = 19.9.0
-    IDNA = = 2.9
-    sbilanciato-Learn = = 0.4.3
-    isodate==0.6.0
-    itsdangerous = = 1.1.0
-    Jeepney = = 0.4.3
-    jinja2 = = 2.11.1
-    jmespath = = 0.9.5
-    joblib = = 0.14.0
-    JSON-Logging-py = = 0.2
-    jsonpickle = = 1.3
-    jsonschema = = 3.0.1
-    kiwisolver==1.1.0
-    Liac-ARFF = = 2.4.0
-    lightgbm==2.2.3
-    markupsafe = = 1.1.1
-    Matplotlib = = 3.1.3
-    altro-itertools = = 6.0.0
-    MSAL-Extensions = = 0.1.3
-    MSAL = = 1.1.0
-    msrest = = 0.6.11
-    msrestazure = = 0.6.3
-    ndg-httpsclient==0.5.1
-    nimbusml = = 1.6.1
-    NumPy = = 1.18.2
-    oauthlib==3.1.0
-    Pandas = = 0.25.3
-    pathspec = = 0.7.0
-    PIP = = 20.0.2
-    portalocker = = 1.6.0
-    protobuf = = 3.11.3
-    pyarrow = = 0.16.0
-    pyasn1-Modules = = 0.2.8
-    pyasn1 = = 0.4.8
-    pycparser = = 2.20
-    pycryptodomex = = 3.7.3
-    pyjwt = = 1.7.1
-    pyopenssl = = 19.1.0
-    PyParsing = = 2.4.6
-    pyrsistent = = 0.16.0
-    Python-dateutil = = 2.8.1
-    pytz==2019.3
-    richieste-oauthlib = = 1.3.0
-    richieste = = 2.23.0
-    RSA = = 4.0
-    ruamel.yaml==0.15.89
-    s3transfer = = 0.3.3
-    Scikit-learn = = 0.22.2
-    SciPy = = 1.4.1
-    secretstorage = = 3.1.2
-    setuptools = = 46.1.1. post20200323
-    Sei = = 1.14.0
-    Smart-Open = = 1.10.0
-    urllib3 = = 1.25.8
-    WebSocket-client = = 0.57.0
-    Werkzeug = = 0.16.1
-    Wheel = = 0.34.2

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
