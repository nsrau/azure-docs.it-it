---
title: Configurare un ambiente di sviluppo Python
titleSuffix: Azure Machine Learning service
description: Informazioni su come configurare un ambiente di sviluppo quando si usa il servizio Azure Machine Learning. Questo articolo illustra come usare ambienti Conda, creare file di configurazione e configurare Jupyter Notebook, Azure Notebooks, Azure Databricks, ambienti IDE, editor di codice e la Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 02/24/2019
ms.custom: seodec18
ms.openlocfilehash: 6a51e57cfac326663d41b545c9f2883a446467d3
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340763"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurare un ambiente di sviluppo per Azure Machine Learning

Questo articolo contiene informazioni su come configurare un ambiente di sviluppo per poter usare il servizio Azure Machine Learning. Il servizio Machine Learning è indipendente dalla piattaforma.

I requisiti soli per l'ambiente di sviluppo sono Python 3, Anaconda (per gli ambienti isolati) e un file di configurazione che contiene le informazioni sull'area di lavoro di Azure Machine Learning.

Questo articolo è incentrato sugli strumenti e ambienti seguenti:

* Azure Notebooks: un servizio di Jupyter Notebook ospitato nel cloud di Azure. Si tratta del modo più semplice per iniziare perché l'SDK di Azure Machine Learning è già installato.

