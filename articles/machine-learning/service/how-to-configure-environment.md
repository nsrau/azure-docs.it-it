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
ms.openlocfilehash: 2fd2d35bde95a3e268f46b398f2163f9d40ab1ee
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613954"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurare un ambiente di sviluppo per Azure Machine Learning

Questo documento contiene informazioni su come configurare un ambiente di sviluppo per poter usare il servizio Azure Machine Learning. Il servizio di Azure Machine Learning è indipendente dalla piattaforma. Gli unici requisiti per l'ambiente di sviluppo sono __Python 3__, __Conda__ (per gli ambienti di tipo isolato) e un file di configurazione che contiene le informazioni sull'area di lavoro di Azure Machine Learning.

Questo documento è incentrato sugli strumenti e ambienti specifici seguenti:

* [Azure Notebooks](#aznotebooks): è un servizio Jupyter Notebook ospitato nel cloud di Azure. Si tratta del modo __più semplice__ per iniziare, perché il SDK di Azure Machine Learning è già installato.
* [Data Science Virtual Machine](#dsvm): una macchina virtuale nel cloud di Azure __progettata per operare in ambito data science__. Python 3, Conda, Jupyter Notebook e SDK di Azure Machine Learning sono già installati. La macchina virtuale viene fornita con i framework di Machine Learning, gli strumenti e gli editor più diffusi per lo sviluppo di soluzioni di Machine Learning. Si tratta probabilmente dell'ambiente di sviluppo __più completo__ di Machine Learning nella piattaforma Azure.
* [Jupyter Notebook](#jupyter): se si usa già i notebook di Jupyter, SDK include alcune funzionalità aggiuntive che è necessario installare.
* [Visual Studio Code](#vscode): se si usa Visual Studio Code, esistono alcune estensioni utili che è possibile installare.

Se si dispone già di un ambiente Python 3, o si desidera solo conoscere i passaggi di base per l'installazione di SDK, vedere la sezione [computer locale](#local).

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro del servizio Azure Machine Learning. Seguire i passaggi descritti in [Guida introduttiva per iniziare a usare i servizi di Azure Machine Learning](quickstart-get-started.md) per crearne una.

- Lo strumento di gestione pacchetti per [Continuum Anaconda](https://www.anaconda.com/download/) o [Miniconda](https://conda.io/miniconda.html).

    > [!IMPORTANT]
    > Quando si usa Azure Notebooks, Anaconda e Miniconda non sono necessari.

- In Linux o Mac OS, è necessaria la shell bash.

    > [!TIP]
    > Se si sta utilizzando Linux o Mac OS e si usa una shell diversa da bash (ad esempio, zsh) si potrebbero verificare errori durante l'esecuzione di alcuni comandi. Per risolvere questo problema, usare il comando `bash` per avviare una nuova shell bash ed eseguire i comandi lì.

- In Windows, è necessario il prompt dei comandi o di Anaconda (installato per Anaconda e Miniconda).

## <a id="anotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (anteprima) è un ambiente di sviluppo interattivo nel cloud di Azure. Si tratta del modo __più semplice__ per iniziare con lo sviluppo di Azure Machine Learning.

* SDK di Azure Machine Learning è __già installata__.
* Dopo aver creato un'area di lavoro del servizio di Azure Machine Learning nel portale di Azure, è possibile fare clic su un pulsante per configurare automaticamente l'ambiente Notebook di Azure per lavorare con l'area di lavoro.

Per iniziare a sviluppare con notebook di Azure, seguire il documento [Introduzione al servizio di Azure Machine Learning](quickstart-get-started.md).

## <a id="dsvm"></a>Data Science Virtual Machine

La Data Science Virtual Machine (DSVM) è una macchina virtuale (VM) personalizzata **progettata per operare in ambito data science**. Sono inclusi:

  - Strumenti di data science comuni
  - Ambienti di sviluppo integrato (IDE) come RStudio e PyCharm
  - Pacchetti come Jupyter Notebook e TensorFlow

SDK di Azure Machine Learning funziona nella versione Windows o Ubuntu della macchina virtuale data science. Seguire questa procedura per usare la DSVM come ambiente di sviluppo:

1. Per creare una macchina virtuale di Data Science, seguire i passaggi in uno dei documenti seguenti:

    * [Creare una macchina virtuale data science Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)
    * [Creare una macchina virtuale data science Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

1. SDK di Azure Machine Learning è **già installato** nella DSVM. Per usare l'ambiente Conda contenente il SDK, usare uno dei seguenti comandi:

    * Nella DSVM di __Ubuntu__, usare questo comando:

        ```shell
        conda activate py36
        ```

    * Nella DSVM di __Windows__, usare questo comando:

        ```shell
        conda activate AzureML
        ```

1. Per verificare che sia possibile accedere al SDK e controllare la versione, usare il codice Python seguente:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Per configurare la macchina virtuale data science per utilizzare l'area di lavoro del servizio di Azure Machine Learning, vedere la sezione [Configura area di lavoro](#workspace).

Per altre informazioni su Data Science Virtual Machine, vedere [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Computer locale

Quando si usa un computer locale (che potrebbe anche essere una macchina virtuale remota), usare la procedura seguente per creare un ambiente conda e installare il SDK:

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

   > [!NOTE]
   > Se si riceve un messaggio che indica che `PyYAML` non può essere installato, usare il comando seguente:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   L'installazione dell'SDK può richiedere alcuni minuti.

1. Installare i pacchetti per la sperimentazione di Azure Machine Learning. Usare il comando seguente e sostituire `<new package>` con il pacchetto da installare:

    ```shell
    conda install <new package>
    ```

1. Per verificare che il SDK è installato, il codice Python seguente:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Notebook Jupyter

I notebook di Jupyter fanno parte del [progetto Jupyter](https://jupyter.org/). Forniscono un'esperienza di codifica interattiva in cui si creano documenti che combinano codice attivo con testi narrativi e grafica. I notebook di Jupyter sono anche un ottimo modo per condividere i risultati con altri utenti, dal momento che è possibile salvare l'output delle sezioni di codice nel documento. È possibile installare Jupyter Notebook in un'ampia gamma di piattaforme.

I passaggi descritti nella sezione [Computer locale](#local) illustrano come installare i componenti facoltativi per i notebook di Jupyter. Per abilitare questi componenti nell'ambiente Notebook di Jupyter, procedere come segue:

1. Aprire una shell o un prompt dei comandi.

1. Per installare un server Jupyter Notebook compatibile con conda e abilitare i widget dell'esperimento, usare i comandi seguenti:

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

1. Per verificare che i notebook di Jupyter possano usare il SDK, aprire un nuovo notebook e selezionare "myenv" come kernel. Eseguire quindi il comando seguente in una cella di notebook:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Per configurare Jupyter Notebook per utilizzare l'area di lavoro del servizio di Azure Machine Learning, vedere la sezione [Configura area di lavoro](#workspace).

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code è un editor di codice multipiattaforma. Si basa su un'installazione locale di Python 3 e Conda per il supporto di Python, ma fornisce strumenti aggiuntivi per l'uso con l'intelligenza artificiale. Fornisce inoltre il supporto per la selezione dell'ambiente Conda all'interno dell'editor di codice.

Per usare Visual Studio Code per lo sviluppo, usare la seguente procedura:

1. Per informazioni su come usare Visual Studio Code per lo sviluppo di Python, vedere il documento [Introduzione a Python in Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

1. Per selezionare l'ambiente Conda, aprire Visual Studio Code e quindi usare __Ctrl-MAIUSC-P__ (Linux e Windows) o __Command-MAIUSC-P__ (Mac) per ottenere il __Riquadro comandi__. Immettere __Python: Select Interpreter__ (Python: Seleziona interprete) e quindi selezionare l'ambiente Conda.

1. Per convalidare che è possibile usare il SDK, creare un nuovo file di Python (.py) che contiene il codice seguente. Quindi, eseguire il file:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Per installare l'estensione Visual Studio Code Tools for AI, vedere la pagina [Strumenti per intelligenza artificiale](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Per altre informazioni, vedere il documento [Utilizza Visual Studio Code Tools for AI con Azure Machine Learning](how-to-vscode-tools.md).

## <a id="workspace"></a>Creare un file di configurazione dell'area di lavoro

Il file di configurazione dell'area di lavoro è un documento JSON che indica all'SDK come comunicare con l'area di lavoro del servizio Azure Machine Learning. Il file è denominato `config.json` e ha il formato seguente:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Questo file deve trovarsi nella struttura della directory che contiene gli script di Python o i notebook di Jupyter. Può essere nella stessa directory, una sottodirectory denominata `aml_config`, o in una directory padre.

Per usare questo file dal codice, usare `ws=Workspace.from_config()`. Questo codice carica le informazioni dal file e si connette all'area di lavoro.

Esistono tre modi per creare il file di configurazione:

* Seguendo la [Guida introduttiva di Azure Machine Learning](quickstart-get-started.md), un file `config.json` viene creato nella libreria di Azure Notebook. e contiene le informazioni di configurazione per l'area di lavoro. È possibile scaricare o copiare questo `config.json` in altri ambienti di sviluppo.

* È possibile **creare manualmente il file** usando un editor di testo. È possibile trovare i valori per passare nel file di configurazione visitando l'area di lavoro nel [portale di Azure](https://portal.azure.com). Copiare i valori __Nome dell'area di lavoro__, __Gruppo di risorse__, e __ID sottoscrizione__ e usarli nel file di configurazione.
        ![Portale di Azure](./media/how-to-configure-environment/configure.png)

* È possibile **creare il file a livello di codice**. Il frammento di codice seguente illustra come connettersi a un'area di lavoro fornendo l'ID sottoscrizione, il gruppo di risorse e il nome dell'area di lavoro. Quindi viene salvata la configurazione dell'area di lavoro nel file:

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    Questo codice scrive il file di configurazione nel file `aml_config/config.json`.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md)
- [Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk)
- [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)