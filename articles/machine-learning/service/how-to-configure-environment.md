---
title: Configurare un ambiente di sviluppo per Azure Machine Learning | Microsoft Docs
description: Informazioni su come configurare un ambiente di sviluppo quando si usa il servizio Azure Machine Learning. Questo documento illustra come usare ambienti Conda, creare file di configurazione e configurare Jupyter Notebook, Azure Notebooks, ambienti IDE, editor di codice e Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 8/6/2018
ms.openlocfilehash: 675dae022376fc62292f3b079bd735939b9199c2
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220296"
---
# <a name="configure-a-development-environment-for-the-azure-machine-learning-service"></a>Configurare un ambiente di sviluppo per Azure Machine Learning

Informazioni su come configurare un ambiente di sviluppo per poter usare il servizio Azure Machine Learning. Si apprenderà la procedura necessaria per creare un file di configurazione che associ l'ambiente in uso a un'area di lavoro di Azure Machine Learning e si imparerà a configurare gli ambienti di sviluppo seguenti:

* Jupyter Notebook nel computer in uso
* Visual Studio Code
* Un editor di codice di propria scelta

Si consiglia di usare gli [ambienti virtuali conda](https://conda.io/docs/user-guide/tasks/manage-environments.html) Anaconda Continuum per isolare l'ambiente di lavoro ed evitare così conflitti di dipendenza tra i pacchetti. Questo articolo illustra la procedura di configurazione e utilizzo di un ambiente conda per Azure Machine Learning.


## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro del servizio Azure Machine Learning. Per crearne una, usare la procedura descritta nel documento [Iniziare a usare il servizio Azure Machine Learning](quickstart-get-started.md).

* Gestione pacchetti per [Continuum Anaconda](https://www.anaconda.com/download/) o [Miniconda](https://conda.io/miniconda.html).

 * Per l'ambiente Visual Studio Code, l'[estensione Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="create-workspace-configuration-file"></a>Creare il file di configurazione dell'area di lavoro

Il file di configurazione dell'area di lavoro viene usato dall'SDK per comunicare con l'area di lavoro del servizio Azure Machine Learning.  Per ottenere questo file sono disponibili due modi:

* Completare la [guida introduttiva](quickstart-get-started.md) per creare un'area di lavoro e un file di configurazione. Il file `config.json` viene automaticamente creato in Azure Notebook  e contiene le informazioni di configurazione per l'area di lavoro.  Scaricarlo o copiarlo nella stessa directory degli script o dei notebook che fanno riferimento all'area di lavoro.


* Creare manualmente il file di configurazione seguendo questa procedura:

    1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com). Copiare i valori __Nome dell'area di lavoro__, __Gruppo di risorse__ e __ID sottoscrizione__. Questi valori vengono usati per creare il file di configurazione.

        ![Portale di Azure](./media/how-to-configure-environment/configure.png) 
    
    1. Creare il file con questo codice Python. Eseguire il codice nella stessa directory degli script o dei notebook che fanno riferimento all'area di lavoro.
        ```
        from azureml.core import Workspace

        subscription_id ='<subscription-id>'
        resource_group ='<resource-group>'
        workspace_name = '<workspace-name>'
        
        try:
           ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
           ws.write_config()
           print('Library configuration succeeded')
        except:
           print('Workspace not found')
        ```
        Viene scritto il file `aml_config/config.json` seguente: 
    
        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        È possibile copiare la directory `aml_config` o solo il file `config.json` in qualsiasi altra directory che fa riferimento all'area di lavoro.

>[!NOTE] 
>Eventuali altri script o notebook nella stessa directory o in directory sottostanti caricheranno l'area di lavoro con `ws=Workspace.from_config()`

## <a name="azure-notebooks-and-data-science-virtual-machine"></a>Azure Notebooks e Data Science Virtual Machine

Azure Notebooks e Azure Data Science Virtual Machines (DSVM) sono preconfigurati per interagire con il servizio Azure Machine Learning. Tutti i componenti necessari, ad esempio Azure Machine Learning SDK, sono infatti pre-installati in questi ambienti.

Azure Notebooks è un servizio Jupyter Notebook nel cloud di Azure, mentre Data Science Virtual Machine è un'immagine di macchina virtuale preconfigurata per analisi scientifiche dei dati. La macchina virtuale include gli strumenti, gli ambienti di sviluppo e i pacchetti più diffusi, quali Jupyter Notebook, PyCharm e Tensorflow.

Per poter usare questi ambienti, tuttavia, è necessario un file di configurazione dell'area di lavoro.

Per altre informazioni su Data Science Virtual Machines, vedere la documentazione su [Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

Per un esempio relativo all'uso di Azure Notebooks con il servizio Azure Machine Learning, vedere il documento [Iniziare a usare il servizio Azure Machine Learning](quickstart-get-started.md).

## <a name="configure-jupyter-notebooks-on-your-own-computer"></a>Configurare Jupyter Notebook nel computer in uso

1. Aprire un prompt dei comandi o una shell.

2. Per creare un ambiente conda, usare i comandi seguenti:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

    La creazione dell'ambiente può richiedere alcuni minuti, poiché è possibile che sia necessario scaricare Python 3.6 e altri componenti.

3. Per installare Azure Machine Learning SDK con componenti aggiuntivi per notebook, usare il comando seguente:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl]
    ```

    L'installazione dell'SDK può richiedere alcuni minuti.

4. Per installare i pacchetti necessari per la sperimentazione di Machine Learning, usare il comando seguente e sostituire `<new package>` con il pacchetto da installare:

    ```shell
    conda install <new package>
    ```

5. Per installare un server Jupyter Notebook compatibile con conda e abilitare i widget dell'esperimento (per visualizzare le informazioni di esecuzione), usare i comandi seguenti:

    ```shell
    # install Jupyter 
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

6. Per avviare Jupyter Notebook, usare il comando seguente:

    ```shell
    jupyter notebook
    ```

7. Aprire un nuovo notebook e selezionare "myenv" come kernel. Eseguire quindi il comando seguente in una cella di Notebook per accertarsi che Azure Machine Learning SDK sia installato:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Configurare Visual Studio Code

1. Aprire un prompt dei comandi o una shell.

2. Per creare un ambiente conda, usare i comandi seguenti:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

2. Per installare Azure Machine Learning SDK, usare il comando seguente:
 
    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

4. Per installare Visual Studio Code Tools for AI, vedere la voce di Visual Studio Marketplace relativa a [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai). 

5. Per installare i pacchetti necessari per la sperimentazione di Machine Learning, usare il comando seguente e sostituire `<new package>` con il pacchetto da installare:

    ```shell
    conda install <new package>
    ```

6. Avviare Visual Studio Code e quindi usare __CTRL-MAIUSC-P__ per visualizzare il __riquadro comandi__. Immettere *Python: Select Interpreter* (Python: Seleziona interprete) e selezionare l'ambiente conda creato.

    > [!NOTE]
    > Visual Studio Code riconoscere automaticamente gli ambienti conda presenti nel computer. Per altre informazioni, vedere la [documentazione su Visual Studio Code](https://code.visualstudio.com/docs/python/environments#_conda-environments).

7. Per convalidare la configurazione, usare Visual Studio Code per creare un nuovo file di script Python con il codice seguente ed eseguirlo:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-code-editor-of-your-choice"></a>Configurare un editor di codice di propria scelta

Per usare un editor di codice personalizzato con Azure Machine Learning SDK, è necessario creare prima un ambiente conda, come descritto in precedenza. Seguire quindi le istruzioni specifiche dell'editor per usare l'ambiente conda. Le istruzioni per PyCharm, ad esempio, sono disponibili all'indirizzo [https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).
 
## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md)
