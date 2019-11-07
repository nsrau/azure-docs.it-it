---
title: Configurare un ambiente di sviluppo Python
titleSuffix: Azure Machine Learning
description: Informazioni su come configurare l'ambiente di sviluppo per Azure Machine Learning. Usare ambienti conda, creare file di configurazione e configurare il proprio server notebook basato sul cloud, notebook Jupyter, Azure Databricks, IDE, editor di codice e la Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 19045b54b97fdb69f9fdab3d17066faa5dbcc435
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580727"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurare un ambiente di sviluppo per Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come configurare un ambiente di sviluppo per l'uso con Azure Machine Learning. Azure Machine Learning è indipendente dalla piattaforma. L'unico requisito difficile per l'ambiente di sviluppo è Python 3. È consigliabile anche un ambiente isolato come Anaconda o virtualenv.

La tabella seguente illustra tutti gli ambienti di sviluppo trattati in questo articolo, oltre a vantaggi e svantaggi.

| Environment | Vantaggi | Svantaggi |
| --- | --- | --- |
| [VM Azure Machine Learning notebook basata sul cloud](#notebookvm) | Modo più semplice per iniziare. L'intero SDK è già installato nella macchina virtuale dell'area di lavoro e le esercitazioni sui notebook sono pre-clonate e pronte per l'esecuzione. | Mancanza di controllo sull'ambiente di sviluppo e sulle dipendenze. Costi aggiuntivi per le VM Linux (è possibile arrestare la macchina virtuale quando non è in uso per evitare addebiti). Vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Ambiente locale](#local) | Controllo completo dell'ambiente di sviluppo e delle dipendenze. Eseguire con qualsiasi strumento di compilazione, ambiente o IDE di propria scelta. | Per iniziare, è necessario più tempo. È necessario installare i pacchetti SDK necessari ed è necessario installare anche un ambiente, se non è già presente. |
| [Azure Databricks](#aml-databricks) | Ideale per l'esecuzione di flussi di lavoro di Machine Learning intensivi su larga scala sulla piattaforma Apache Spark scalabile. | Overkill per l'apprendimento automatico sperimentale o per esperimenti e flussi di lavoro su scala ridotta. Costi aggiuntivi per Azure Databricks. Vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/databricks/). |
| [Il Data Science Virtual Machine (DSVM)](#dsvm) | Analogamente alla macchina virtuale notebook basata sul cloud (Python e SDK sono preinstallati), ma con gli strumenti di data science e di apprendimento automatico più diffusi preinstallati. Facile da scalare e combinare con altri strumenti e flussi di lavoro personalizzati. | Un'esperienza introduttiva più lenta rispetto alla VM notebook basata sul cloud. |


Questo articolo fornisce anche suggerimenti sull'utilizzo aggiuntivi per gli strumenti seguenti:

* [Notebook di Jupyter](#jupyter): se si sta già usando il Jupyter notebook, l'SDK include alcuni extra che è necessario installare.

* [Visual Studio Code](#vscode): se si usa Visual Studio Code, l' [estensione Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) include il supporto completo del linguaggio per Python, oltre a funzionalità che consentono di lavorare con la Azure Machine Learning molto più comoda e produttiva.

## <a name="prerequisites"></a>Prerequisiti

Un'area di lavoro di Azure Machine Learning. Per creare l'area di lavoro, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md). Un'area di lavoro è sufficiente per iniziare a usare il proprio [Server notebook basato sul cloud](#notebookvm), un [DSVM](#dsvm)o [Azure Databricks](#aml-databricks).

Per installare l'ambiente SDK per il [computer locale](#local), [Jupyter Notebook server](#jupyter) o [Visual Studio Code](#vscode) sono necessari anche:

- Gestione pacchetti [Anaconda](https://www.anaconda.com/download/) o [Miniconda](https://conda.io/miniconda.html) .

- In Linux o macOS è necessaria la shell bash.

    > [!TIP]
    > Se si usa Linux o macOS con una shell diversa da bash, ad esempio zsh, possono verificarsi errori durante l'esecuzione di alcuni comandi. Per risolvere questo problema, usare il comando `bash` per avviare una nuova shell bash ed eseguire i comandi nella shell.

- In Windows è necessario il prompt dei comandi o il prompt di Anaconda (installato per Anaconda e Miniconda).

## <a id="notebookvm"></a>VM notebook basata sul cloud

La VM Azure Machine Learning notebook è una workstation di Azure sicura basata sul cloud che fornisce ai data scientist un server Jupyter notebook, JupyterLab e un ambiente ML completamente preparato.

La VM del notebook è:

Non sono presenti elementi da installare o configurare per un'istanza di calcolo.  Crearne uno in qualsiasi momento dall'area di lavoro Azure Machine Learning. Fornire un solo nome e specificare un tipo di macchina virtuale di Azure. Provare ora con questa [esercitazione: configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md).


Per arrestare i costi di calcolo, [arrestare la VM del notebook](tutorial-1st-experiment-sdk-train.md#clean-up-resources).

## <a id="dsvm"></a>Data Science Virtual Machine

La Data Science Virtual Machine (DSVM) è un'immagine di macchina virtuale personalizzata. È stata progettata per operazioni di data science che sono preconfigurate con:

  - Pacchetti come TensorFlow, PyTorch, Scikit-learn, XGBoost e SDK di Azure Machine Learning
  - Strumenti di data science comuni come Spark Standalone e Drill
  - Strumenti di Azure come l'interfaccia della riga di comando di Azure, AzCopy e Storage Explorer
  - Ambienti di sviluppo integrato (IDE) come Visual Studio Code e PyCharm
  - Server Jupyter Notebook

L'SDK di Azure Machine Learning funziona nella versione della DSVM per Windows o Ubuntu. Ma se si prevede di usare DSVM anche come una destinazione di calcolo, è supportato solo Ubuntu.

Per usare DSVM come ambiente di sviluppo:

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

1. Per configurare DSVM per l'uso dell'area di lavoro Azure Machine Learning, vedere la sezione [creare un file di configurazione dell'area di lavoro](#workspace) .

Per altre informazioni, vedere [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Computer locale

Quando si usa un computer locale (che potrebbe anche essere una macchina virtuale remota), creare un ambiente Anaconda e installare l'SDK. Ad esempio:

1. Scaricare e installare [Anaconda](https://www.anaconda.com/distribution/#download-section) (versione Python 3,7) se non è già presente.

1. Aprire un prompt di Anaconda e creare un ambiente con i comandi seguenti:

    Eseguire il comando seguente per creare l'ambiente.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Quindi attivare l'ambiente.

    ```shell
    conda activate myenv
    ```

    Questo esempio crea un ambiente con Python 3.6.5, ma è possibile scegliere eventuali sottoversioni specifiche. È possibile che la compatibilità con SDK non sia garantita con alcune versioni principali (sono consigliate 3.5 +) ed è consigliabile provare una versione/Subversion diversa nell'ambiente Anaconda se si verificano errori. Per creare l'ambiente possono essere necessari diversi minuti mentre vengono scaricati componenti e pacchetti.

1. Eseguire i comandi seguenti nel nuovo ambiente per abilitare i kernel IPython specifici dell'ambiente. In questo modo si garantisce il comportamento previsto per l'importazione di kernel e pacchetti quando si usano notebook Jupyter negli ambienti Anaconda:

    ```shell
    conda install notebook ipykernel
    ```

    Eseguire quindi il comando seguente per creare il kernel:

    ```shell
    ipython kernel install --user
    ```

1. Usare i comandi seguenti per installare i pacchetti:

    Questo comando installa il Azure Machine Learning SDK di base con notebook e `automl` extra. Il `automl` extra è un'installazione di grandi dimensioni e può essere rimosso dalle parentesi quadre se non si intende eseguire esperimenti automatici di machine learning. Il `automl` aggiuntivo include anche Azure Machine Learning data Prep SDK per impostazione predefinita come dipendenza.

    ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Se si riceve un messaggio che indica che PyYAML non può essere installato, usare in alternativa il comando seguente:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * A partire da macOS Catalina, zsh (Z Shell) è la shell di accesso predefinita e la shell interattiva. In zsh usare il comando seguente che consente di eseguire l'escape delle parentesi quadre con "\\" (barra rovesciata):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`


   L'installazione dell'SDK può richiedere diversi minuti. Per ulteriori informazioni sulle opzioni di installazione, vedere la [Guida](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)all'installazione.

1. Installare altri pacchetti per la sperimentazione di machine learning.

    Usare uno dei comandi seguenti e sostituire *\<nuovo pacchetto >* con il pacchetto che si vuole installare. Per l'installazione di pacchetti tramite `conda install` è necessario che il pacchetto faccia parte dei canali correnti. i nuovi canali possono essere aggiunti nel cloud Anaconda.

    ```shell
    conda install <new package>
    ```

    In alternativa, è possibile installare i pacchetti tramite `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Notebook Jupyter

I notebook di Jupyter fanno parte del [progetto Jupyter](https://jupyter.org/). Forniscono un'esperienza di codifica interattiva in cui si creano documenti che combinano codice attivo con testi narrativi e grafica. Anche i notebook di Jupyter sono un'ottima soluzione per condividere i risultati con altri utenti perché è possibile salvare l'output delle sezioni di codice nel documento. È possibile installare Jupyter Notebook in un'ampia gamma di piattaforme.

La procedura nella sezione [computer locale](#local) installa i componenti necessari per l'esecuzione di notebook di Jupyter in un ambiente Anaconda.

Per abilitare questi componenti nell'ambiente Jupyter Notebook:

1. Aprire un prompt di Anaconda e attivare l'ambiente.

    ```shell
    conda activate myenv
    ```

1. Clonare [il repository GitHub](https://aka.ms/aml-notebooks) per un set di notebook di esempio.

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Avviare il server di Jupyter Notebook con il comando seguente:

    ```shell
    jupyter notebook
    ```

1. Per verificare che Jupyter Notebook possa usare l'SDK, creare un **nuovo** notebook, selezionare **Python 3** come kernel, quindi eseguire il comando seguente in una cella del notebook:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Se si verificano problemi durante l'importazione di moduli e si riceve una `ModuleNotFoundError`, assicurarsi che il kernel Jupyter sia connesso al percorso corretto per l'ambiente eseguendo il codice seguente in una cella del notebook.

    ```python
    import sys
    sys.path
    ```

1. Per configurare la Jupyter Notebook per l'uso dell'area di lavoro Azure Machine Learning, passare alla sezione [creare un file di configurazione dell'area di lavoro](#workspace) .


### <a id="vscode"></a>Visual Studio Code

Visual Studio Code è un editor di codice multipiattaforma molto diffuso che supporta una vasta gamma di linguaggi di programmazione e strumenti tramite le estensioni disponibili in [Visual Studio Marketplace](https://marketplace.visualstudio.com/vscode). L' [estensione Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) installa l' [estensione Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) per la codifica in tutti i tipi di ambienti Python (virtuali, Anaconda e così via). Fornisce inoltre funzionalità utili per l'utilizzo di risorse Azure Machine Learning e l'esecuzione di Azure Machine Learning esperimenti senza uscire da Visual Studio Code.

Per usare Visual Studio Code per lo sviluppo:

1. Installare l'estensione Azure Machine Learning per Visual Studio Code, vedere [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Per altre informazioni, vedere [Introduzione ad Azure Machine Learning per Visual Studio Code](how-to-vscode-tools.md).

1. Per informazioni su come usare Visual Studio Code per qualsiasi tipo di sviluppo Python, vedere [Introduzione a Python in VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

    - Per selezionare l'ambiente SDK Python contenente l'SDK, aprire VS Code, quindi premere CTRL + MAIUSC + P (Linux e Windows) o comando + MAIUSC + P (Mac).
        - Verrà aperto il __riquadro comandi__ .

    - Immettere __Python: selezionare interprete__e quindi selezionare l'ambiente appropriato.

1. Per convalidare che è possibile usare l'SDK, creare un nuovo file Python (con estensione py) che contenga il codice seguente:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Eseguire questo codice facendo clic sul CodeLens "Esegui cella" o semplicemente premere MAIUSC + INVIO.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks è un ambiente basato su Apache Spark nel cloud di Azure. Offre un ambiente di collaborazione basato su notebook con CPU o cluster di calcolo basato su GPU.

Funzionamento di Azure Databricks con Azure Machine Learning:
+ È possibile eseguire il training di un modello usando Spark MLlib e distribuire il modello in ACI/AKS dall'interno Azure Databricks.
+ È anche possibile usare le funzionalità [automatiche di Machine Learning](concept-automated-ml.md) in un SDK di Azure ml speciale con Azure Databricks.
+ È possibile usare Azure Databricks come destinazione di calcolo da una [pipeline di Azure Machine Learning](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Configurare il cluster databricks

Creare un [cluster databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Alcune impostazioni si applicano solo se si installa l'SDK per Machine Learning automatico in databricks.
**La creazione del cluster potrebbe richiedere alcuni minuti.**

Usare le impostazioni seguenti:

| Impostazione |Si applica a| Valore |
|----|---|---|
| Nome del cluster |Sempre| nomecluster |
| Databricks Runtime |Sempre|Runtime non ML 6,0 (scala 2,11, Spark 2.4.3) |
| Versione Python |Sempre| 3 |
| Ruoli di lavoro |Sempre| almeno 2 |
| Worker node VM types (Tipi di VM dei nodi di ruolo di lavoro) <br>(determina il numero massimo di iterazioni simultanee) |Funzionalità automatiche di Machine Learning<br>solo| È preferibile una macchina virtuale ottimizzata per la memoria |
| Enable Autoscaling (Abilita la scalabilità automatica) |Funzionalità automatiche di Machine Learning<br>solo| Deselezionare |

Attendere che il cluster sia in esecuzione prima di proseguire.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Installare l'SDK corretto in una libreria databricks
Quando il cluster è in esecuzione, [creare una libreria](https://docs.databricks.com/user-guide/libraries.html#create-a-library) per alleghi il pacchetto SDK Azure Machine Learning appropriato al cluster.

1. Fare clic con il pulsante destro del mouse sulla cartella dell'area di lavoro corrente in cui si desidera archiviare la libreria. Selezionare **crea** > **libreria**.

1. Scegliere **una sola** opzione (non sono supportate altre installazioni SDK)

   |Pacchetto di&nbsp;SDK&nbsp;funzionalità aggiuntive|Source|PyPi&nbsp;nome&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Per databricks| Caricare Python Egg o PyPI | azureml-sdk[databricks]|
   |Per databricks-con-<br> funzionalità di Machine Learning automatiche| Caricare Python Egg o PyPI | azureml-SDK [automl]|

   > [!Warning]
   > Non è possibile installare altri SDK aggiuntivi. Scegliere solo una delle opzioni precedenti [databricks] o [automl].

   * Non selezionare **Connetti automaticamente a tutti i cluster**.
   * Selezionare **Connetti** accanto al nome del cluster.

1. Consente di monitorare gli errori fino a quando lo stato non diventa **collegato**, operazione che può richiedere alcuni minuti.  Se questo passaggio ha esito negativo:

   Provare a riavviare il cluster per:
   1. Nel riquadro a sinistra selezionare **Cluster**.
   1. Nella tabella selezionare il nome del cluster.
   1. Nella scheda **Librerie** selezionare **Riavvia**.

   Prendere in considerazione anche:
   + In AutoML config, quando si usa Azure Databricks aggiungere i parametri seguenti:
       1. ```max_concurrent_iterations``` si basa sul numero di nodi del ruolo di lavoro nel cluster.
        2. ```spark_context=sc``` si basa sul contesto Spark predefinito.
   + In alternativa, se si dispone di una versione precedente dell'SDK, deselezionarla dalle librerie installate del cluster e spostarla nel Cestino. Installare la nuova versione dell'SDK e riavviare il cluster. Se si verifica un problema dopo il riavvio, scollegare e riconnettere il cluster.

Se l'installazione ha avuto esito positivo, la libreria importata avrà un aspetto simile al seguente:

SDK per databricks **_senza_** automazione di machine learning ![Azure Machine Learning SDK per databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK per databricks **con** machine learning ![SDK automatizzato con Machine Learning automatico installato in databricks](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Inizia a esplorare

Prova:
+ Sebbene siano disponibili molti notebook di esempio, **solo [questi notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) funzionano con Azure Databricks.**

+ Importare questi esempi direttamente dall'area di lavoro. Vedere di seguito: ![selezionare Importa](media/how-to-configure-environment/azure-db-screenshot.png)
![pannello di importazione](media/how-to-configure-environment/azure-db-import.png)

+ Informazioni su come [creare una pipeline con databricks come calcolo di training](how-to-create-your-first-pipeline.md).

## <a id="workspace"></a>Creare un file di configurazione dell'area di lavoro

Il file di configurazione dell'area di lavoro è un file JSON che indica all'SDK come comunicare con l'area di lavoro Azure Machine Learning. Il file è denominato *config.json* e ha il formato seguente:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Questo file JSON deve trovarsi nella struttura della directory che contiene gli script di Python o i notebook di Jupyter. Può trattarsi della stessa directory, in una sottodirectory denominata *.azureml* o in una directory padre.

Per usare questo file dal codice, usare `ws=Workspace.from_config()`. Questo codice carica le informazioni dal file e si connette all'area di lavoro.

È possibile creare il file di configurazione in tre modi:

* **Usare [WS. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)** : per scrivere un file *config. JSON* . che contiene le informazioni di configurazione per l'area di lavoro. È possibile scaricare o copiare il file *config.json* in altri ambienti di sviluppo.

* **Scaricare il file**: nella [portale di Azure](https://ms.portal.azure.com)Selezionare **Scarica config. JSON** dalla sezione **Panoramica** dell'area di lavoro.

     ![Portale di Azure](./media/how-to-configure-environment/configure.png)

* **Creare il file a livello**di codice: nel seguente frammento di codice è possibile connettersi a un'area di lavoro fornendo l'ID sottoscrizione, il gruppo di risorse e il nome dell'area di lavoro. La configurazione dell'area di lavoro viene quindi salvata nel file:

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

    Questo codice scrive il file di configurazione nel file con *estensione azureml/config. JSON* .


## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training di un modello](tutorial-train-models-with-aml.md) in Azure Machine Learning con il set di dati MNIST
- Vedere la documentazione di riferimento di [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
