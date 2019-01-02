---
title: Configurare un ambiente di sviluppo Python
titleSuffix: Azure Machine Learning service
description: Informazioni su come configurare un ambiente di sviluppo quando si usa il servizio Azure Machine Learning. Questo documento illustra come usare ambienti Conda, creare file di configurazione e configurare Jupyter Notebook, Azure Notebooks, ambienti IDE, editor di codice e Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 59f847dc38cddfd9185cfd169cf1ef34c744f8f7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53192635"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurare un ambiente di sviluppo per Azure Machine Learning

Questo documento contiene informazioni su come configurare un ambiente di sviluppo per poter usare il servizio Azure Machine Learning. Il servizio di Azure Machine Learning è indipendente dalla piattaforma. Gli unici requisiti per l'ambiente di sviluppo sono __Python 3__, __Conda__ (per gli ambienti di tipo isolato) e un file di configurazione che contiene le informazioni sull'area di lavoro di Azure Machine Learning.

Questo documento è incentrato sugli strumenti e ambienti specifici seguenti:

* [Azure Notebook](#aznotebooks): un servizio di Jupyter Notebook ospitato nel cloud di Azure. Si tratta del modo __più semplice__ per iniziare, perché l'SDK di Azure Machine Learning è già installato.
* [Data Science Virtual Machine](#dsvm): un __ambiente di sviluppo/sperimentazione preconfigurato__ nel cloud di Azure che è stato __progettato per l'ambito data science__ e può essere distribuito in istanze di macchina virtuale con sola CPU o basate su GPU. Python 3, Conda, Jupyter Notebook e SDK di Azure Machine Learning sono già installati. La macchina virtuale viene fornita con i framework, gli strumenti e gli editor di Machine Learning/Deep Learning più diffusi per lo sviluppo di soluzioni di Machine Learning. Si tratta probabilmente dell'ambiente di sviluppo __più completo__ di Machine Learning nella piattaforma Azure.
* [Jupyter Notebook](#jupyter): se si usa già Jupyter Notebook, l'SDK include alcune funzionalità aggiuntive che è consigliabile installare.
* [Visual Studio Code](#vscode): se si usa Visual Studio Code, sono disponibili alcune estensioni utili che è possibile installare.
* [Azure Databricks](#aml-databricks): una piattaforma diffusa per l'analisi dei dati basata su Apache Spark. Leggere le informazioni su come installare l'SDK di Azure Machine Learning nel cluster in modo da poter distribuire i modelli.

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

La Data Science Virtual Machine (DSVM) è un'immagine di macchina virtuale personalizzata **progettata per operare in ambito data science** che è preconfigurata con:

  - Pacchetti come Tensorflow, Pytorch, scikit-learn, Xgboost e l'SDK di Azure Machine Learning
  - Strumenti di data science comuni, ad esempio Spark Standalone e Drill
  - Strumenti di Azure, ad esempio l'interfaccia della riga di comando, Azcopy e Storage Explorer
  - Ambienti di sviluppo integrato (IDE) come Visual Studio Code, PyCharm e RStudio
  - Server Jupyter Notebook 

L'SDK di Azure Machine Learning funziona nella versione Windows o Ubuntu della Data Science Virtual Machine. Per usare la Data Science Virtual Machine come ambiente di sviluppo, procedere come segue:

1. Per creare una Data Science Virtual Machine, adottare una delle soluzioni seguenti:

    * Uso del portale di Azure:

        * [Creare una Data Science Virtual Machine per __Ubuntu__](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Creare una Data Science Virtual Machine per __Windows__](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Uso dell'interfaccia della riga di comando di Azure:

        > [!IMPORTANT]
        > Quando si usa l'interfaccia della riga di comando di Azure, accedere alla sottoscrizione di Azure tramite il comando `az login`.
        >
        > Quando si usano i comandi in questo passaggio, è necessario specificare il nome di un gruppo di risorse, un nome per la macchina virtuale, un nome utente e una password.

        * Per creare una Data Science Virtual Machine per __Ubuntu__, usare il comando seguente:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Per creare una Data Science Virtual Machine per __Windows__, usare il comando seguente:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. SDK di Azure Machine Learning è **già installato** nella DSVM. Per usare l'ambiente Conda contenente il SDK, usare uno dei seguenti comandi:

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

1. Per configurare la DSVM in modo da usare la propria area di lavoro del servizio Azure Machine Learning, vedere la sezione [Configura area di lavoro](#workspace).

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

1. Per installare un server Jupyter Notebook compatibile con Conda, usare il comando seguente:

    ```shell
    # install Jupyter
    conda install nb_conda
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

1. Per installare l'estensione di Azure Machine Learning per Visual Studio Code, vedere la pagina [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Per altre informazioni, vedere [Introduzione ad Azure Machine Learning per Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

È possibile usare una versione personalizzata dell'SDK di Azure Machine Learning per Azure Databricks per l'apprendimento automatico personalizzato end-to-end. In alternativa, eseguire il training del modello all'interno di Databricks e usare [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code) per distribuire il modello.

Per preparare il cluster Databricks e ottenere notebook di esempio:

1. Creare un [cluster Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) con una versione di runtime di Databricks 4.x (preferibilmente a concorrenza elevata) con **Python 3**. 

1. Creare una libreria per [installare e collegare](https://docs.databricks.com/user-guide/libraries.html#create-a-library) nel cluster il pacchetto PyPi `azureml-sdk[databricks]` dell'SDK di Azure Machine Learning per Python. Al termine dell'operazione, si vedrà la libreria collegata come illustrato in questa immagine. Tenere presenti questi [problemi noti di Databricks](resource-known-issues.md#databricks).

   ![SDK installato in Databricks ](./media/how-to-azure-machine-learning-on-databricks/sdk-installed-on-databricks.jpg)

   Se questo passaggio non riesce, riavviare il cluster:
   1. Selezionare `Clusters` nel riquadro a sinistra. Selezionare il nome del cluster nella tabella. 
   1. Nella scheda `Libraries` selezionare `Restart`.

1. Scaricare il [file dell'archivio di notebook di Azure Databricks/SDK di Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/databricks/Databricks_AMLSDK_github.dbc).

   >[!Warning]
   > Molti notebook di esempio sono disponibili per l'uso con il servizio Azure Machine Learning. Solo questi notebook di esempio funzionano con Azure Databricks: https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks

1.  [Importare questo file di archivio](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) nel cluster Databricks e iniziare a esplorare come [descritto qui](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks).


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