* [Data Science Virtual Machine (DSVM)](#dsvm): un ambiente di sviluppo o sperimentazione preconfigurato nel cloud di Azure che è stato progettato per l'ambito data science e può essere distribuito in istanze di macchina virtuale con sola CPU o basate su GPU. Python 3, Conda, Jupyter Notebook e l'SDK di Azure Machine Learning sono già installati. La macchina virtuale viene fornita con i framework, gli strumenti e gli editor di Machine Learning e Deep Learning più diffusi per lo sviluppo di soluzioni di Machine Learning. Si tratta probabilmente dell'ambiente di sviluppo di Machine Learning più completo nella piattaforma Azure.

* [Jupyter Notebook](#jupyter): se si usa già Jupyter Notebook, l'SDK include alcune funzionalità aggiuntive che è consigliabile installare.

* [Visual Studio Code](#vscode): se si usa Visual Studio Code, sono disponibili alcune estensioni utili che è possibile installare.

* [Azure Databricks](#aml-databricks): una piattaforma diffusa per l'analisi dei dati basata su Apache Spark. Leggere le informazioni su come installare l'SDK di Azure Machine Learning nel cluster in modo da poter distribuire i modelli.

Se si dispone già di un ambiente Python 3, o si vogliono solo conoscere i passaggi di base per l'installazione dell'SDK, vedere la sezione [Computer locale](#local).

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro del servizio Azure Machine Learning. Per creare l'area di lavoro, vedere [Iniziare a usare il servizio Azure Machine Learning](quickstart-get-started.md).

- Entrambi i [Anaconda](https://www.anaconda.com/download/) oppure [Miniconda](https://conda.io/miniconda.html) Gestione pacchetti.

    > [!IMPORTANT]
    > Quando si usa Azure Notebooks, Anaconda e Miniconda non sono necessari.

- In Linux o macOS è necessaria la shell bash.

    > [!TIP]
    > Se si usa Linux o macOS con una shell diversa da bash, ad esempio zsh, possono verificarsi errori durante l'esecuzione di alcuni comandi. Per risolvere questo problema, usare il comando `bash` per avviare una nuova shell bash ed eseguire i comandi nella shell.

- In Windows è necessario il prompt dei comandi o il prompt di Anaconda (installato per Anaconda e Miniconda).

## <a id="aznotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (anteprima) è un ambiente di sviluppo interattivo nel cloud di Azure. Si tratta del modo più semplice per iniziare le attività di sviluppo di Azure Machine Learning.

* L'SDK di Azure Machine Learning è già installato.
* Dopo aver creato un'area di lavoro del servizio Azure Machine Learning nel portale di Azure, è possibile fare clic su un pulsante per configurare automaticamente l'ambiente di Azure Notebooks per usare l'area di lavoro.

Per iniziare a sviluppare con Azure Notebooks, vedere [Introduzione al servizio Azure Machine Learning](quickstart-get-started.md).

Per impostazione predefinita, Azure Notebooks usa un livello di servizio gratuito limitato a 4 GB di memoria e 1 GB di dati. È, tuttavia, possibile rimuovere questi limiti mediante l'aggiunta di un'istanza di Data Science Virtual Machine al progetto Azure Notebooks. Per altre informazioni, consultare [Manage and configure Azure Notebooks projects - Compute tier (Gestire e configurare i progetti Azure Notebooks - Livello di calcolo)](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a id="dsvm"></a>Data Science Virtual Machine

La Data Science Virtual Machine (DSVM) è un'immagine di macchina virtuale personalizzata. È stata progettata per operazioni di data science che sono preconfigurate con:

  - Pacchetti come TensorFlow, PyTorch, Scikit-learn, XGBoost e SDK di Azure Machine Learning
  - Strumenti di data science comuni come Spark Standalone e Drill
  - Strumenti di Azure come l'interfaccia della riga di comando di Azure, AzCopy e Storage Explorer
  - Ambienti di sviluppo integrato (IDE) come Visual Studio Code e PyCharm
  - Server Jupyter Notebook

L'SDK di Azure Machine Learning funziona nella versione della DSVM per Windows o Ubuntu. Ma se si prevede di usare DSVM anche come una destinazione di calcolo, è supportato solo Ubuntu.

Per usare la DSVM come ambiente di sviluppo, eseguire queste operazioni:

1. Creare una DSVM in uno degli ambienti seguenti:

    * Il portale di Azure:

        * [Creare una Data Science Virtual Machine per Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Creare una Data Science Virtual Machine per Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * L'interfaccia della riga di comando di Azure:

        > [!IMPORTANT]
        > * Quando si usa l'interfaccia della riga di comando di Azure, accedere alla sottoscrizione di Azure tramite il comando `az login`.
        >
        > * Quando si usano i comandi in questo passaggio, è necessario specificare il nome di un gruppo di risorse, un nome per la macchina virtuale, un nome utente e una password.

        * Per creare una Data Science Virtual Machine per Ubuntu, usare il comando seguente:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Per creare una Data Science Virtual Machine per Windows, usare il comando seguente:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. L'SDK di Azure Machine Learning è già installato nella DSVM. Per usare l'ambiente Conda contenente l'SDK, usare uno dei seguenti comandi:

    * Per la DSVM Ubuntu:

        ```shell
        conda activate py36
        ```

    * Per la DSVM Windows:

        ```shell
        conda activate AzureML
        ```

1. Per verificare che sia possibile accedere all'SDK e controllare la versione, usare il codice Python seguente:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Per configurare la DSVM per l'uso dell'area di lavoro del servizio Azure Machine Learning, vedere la sezione [Creare un file di configurazione dell'area di lavoro](#workspace).

Per altre informazioni, vedere [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Computer locale

Quando si usa un computer locale (che potrebbe anche essere una macchina virtuale remota), creare un ambiente Anaconda e installare il SDK eseguendo queste operazioni:

1. Scaricare e installare [Anaconda](https://www.anaconda.com/distribution/#download-section) (versione 3.7 Python) se non si ha già.

1. Aprire un prompt dei comandi di Anaconda e creare un ambiente con i comandi seguenti:

    Eseguire il comando seguente per creare l'ambiente.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Quindi attivare l'ambiente.

    ```shell
    conda activate myenv
    ```

    Questo esempio viene creato un ambiente che usa python 3.6.5, ma è possibile scegliere qualsiasi subversions specifico. Potrebbe non essere garantita la compatibilità SDK con determinate versioni principali (3.5 + è consigliato), e si consiglia di provare una versione diversa o subversion nel proprio ambiente Anaconda, se si verificano errori. Per creare l'ambiente possono essere necessari diversi minuti mentre vengono scaricati componenti e pacchetti.

1. Eseguire i comandi seguenti nell'ambiente di nuovo per abilitare i kernel ipython specifici dell'ambiente. Ciò garantisce che pacchetto e il kernel previsto importare il comportamento quando si lavora con i notebook di Jupyter all'interno di ambienti Anaconda:

    ```shell
    conda install notebook ipykernel
    ```

    Eseguire quindi il comando seguente per creare il kernel:

    ```shell
    ipython kernel install --user
    ```

1. Usare i comandi seguenti per installare i pacchetti:

    Questo comando Installa SDK di base Azure Machine Learning con notebook e automl funzionalità aggiuntive. Il `automl` aggiuntivo è un'installazione di grandi dimensioni e può essere rimossa dalla parentesi se non si prevede di eseguire esperimenti di apprendimento automatico automatizzati. Il `automl` aggiuntivo include anche il SDK di Azure Machine Learning Data Prep per impostazione predefinita come dipendenza.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

    Usare questo comando per installare il SDK di Azure Machine Learning Data Prep di per sé:

    ```shell
    pip install azureml-dataprep
    ```

   > [!NOTE]
   > Se si riceve un messaggio che indica che PyYAML non può essere installato, usare in alternativa il comando seguente:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Richiederà alcuni minuti per installare il SDK. Vedere le [Guida all'installazione](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) per altre informazioni sulle opzioni di installazione.

1. Installare altri pacchetti per il machine learning sperimentazione.

    Utilizzare uno dei comandi seguenti e sostituire  *\<nuovo pacchetto >* con il pacchetto da installare. Installazione dei pacchetti tramite `conda install` richiede che il pacchetto fa parte dei canali correnti (è possibile aggiungere nuovi canali nel Cloud di Anaconda).

    ```shell
    conda install <new package>
    ```

    In alternativa, è possibile installare i pacchetti tramite `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter Notebook

I notebook di Jupyter fanno parte del [progetto Jupyter](https://jupyter.org/). Forniscono un'esperienza di codifica interattiva in cui si creano documenti che combinano codice attivo con testi narrativi e grafica. Anche i notebook di Jupyter sono un'ottima soluzione per condividere i risultati con altri utenti perché è possibile salvare l'output delle sezioni di codice nel documento. È possibile installare Jupyter Notebook in un'ampia gamma di piattaforme.

La procedura descritta nel [computer locale](#local) sezione Installa i componenti necessari per eseguire i notebook di Jupyter in un ambiente Anaconda. Per abilitare questi componenti nell'ambiente di Jupyter Notebook, seguire questa procedura:

1. Aprire un prompt dei comandi di Anaconda e attivare l'ambiente.

    ```shell
    conda activate myenv
    ```

1. Avviare il server Jupyter Notebook con il comando seguente:

    ```shell
    jupyter notebook
    ```

1. Per verificare che i Notebook di Jupyter è possibile usare il SDK, creare un **New** notebook, selezionare **Python 3** come kernel e quindi eseguire il comando seguente in una cella notebook:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Se si verificano problemi di importazione dei moduli e viene visualizzato un `ModuleNotFoundError`, assicurarsi che il kernel Jupyter sia connesso al percorso corretto per l'ambiente eseguendo il codice seguente in una cella Notebook.

    ```python
    import sys
    sys.path
    ```

1. Per configurare Jupyter Notebook per l'uso dell'area di lavoro del servizio Azure Machine Learning, vedere la sezione [Creare un file di configurazione dell'area di lavoro](#workspace).

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code è un editor di codice multipiattaforma. Si basa su un'installazione locale di Python 3 e Conda per il supporto di Python, ma fornisce strumenti aggiuntivi per l'uso con l'intelligenza artificiale. Offre inoltre il supporto per la selezione dell'ambiente Conda all'interno dell'editor di codice.

Per usare Visual Studio Code per lo sviluppo, seguire questa procedura:

1. Per informazioni su come usare Visual Studio Code per lo sviluppo in Python, vedere [Get started with Python in VSCode](https://code.visualstudio.com/docs/python/python-tutorial) (Introduzione a Python in Visual Studio Code).

1. Per selezionare l'ambiente Conda, aprire Visual Studio Code e quindi usare CTRL+MAIUSC+P (Linux e Windows) o Comando+MAIUSC+P (Mac).
    Viene visualizzato il __riquadro comandi__.

1. Immettere __Python: Select Interpreter__ (Python: Seleziona interprete) e quindi selezionare l'ambiente Conda.

1. Per verificare che sia possibile usare l'SDK, creare ed eseguire un nuovo file di Python (.py) che contiene il codice seguente:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Per installare l'estensione di Azure Machine Learning per Visual Studio Code, vedere [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Per altre informazioni, vedere [Introduzione ad Azure Machine Learning per Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks è un ambiente basato su Apache Spark nel cloud di Azure. Fornisce un ambiente collaborativo basato su Notebook con cluster di calcolo basato su CPU o GPU.

Funzionamento Azure Databricks con il servizio di Azure Machine Learning:
+ È possibile eseguire il training di un modello con MLlib di Spark e distribuirlo in ACI/AKS da Azure Databricks. 
+ È anche possibile usare [automatizzata apprendimento](concept-automated-ml.md) funzionalità in una speciale Machine Learning di Azure SDK con Azure Databricks.
+ È possibile usare Azure Databricks come una destinazione di calcolo da un' [pipeline di Azure Machine Learning](concept-ml-pipelines.md). 

### <a name="set-up-your-databricks-cluster"></a>Configurare il cluster Databricks

Creare un [cluster Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Alcune impostazioni si applicano solo se si installa il SDK per automatizzati di machine learning in Databricks.
**Richiederà alcuni minuti per creare il cluster.**

Usare queste impostazioni:

| Impostazione |Si applica a| Valore |
|----|---|---|
| Nome cluster |sempre| nomecluster |
| Databricks Runtime |sempre| Qualsiasi runtime non ML (non ML 4.x, 5.x) |
| Versione Python |sempre| 3 |
| Ruoli di lavoro |sempre| almeno 2 |
| Worker node VM types (Tipi di VM dei nodi di ruolo di lavoro) <br>(determina il numero massimo di iterazioni simultanee) |Funzionalità automatiche di Machine Learning<br>Solo| È preferibile una macchina virtuale ottimizzata per la memoria |
| Enable Autoscaling (Abilita la scalabilità automatica) |Funzionalità automatiche di Machine Learning<br>Solo| Deselezionare |

Attendere che il cluster sia in esecuzione prima di proseguire.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Installare il SDK corretto in una libreria di Databricks
Quando il cluster è in esecuzione, [creare una libreria](https://docs.databricks.com/user-guide/libraries.html#create-a-library) per collegare il pacchetto SDK di Azure Machine Learning appropriato per il cluster. 

1. Scegli **sola** opzione (nessun altra installazione di SDK sono supportati)

   |SDK&nbsp;pacchetto&nbsp;funzionalità aggiuntive|Source (Sorgente)|PyPi&nbsp;Name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Per Databricks| Caricare Python Egg o PyPI | azureml-sdk[databricks]|
   |Per Databricks - with-<br> funzionalità di Machine Learning automatizzate| Caricare Python Egg o PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > È possibile installare nessun altra funzionalità aggiuntive SDK. Scegliere solo una delle opzioni precedenti [databricks] o [automl_databricks].

   * Non si seleziona **Collega automaticamente a tutti i cluster**.
   * Selezionare **Attach** accanto al nome del cluster.

1. Monitoraggio per gli errori fino a quando lo stato diventa **Attached**, l'operazione potrebbe richiedere alcuni minuti.  Se questo passaggio non riesce, verificare quanto segue: 

   Provare a riavviare il cluster tramite:
   1. Nel riquadro a sinistra selezionare **Cluster**.
   1. Nella tabella selezionare il nome del cluster.
   1. Nella scheda **Librerie** selezionare **Riavvia**.
      
   Si consideri inoltre:
   + Alcuni pacchetti, come `psutil`, possono causare conflitti di Databricks durante l'installazione. Per evitare tali errori, installare i pacchetti dalle versioni lib del blocco, ad esempio `pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0`. 
   + In alternativa, se si dispone di una versione precedente del SDK, deselezionarla dalle librerie installate del cluster e spostare nel Cestino. Installare la nuova versione dell'SDK e riavviare il cluster. Se successivamente si verifica un problema, scollegare e ricollegare il cluster.

Se l'installazione ha esito positivo, la libreria importata dovrebbe essere simile a uno dei seguenti:
   
SDK per Databricks **_senza_** automatizzata apprendimento ![Azure Machine Learning SDK per Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK per Databricks **WITH** automatizzata apprendimento ![SDK con automated apprendimento installato in Databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Iniziare a esplorare

Prova pratica:
+ Scaricare il [file di archivio dei blocchi appunti](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) per SDK di Azure Databricks, Azure Machine Learning e [importare il file di archivio](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) nel cluster Databricks.  
  Anche se sono disponibili molti notebook di esempio **soltanto [questi esempi di notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) funzionano con Azure Databricks.**
  
+ Informazioni su come [creare una pipeline con Databricks come le risorse di calcolo di training](how-to-create-your-first-pipeline.md).

## <a id="workspace"></a>Creare un file di configurazione dell'area di lavoro

Il file di configurazione dell'area di lavoro è un file JSON che indica all'SDK come comunicare con l'area di lavoro del servizio Azure Machine Learning. Il file è denominato *config.json* e ha il formato seguente:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Questo file JSON deve trovarsi nella struttura della directory che contiene gli script di Python o i notebook di Jupyter. Può essere nella stessa directory, in una sottodirectory denominata *aml_config* o in una directory padre.

Per usare questo file dal codice, usare `ws=Workspace.from_config()`. Questo codice carica le informazioni dal file e si connette all'area di lavoro.

È possibile creare il file di configurazione in tre modi:

* **Seguire l'[avvio rapido per iniziare a usare Azure Machine Learning](quickstart-get-started.md)**: nella libreria di Azure Notebooks viene creato un file *config.json* che contiene le informazioni di configurazione per l'area di lavoro. È possibile scaricare o copiare il file *config.json* in altri ambienti di sviluppo.

* **Creare il file manualmente**: per la creazione manuale si usa un editor di testo. È possibile trovare i valori da inserire nel file di configurazione visitando l'area di lavoro nel [portale di Azure](https://portal.azure.com). Copiare i valori relativi al nome dell'area di lavoro, al gruppo di risorse e all'ID sottoscrizione e inserirli nel file di configurazione.

     ![Portale di Azure](./media/how-to-configure-environment/configure.png)

* **Creare il file a livello di codice**: nel frammento di codice seguente si stabilisce una connessione a un'area di lavoro specificando l'ID sottoscrizione, il gruppo di risorse e il nome dell'area di lavoro. La configurazione dell'area di lavoro viene quindi salvata nel file:

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

    Questo codice scrive il file di configurazione nel file *aml_config/config.json*.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training di un modello](tutorial-train-models-with-aml.md) in Azure Machine Learning con il set di dati MNIST
- Vedere la documentazione di riferimento di [Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk)
- Vedere le informazioni su [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)
