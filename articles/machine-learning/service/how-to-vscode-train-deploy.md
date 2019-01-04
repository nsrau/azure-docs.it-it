---
title: Eseguire il training e la distribuzione di modelli da VS Code
titleSuffix: Azure Machine Learning service
description: Informazioni su Azure Machine Learning for Visual Studio Code e su come avviare il training e la distribuzione di modelli di Machine Learning e Deep Learning nel servizio Azure Machine Learning usando Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 0910cce92c410a68dce6e2c44d29e72e594cd153
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271709"
---
# <a name="use-visual-studio-code-to-train-and-deploy-machine-learning-models"></a>Usare Visual Studio Code per eseguire il training e la distribuzione di modelli di Machine Learning

Questo articolo illustra come usare l'estensione **Azure Machine Learning for Visual Studio Code** per eseguire il training e la distribuzione di modelli di Machine Learning e Deep Learning con il servizio Azure Machine Learning in Visual Studio Code (VS Code).

Azure Machine Learning supporta gli esperimenti in esecuzione localmente e su destinazioni di calcolo remote. Per ogni esperimento è possibile tenere traccia di più esecuzioni in quanto spesso occorre provare in modo interattivo altre tecniche, iperparametri diversi e altro ancora. È possibile usare Azure Machine Learning per tenere traccia delle metriche personalizzate e delle esecuzioni dell'esperimento, consentendo così la riproducibilità e la verificabilità di data science.

È anche possibile distribuire questi modelli per esigenze di test e di produzione.

## <a name="prerequisites"></a>Prerequisiti

+ Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](http://aka.ms/AMLFree).

+ Installare l'estensione [Azure Machine Learning for VS Code](how-to-vscode-tools.md).

+ Installare [Azure Machine Learning SDK for Python](how-to-vscode-tools.md) con VS Code.

## <a name="create-and-manage-compute-targets"></a>Creare e gestire destinazioni di calcolo

Con Azure Machine Learning for VS Code è possibile preparare i dati, eseguire il training di modelli e distribuirli sia in locale sia in destinazioni di calcolo remote.

Questa estensione supporta varie destinazioni di calcolo remote per Azure Machine Learning. Vedere l'[elenco completo delle destinazioni di calcolo supportate](how-to-set-up-training-targets.md) per Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Creare destinazioni di calcolo per Azure Machine Learning in VS Code

**Per creare una destinazione di calcolo:**

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale di Azure Machine Learning.

2. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning. Nell'immagine animata il nome della sottoscrizione è "Free Trial" e l'area di lavoro è "TeamWorkspace". 

3. Sotto il nodo dell'area di lavoro fare clic con il pulsante destro del mouse sul nodo **Compute** (Calcolo) e scegliere **Create Compute** (Crea calcolo).

4. Scegliere il tipo di destinazione di calcolo nell'elenco. 

5. Specificare le proprietà avanzate nel file di configurazione JSON che viene aperto in una nuova scheda. In questo file è possibile specificare un nome univoco per la destinazione di calcolo.

6. Dopo aver configurato la destinazione di calcolo, fare clic su **Invia** in basso a destra.

Di seguito è riportato un esempio per l'ambiente di calcolo di Azure Machine Learning (AMLCompute): [![Creare l'ambiente di calcolo di Azure Machine Learning in VS Code](./media/vscode-tools-for-ai/CreateAMLCompute.gif)](./media/vscode-tools-for-ai/CreateAMLCompute.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Usare calcoli remoti per esperimenti in VS Code

Per usare una destinazione di calcolo remota durante il training, è necessario creare un file di configurazione di esecuzione. Questo file indica ad Azure Machine Learning non solo la posizione in cui eseguire l'esperimento, ma anche come preparare l'ambiente.

#### <a name="the-run-configuration-file"></a>File di "configurazione di esecuzione"

L'estensione VS Code creerà automaticamente una configurazione di esecuzione per gli ambienti **locale** e **docker** nel computer locale.

Di seguito è riportato un frammento del file di configurazione di esecuzione predefinito.

Se si intende installare tutte le librerie/dipendenze manualmente, impostare `userManagedDependencies: True`. Le esecuzioni dell'esperimento locali useranno quindi l'ambiente Python predefinito come specificato dall'estensione Python di VS Code.

```yaml
# user_managed_dependencies=True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: False
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
    docker:
# Set True to perform this run inside a Docker container.
    enabled: false
```

#### <a name="the-conda-dependencies-file"></a>File delle dipendenze Conda

Per impostazione predefinita, viene creato automaticamente un nuovo ambiente Conda e vengono gestite le dipendenze dell'installazione. È tuttavia necessario specificare le dipendenze nel file `aml_config/conda_dependencies.yml`.

Di seguito è riportato un frammento del file "aml_config/conda_dependencies.yml" predefinito.
È possibile inserire dipendenze aggiuntive nel file di configurazione.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

## <a name="train-and-tune-models"></a>Eseguire il training e ottimizzare i modelli

Usare Azure Machine Learning for VS Code (anteprima) per eseguire rapidamente le iterazioni del codice, esaminare ogni passaggio ed eseguirne il debug e usare la soluzione di controllo del codice sorgente di propria scelta. 

**Per eseguire l'esperimento con Azure Machine Learning:**

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning. 

1. Sotto il nodo dell'area di lavoro espandere il nodo **Compute** (Calcolo) e fare clic con il pulsante destro del mouse sulla **configurazione di esecuzione** del calcolo che si intende usare. 

1. Selezionare **Run Experiment** (Esegui esperimento).

1. Fare clic su **View Experiment Run** (Visualizza esecuzione esperimento) per vedere il portale di Azure Machine Learning integrato, al fine di monitorare le esecuzioni e visualizzare i modelli sottoposti al training.

## <a name="deploy-and-manage-models"></a>Distribuire e gestire i modelli
Azure Machine Learning consente di distribuire e gestire i modelli di Machine Learning nel cloud e ai margini. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Registrare il modello in Azure Machine Learning da VS Code

Dopo avere eseguito il training del modello, è possibile registrarlo nell'area di lavoro.
I modelli registrati possono essere verificati e distribuiti.

**Per registrare il modello:**

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning.

1. Sotto il nodo dell'area di lavoro fare clic con il pulsante destro del mouse su **Models** (Modelli) e scegliere **Register Model** (Registra modello).

1. Nell'elenco scegliere se caricare un **file di modello** (per modelli singoli) o una **cartella di modello** (per modelli con più file, ad esempio Tensorflow). 

1. Selezionare la cartella o il file.

1. Dopo aver configurato le proprietà del modello, fare clic su **Invia** in basso a destra. 



### <a name="deploy-your-service-from-vs-code"></a>Distribuire il servizio da VS Code

Tramite VS Code è possibile distribuire il servizio Web in:
+ Istanza di contenitore di Azure: per l'ambiente di test
+ Servizio Azure Kubernetes (AKS): per l'ambiente di produzione 

Non occorre creare un contenitore di Istanza di contenitore di Azure da testare in anticipo in quanto il contenitore viene creato al momento. I cluster AKS non devono necessariamente essere configurati in anticipo. 

Sono disponibili altre informazioni generali sulla [distribuzione con Azure Machine Learning](how-to-deploy-and-where.md).

**Per distribuire un servizio Web:**

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning.

1. Sotto il nodo dell'area di lavoro espandere il nodo **Models** (Modelli).

1. Fare clic con il pulsante destro del mouse sul modello che si intende distribuire e scegliere il comando **Deploy Service from Registered Model**(Distribuisci servizio da modello registrato) nel menu a comparsa.

1. Nel riquadro comandi scegliere dall'elenco la destinazione di calcolo in cui eseguire la distribuzione. 

1. Nel campo immettere un nome per questo servizio. 

1. Nel riquadro comandi premere INVIO sulla tastiera per cercare e selezionare il file di script.

1. Nel riquadro comandi premere INVIO sulla tastiera per cercare e selezionare il file delle dipendenze Conda.

1. Dopo aver configurato le proprietà del servizio, fare clic su **Invia** in basso a destra. In questo file di proprietà del servizio è possibile specificare un file Docker locale o un file schema.json che può essere opportuno usare.

Il servizio Web è ora distribuito.

## <a name="next-steps"></a>Passaggi successivi

Per una procedura dettagliata sul training con Machine Learning all'esterno di VS Code, leggere [Esercitazione: Eseguire il training di modelli con Azure Machine Learning](tutorial-train-models-with-aml.md).

Per una procedura dettagliata per la modifica, l'esecuzione e il debug del codice in locale, vedere l'[esercitazione Hello World in Python](https://code.visualstudio.com/docs/python/python-tutorial)
