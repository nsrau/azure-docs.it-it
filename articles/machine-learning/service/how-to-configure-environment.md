---
title: Configurare un ambiente di sviluppo per Azure Machine Learning | Microsoft Docs
description: Informazioni su come configurare un ambiente di sviluppo quando si usa il servizio Azure Machine Learning. Questo documento illustra come usare ambienti Conda, creare file di configurazione e configurare Jupyter Notebook, Azure Notebooks, ambienti IDE, editor di codice e Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 11/6/2018
ms.openlocfilehash: 8ce411e424d538a4a1f94300bfe5510658017f56
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238323"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurare un ambiente di sviluppo per Azure Machine Learning

Questo articolo descrive come configurare un ambiente di sviluppo per usare il servizio di Azure Machine Learning e illustra gli argomenti indicati di seguito.

- Come creare un file di configurazione che associ l'ambiente in uso a un'area di lavoro di Azure Machine Learning.
- Come configurare gli ambienti di sviluppo seguenti:
  - Jupyter Notebook sul computer in uso
  - Visual Studio Code
  - Editor di codice personalizzato
- Come configurare un [ambiente virtuale Conda](https://conda.io/docs/user-guide/tasks/manage-environments.html) e usarlo per Azure Machine Learning. È consigliabile usare Anaconda Continuum per isolare l'ambiente di lavoro per evitare conflitti di dipendenza tra pacchetti.

## <a name="prerequisites"></a>Prerequisiti

- Configurare un'area di lavoro di servizio di Azure Machine Learning. Seguire i passaggi descritti in [Guida introduttiva: Iniziare a usare Azure Machine Learning con il portale di Azure](quickstart-get-started.md).
- Installare lo strumento di gestione pacchetti per [Continuum Anaconda](https://www.anaconda.com/download/) o [Miniconda](https://conda.io/miniconda.html).
- Se si usa Visual Studio Code, ottenere l'[estensione Python](https://code.visualstudio.com/docs/python/python-tutorial).

> [!NOTE]
> È possibile testare i comandi della shell mostrati in questo articolo tramite bash (in Linux e Mac OS) oppure il prompt dei comandi (in Windows).

## <a name="create-a-workspace-configuration-file"></a>Creare un file di configurazione dell'area di lavoro

Azure Machine Learning SDK usa il file di configurazione dell'area di lavoro per comunicare con l'area di lavoro di servizio di Azure Machine Learning.

- Per creare il file di configurazione, completare i passaggi descritti in [Guida introduttiva: Iniziare a usare Azure Machine Learning con il portale di Azure](quickstart-get-started.md).
  - Il processo consente di creare un file `config.json` in Azure Notebooks e contiene le informazioni di configurazione per l'area di lavoro.
  - Scaricare o copiare il file `config.json` nella stessa directory degli script o dei notebook che vi fanno riferimento.

- In alternativa, è possibile creare manualmente il file seguendo questa procedura:

    1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com). Copiare i valori __Nome dell'area di lavoro__, __Gruppo di risorse__ e __ID sottoscrizione__. Questi valori vengono usati per creare il file di configurazione.
        ![Portale di Azure](./media/how-to-configure-environment/configure.png)

    1. Creare il file con codice Python seguente e verificare di eseguire il codice nella stessa directory degli script o dei notebook che fanno riferimento all'area di lavoro:

        ```python
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
        Il codice scrive i file seguenti `aml_config/config.json`:

        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        È possibile copiare la directory `aml_config` o solo il file `config.json` in qualsiasi altra directory che fa riferimento all'area di lavoro.

       > [!NOTE]
       > Altri script o notebook nella stessa directory o in directory sottostanti caricano l'area di lavoro con `ws=Workspace.from_config()`.

## <a name="azure-notebooks-and-data-science-virtual-machines"></a>Azure Notebooks e Data Science Virtual Machine

Azure Notebooks e Azure Data Science Virtual Machines (DSVM) sono preconfigurati per interagire con il servizio Azure Machine Learning. Tali ambienti includono i componenti necessari, ad esempio Azure Machine Learning SDK.

### <a name="azure-notebooks"></a>Notebook di Azure

- Azure Notebooks è un servizio Jupyter Notebook nel cloud di Azure,
- Per usare questi ambienti, tuttavia, è necessario un file di configurazione dell'area di lavoro.

Per un esempio relativo all'uso di Azure Notebooks con il servizio Azure Machine Learning, vedere il documento [Guida introduttiva: Iniziare a usare Azure Machine Learning con il portale di Azure](quickstart-get-started.md).

### <a name="data-science-virtual-machines"></a>Data Science Virtual Machine

- mentre Data Science Virtual Machine è una macchina virtuale personalizzata progettata per operare in ambito data science. Sono inclusi:
  - Strumenti di data science comuni
  - Ambienti di sviluppo integrato (IDE) come RStudio e PyCharm
  - Pacchetti come Jupyter Notebook e TensorFlow

In DSVM sono già installati più ambienti Anaconda. Per usare Azure Machine Learning SDK per Python senza installare il pacchetto, aprire un prompt/una shell dei comandi e usare uno dei comandi seguenti per attivare l'ambiente:

* Nella DSVM di __Ubuntu__, usare questo comando:

    ```shell
    conda activate py36
    ```

* Nella DSVM di __Windows__, usare questo comando:

    ```shell
    conda activate AzureML
    ```

Dopo aver attivato l'ambiente è possibile importare immediatamente Azure Machine Learning SDK nello strumento di compilazione preferito, senza installare il pacchetto.

```python
import azureml.core
print(azureml.core.VERSION)
```

Per altre informazioni su Data Science Virtual Machine, vedere [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="configure-jupyter-notebooks-on-your-computer"></a>Configurare Jupyter Notebook nel computer in uso

1. Aprire una shell o un prompt dei comandi.

1. Creare un ambiente Conda con i comandi seguenti:

    ```shell
    # create a new conda environment with Python 3.6, numpy, and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # On Mac OS run
    source activate myenv
    ```

    Se è necessario scaricare Python 3.6 e altri componenti, la creazione dell'ambiente può richiedere alcuni minuti.

1. Installare Azure Machine Learning SDK con notebook extra e l'SDK di preparazione dei dati usando il comando seguente:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   È possibile visualizzare la documentazione di riferimento di Python per le classi e i metodi negli SDK seguenti:
   + [Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk)
   + [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

   > [!NOTE]
   > Se si riceve un messaggio che indica che `PyYAML` non può essere installato, usare il comando seguente:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   L'installazione dell'SDK può richiedere alcuni minuti.

1. Installare i pacchetti per la sperimentazione di Azure Machine Learning. Usare il comando seguente e sostituire `<new package>` con il pacchetto da installare:

    ```shell
    conda install <new package>
    ```

1. Installare un server Jupyter Notebook basato su Conda e abilitare i widget di esperimento (per visualizzare le informazioni di esecuzione). Usare il seguente comando:

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

1. Aprire Jupyter Notebook con il comando seguente:

    ```shell
    jupyter notebook
    ```

1. Aprire un nuovo notebook, selezionare "myenv" come kernel e quindi verificare che Azure Machine Learning SDK sia installato. Eseguire il comando seguente in una cella di notebook:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Configurare Visual Studio Code

1. Aprire una shell o un prompt dei comandi.

1. Creare un ambiente Conda con i comandi seguenti:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

1. Installare l'SDK di preparazione dei dati e Azure Machine Learning SDK con il comando seguente:

    ```shell
    pip install --upgrade azureml-sdk[automl] azureml-dataprep
    ```

1. Installare l'estensione Visual Studio Code Tools for AI. Vedere [Visual Studio Code Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

1. Installare i pacchetti per la sperimentazione di Azure Machine Learning. Usare il comando seguente sostituendo `<new package>` con il pacchetto da installare:

    ```shell
    conda install <new package>
    ```

1. Aprire Visual Studio Code e quindi usare **CTRL-MAIUSC-P** (in Windows) o **COMMAND-SHIFT-P** (in Mac OS) per aprire **Riquadro comandi**. Immettere _Python: Select Interpreter_ (Python: Seleziona interprete) e quindi selezionare l'ambiente Conda creato.

   > [!NOTE]
   > Visual Studio Code riconoscere automaticamente gli ambienti conda presenti nel computer. Per altre informazioni, vedere la [documentazione su Visual Studio Code](https://code.visualstudio.com/docs/python/environments#_conda-environments).

1. Per convalidare la configurazione, usare Visual Studio Code per creare un nuovo file di script Python con il codice seguente e quindi eseguirlo:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-a-custom-code-editor"></a>Configurare un editor di codice personalizzato

È possibile usare un editor di codice preferito con Azure Machine Learning SDK.

1. Creare l'ambiente Conda come descritto nel passaggio 2 della sezione precedente [Configurare Visual Studio Code](#configure-visual-studio-code).
1. Seguire quindi le istruzioni specifiche dell'editor per usare l'ambiente Conda. È possibile ad esempio seguire le [istruzioni PyCharm](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md)
