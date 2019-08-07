---
title: Configurare un ambiente di sviluppo Python
titleSuffix: Azure Machine Learning service
description: Informazioni su come configurare un ambiente di sviluppo quando si usa il servizio Azure Machine Learning. Questo articolo illustra come usare gli ambienti conda, creare file di configurazione e configurare il proprio server notebook basato sul cloud, notebook Jupyter, Azure Databricks, Azure Notebooks, IDE, editor di codice e il Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: efe5829ffbe4db304f9ffab0a259ab9d10279b27
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772697"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurare un ambiente di sviluppo per Azure Machine Learning

Questo articolo contiene informazioni su come configurare un ambiente di sviluppo per poter usare il servizio Azure Machine Learning. Azure Machine Learning servizio è indipendente dalla piattaforma. L'unico requisito difficile per l'ambiente di sviluppo è Python 3. È consigliabile anche un ambiente isolato come Anaconda o virtualenv.

La tabella seguente illustra tutti gli ambienti di sviluppo trattati in questo articolo, oltre a vantaggi e svantaggi.

| Ambiente | Vantaggi | Svantaggi |
| --- | --- | --- |
| [VM notebook basata sul cloud](#notebookvm) | Modo più semplice per iniziare. L'intero SDK è già installato nella macchina virtuale dell'area di lavoro e le esercitazioni sui notebook sono pre-clonate e pronte per l'esecuzione. | Mancanza di controllo sull'ambiente di sviluppo e sulle dipendenze. Costi aggiuntivi per le VM Linux (è possibile arrestare la macchina virtuale quando non è in uso per evitare addebiti). Vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Ambiente locale](#local) | Controllo completo dell'ambiente di sviluppo e delle dipendenze. Eseguire con qualsiasi strumento di compilazione, ambiente o IDE di propria scelta. | Per iniziare, è necessario più tempo. È necessario installare i pacchetti SDK necessari ed è necessario installare anche un ambiente, se non è già presente. |
| [Azure Databricks](#aml-databricks) | Ideale per l'esecuzione di flussi di lavoro di Machine Learning intensivi su larga scala sulla piattaforma Apache Spark scalabile. | Overkill per l'apprendimento automatico sperimentale o per esperimenti e flussi di lavoro su scala ridotta. Costi aggiuntivi per Azure Databricks. Vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/databricks/). |
| [Il Data Science Virtual Machine (DSVM)](#dsvm) | Analogamente alla macchina virtuale notebook basata sul cloud (Python e SDK sono preinstallati), ma con gli strumenti di data science e di apprendimento automatico più diffusi preinstallati. Facile da scalare e combinare con altri strumenti e flussi di lavoro personalizzati. | Un'esperienza introduttiva più lenta rispetto alla VM notebook basata sul cloud. |
| [Azure Notebooks](#aznotebooks) | Esperienza introduttiva gratuita e leggera con Python e l'SDK pre-installato. | Sono disponibili macchine virtuali meno potenti rispetto alla VM notebook basata sul cloud. Isolato dall'area di lavoro e da altre risorse. |

Questo articolo fornisce anche suggerimenti sull'utilizzo aggiuntivi per gli strumenti seguenti:

* [Jupyter Notebook](#jupyter): se si usa già Jupyter Notebook, l'SDK include alcune funzionalità aggiuntive che è consigliabile installare.

* [Visual Studio Code](#vscode): se si usa Visual Studio Code, sono disponibili alcune estensioni utili che è possibile installare.

## <a name="prerequisites"></a>Prerequisiti

Un'area di lavoro del servizio Azure Machine Learning. Per creare l'area di lavoro, vedere [creare un'area di lavoro del servizio Azure Machine Learning](setup-create-workspace.md). Un'area di lavoro è sufficiente per iniziare a usare il proprio [Server notebook basato sul cloud](#notebookvm), un [DSVM](#dsvm), [Azure Databricks](#aml-databricks)o [Azure Notebooks](#aznotebooks).

Per installare l'ambiente SDK per il [computer locale](#local), [Jupyter Notebook server](#jupyter) o [Visual Studio Code](#vscode) sono necessari anche:

- Gestione pacchetti [Anaconda](https://www.anaconda.com/download/) o [Miniconda](https://conda.io/miniconda.html) .

- In Linux o macOS è necessaria la shell bash.

    > [!TIP]
    > Se si usa Linux o macOS con una shell diversa da bash, ad esempio zsh, possono verificarsi errori durante l'esecuzione di alcuni comandi. Per risolvere questo problema, usare il comando `bash` per avviare una nuova shell bash ed eseguire i comandi nella shell.

- In Windows è necessario il prompt dei comandi o il prompt di Anaconda (installato per Anaconda e Miniconda).

## <a id="notebookvm"></a>VM notebook basata sul cloud

La macchina virtuale notebook (anteprima) è una workstation di Azure sicura basata sul cloud che fornisce ai data scientist un server Jupyter notebook, JupyterLab e un ambiente ML completamente preparato.

La VM del notebook è:

+ **Sicuro**. Poiché l'accesso alla macchina virtuale e al notebook viene protetto con HTTPS e Azure Active Directory per impostazione predefinita, i professionisti IT possono applicare facilmente Single Sign-on e altre funzionalità di sicurezza, ad esempio l'autenticazione a più fattori.

+ **Preconfigurato**. Questo ambiente Python ML completamente preparato ne trae il proprio pedigree dal popolare Data Science VM IaaS e include:
  + Azure ML Python SDK (versione più recente)
  + Configurazione automatica per l'uso con l'area di lavoro
  + Un server Jupyter notebook
  + IDE di JupyterLab notebook
  + Driver GPU preconfigurati
  + Una selezione di Framework per Deep Learning


  Se ci si trova nel codice, nella macchina virtuale sono incluse esercitazioni ed esempi che consentono di esplorare e apprendere come usare Azure Machine Learning servizio. I notebook di esempio vengono archiviati nell'account di archiviazione BLOB di Azure dell'area di lavoro rendendoli condivisibili tra le macchine virtuali. Quando vengono eseguiti, possono anche accedere agli archivi dati e alle risorse di calcolo dell'area di lavoro.

+ **Installazione semplice**: Crearne uno in qualsiasi momento dall'area di lavoro Azure Machine Learning. Fornire un solo nome e specificare un tipo di macchina virtuale di Azure. Provare ora con questa [esercitazione: Ambiente e area di](tutorial-1st-experiment-sdk-setup.md)lavoro di installazione.

+ **Personalizzabile**. Anche se un'offerta di macchina virtuale gestita e sicura consente di mantenere l'accesso completo alle funzionalità hardware e di personalizzarlo in base alle proprie esigenze. Ad esempio, è possibile creare rapidamente la macchina virtuale più recente con tecnologia NVidia V100 per eseguire il debug dettagliato di una nuova architettura di rete neurale.

Per arrestare gli addebiti per le VM notebook, [arrestare la VM del notebook](tutorial-1st-experiment-sdk-setup.md#stop-the-notebook-vm). 

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

Quando si usa un computer locale (che potrebbe anche essere una macchina virtuale remota), creare un ambiente Anaconda e installare l'SDK seguendo questa procedura:

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

    Questo comando installa il Azure Machine Learning SDK di base con notebook e automl aggiuntivi. Il `automl` supplemento è un'installazione di grandi dimensioni e può essere rimosso dalle parentesi quadre se non si intende eseguire esperimenti automatici di machine learning. Il `automl` supplemento include anche l'SDK di preparazione dei dati Azure Machine Learning per impostazione predefinita come dipendenza.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > Se si riceve un messaggio che indica che PyYAML non può essere installato, usare in alternativa il comando seguente:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`

   L'installazione dell'SDK può richiedere diversi minuti. Per ulteriori informazioni sulle opzioni di installazione, vedere la [Guida](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) all'installazione.

1. Installare altri pacchetti per la sperimentazione di machine learning.

    Usare uno dei comandi seguenti e sostituire  *\<new Package >* con il pacchetto che si vuole installare. Per l'installazione `conda install` di pacchetti tramite è necessario che il pacchetto faccia parte dei canali correnti. i nuovi canali possono essere aggiunti nel cloud Anaconda.

    ```shell
    conda install <new package>
    ```

    In alternativa, è possibile installare i pacchetti `pip`tramite.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter Notebook

I notebook di Jupyter fanno parte del [progetto Jupyter](https://jupyter.org/). Forniscono un'esperienza di codifica interattiva in cui si creano documenti che combinano codice attivo con testi narrativi e grafica. Anche i notebook di Jupyter sono un'ottima soluzione per condividere i risultati con altri utenti perché è possibile salvare l'output delle sezioni di codice nel documento. È possibile installare Jupyter Notebook in un'ampia gamma di piattaforme.

La procedura nella sezione [computer locale](#local) installa i componenti necessari per l'esecuzione di notebook di Jupyter in un ambiente Anaconda. Per abilitare questi componenti nell'ambiente di Jupyter Notebook, seguire questa procedura:

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

1. Se si verificano problemi durante l'importazione di `ModuleNotFoundError`moduli e la ricezione di un, assicurarsi che il kernel Jupyter sia connesso al percorso corretto per l'ambiente eseguendo il codice seguente in una cella del notebook.

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
Azure Databricks è un ambiente basato su Apache Spark nel cloud di Azure. Offre un ambiente basato su notebook collaborativo con cluster di calcolo basato su CPU o GPU.

Funzionamento di Azure Databricks con il servizio Azure Machine Learning:
+ È possibile eseguire il training di un modello usando Spark MLlib e distribuire il modello in ACI/AKS dall'interno Azure Databricks.
+ È anche possibile usare le funzionalità automatiche di [Machine Learning](concept-automated-ml.md) in un SDK di Azure ml speciale con Azure Databricks.
+ È possibile usare Azure Databricks come destinazione di calcolo da una [pipeline di Azure Machine Learning](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Configurare il cluster databricks

Creare un [cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)databricks. Alcune impostazioni si applicano solo se si installa l'SDK per Machine Learning automatico in databricks.
**La creazione del cluster potrebbe richiedere alcuni minuti.**

Usare le impostazioni seguenti:

| Impostazione |Si applica a| Valore |
|----|---|---|
| Nome cluster |sempre| nomecluster |
| Databricks Runtime |sempre| Qualsiasi runtime non ML (non ML 4.x, 5.x) |
| Versione Python |sempre| 3 |
| Ruoli di lavoro |sempre| almeno 2 |
| Worker node VM types (Tipi di VM dei nodi di ruolo di lavoro) <br>(determina il numero massimo di iterazioni simultanee) |Funzionalità automatiche di Machine Learning<br>solo| È preferibile una macchina virtuale ottimizzata per la memoria |
| Enable Autoscaling (Abilita la scalabilità automatica) |Funzionalità automatiche di Machine Learning<br>solo| Deselezionare |

Attendere che il cluster sia in esecuzione prima di proseguire.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Installare l'SDK corretto in una libreria databricks
Quando il cluster è in esecuzione, [creare una libreria](https://docs.databricks.com/user-guide/libraries.html#create-a-library) per alleghi il pacchetto SDK Azure Machine Learning appropriato al cluster.

1. Scegliere **una sola** opzione (non sono supportate altre installazioni SDK)

   |Funzionalità&nbsp;aggiuntive&nbsp;del pacchetto SDK|Source|Nome&nbsp;pypi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Per databricks| Caricare Python Egg o PyPI | azureml-sdk[databricks]|
   |Per databricks-con-<br> funzionalità di Machine Learning automatiche| Caricare Python Egg o PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Non è possibile installare altri SDK aggiuntivi. Scegliere solo una delle opzioni precedenti [databricks] o [automl_databricks].

   * Non selezionare **Connetti automaticamente a tutti i cluster**.
   * Selezionare **Connetti** accanto al nome del cluster.

1. Consente di monitorare gli errori fino a quando lo stato non diventa **collegato**, operazione che può richiedere alcuni minuti.  Se questo passaggio non riesce, controllare quanto segue:

   Provare a riavviare il cluster per:
   1. Nel riquadro a sinistra selezionare **Cluster**.
   1. Nella tabella selezionare il nome del cluster.
   1. Nella scheda **Librerie** selezionare **Riavvia**.

   Prendere in considerazione anche:
   + In Automl config, quando si usa Azure Databricks aggiungere i parametri seguenti:
       1. ```max_concurrent_iterations```si basa sul numero di nodi del ruolo di lavoro nel cluster.
        2. ```spark_context=sc```si basa sul contesto Spark predefinito.
   + In alternativa, se si dispone di una versione precedente dell'SDK, deselezionarla dalle librerie installate del cluster e spostarla nel Cestino. Installare la nuova versione dell'SDK e riavviare il cluster. Se successivamente si verifica un problema, scollegare e ricollegare il cluster.

Se l'installazione ha avuto esito positivo, la libreria importata avrà un aspetto simile al seguente:

SDK per databricks **_senza_** automazione Azure Machine Learning SDK ![per machine learning per databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK per databricks **con** l'SDK di ![Machine Learning automatizzato con Machine Learning automatico installato in databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Inizia a esplorare

Prova:
+ Scaricare il [file di archivio del notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) per Azure Databricks/Azure Machine Learning SDK e [importare il file di archivio](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) nel cluster databricks.
  Sebbene siano disponibili molti notebook di esempio, **solo [questi notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) funzionano con Azure Databricks.**

+ Informazioni su come [creare una pipeline con databricks come calcolo di training](how-to-create-your-first-pipeline.md).

## <a id="aznotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (anteprima) è un ambiente di sviluppo interattivo nel cloud di Azure. Si tratta di un modo semplice per iniziare a sviluppare Azure Machine Learning.

* L'SDK di Azure Machine Learning è già installato.
* Dopo aver creato un'area di lavoro del servizio Azure Machine Learning nel portale di Azure, è possibile fare clic su un pulsante per configurare automaticamente l'ambiente di Azure Notebooks per usare l'area di lavoro.

Per iniziare a usare Azure Notebooks, usare il [portale di Azure](https://portal.azure.com).  Aprire l'area di lavoro e nella sezione **Panoramica** selezionare **inizia in Azure Notebooks**.

Per impostazione predefinita, Azure Notebooks usa un livello di servizio gratuito limitato a 4 GB di memoria e 1 GB di dati. È, tuttavia, possibile rimuovere questi limiti mediante l'aggiunta di un'istanza di Data Science Virtual Machine al progetto Azure Notebooks. Per altre informazioni, vedere [Manage and configure Azure Notebooks projects - Compute tier](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier) (Gestire e configurare i progetti di Azure Notebooks - Livello di calcolo).

## <a id="workspace"></a>Creare un file di configurazione dell'area di lavoro

Il file di configurazione dell'area di lavoro è un file JSON che indica all'SDK come comunicare con l'area di lavoro del servizio Azure Machine Learning. Il file è denominato *config.json* e ha il formato seguente:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Questo file JSON deve trovarsi nella struttura della directory che contiene gli script di Python o i notebook di Jupyter. Può trovarsi nella stessa directory, in una sottodirectory denominata *. azureml*o in una directory padre.

Per usare questo file dal codice, usare `ws=Workspace.from_config()`. Questo codice carica le informazioni dal file e si connette all'area di lavoro.

È possibile creare il file di configurazione in tre modi:

* **Eseguire la procedura descritta in [creare un'area di lavoro del servizio Azure Machine Learning](setup-create-workspace.md#sdk)** : nella libreria di Azure Notebooks viene creato un file *config.json* che contiene le informazioni di configurazione per l'area di lavoro. È possibile scaricare o copiare il file *config.json* in altri ambienti di sviluppo.

* **Scaricare il file**: Nel [portale di Azure](https://ms.portal.azure.com) selezionare **Scarica config.json** nella sezione **Panoramica** dell'area di lavoro.

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

    Questo codice scrive il file di configurazione nel file con *estensione azureml/config. JSON* .


## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training di un modello](tutorial-train-models-with-aml.md) in Azure Machine Learning con il set di dati MNIST
- Vedere la documentazione di riferimento di [Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk)
- Informazioni sul [pacchetto di preparazione dei dati per Azure Machine Learning](https://aka.ms/data-prep-sdk)
