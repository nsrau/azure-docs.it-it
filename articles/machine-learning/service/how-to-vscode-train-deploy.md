---
title: Utilizzo dell'estensione Visual Studio Code Tools for AI con Azure Machine Learning
description: Informazioni su Visual Studio Code Tools for AI e su come avviare il training e distribuire modelli di Machine Learning e di apprendimento avanzato con il servizio Azure Machine Learning in VS Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: 377a4bbf359b2c65136625fcef8a1093e49da728
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854306"
---
# <a name="vs-code-tools-for-ai-train-and-deploy-ml-models-from-vs-code"></a>Visual Studio Code Tools for AI: eseguire il training e distribuire modelli di Machine Learning da VS Code
In questo articolo si apprenderà come usare l'estensione **VS Code Tools for AI** per eseguire il training e distribuire modelli di Machine Learning e apprendimento avanzato con il servizio Azure Machine Learning in VS Code.

Azure Machine Learning supporta gli esperimenti in esecuzione localmente e su destinazioni di calcolo remote. Per ogni esperimento è possibile tenere traccia di più esecuzioni in quanto spesso occorre provare in modo interattivo altre tecniche, iperparametri diversi e altro ancora. È possibile usare Azure Machine Learning per tenere traccia delle metriche personalizzate e delle esecuzioni dell'esperimento, consentendo così la riproducibilità e la verificabilità di data science.

È anche possibile distribuire questi modelli per esigenze di test e di produzione.

## <a name="prerequisites"></a>Prerequisiti

+ Configurazione di [VS Code Tools for AI](how-to-vscode-tools.md) per Azure Machine Learning.

+ Installazione di [Azure Machine Learning SDK for Python](how-to-vscode-tools.md) con VS Code.

+ Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLfree) prima di iniziare.

## <a name="create-and-manage-compute-targets"></a>Creare e gestire destinazioni di calcolo

Con Visual Studio Code Tools for AI è possibile preparare i dati, eseguire il training dei modelli e distribuirli sia localmente che su destinazioni di calcolo remote.

Questa estensione supporta varie destinazioni di calcolo remote per Azure Machine Learning. Vedere l'[elenco completo delle destinazioni di calcolo supportate](how-to-set-up-training-targets.md) per Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Creare destinazioni di calcolo per Azure Machine Learning in VS Code

**Per creare una destinazione di calcolo:**

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale Azure: Machine Learning.

2. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning. Nell'immagine animata il nome della sottoscrizione è "OpenMind Studio" e l'area di lavoro è "MyWorkspace". 

3. Sotto il nodo dell'area di lavoro fare clic con il pulsante destro del mouse sul nodo **Compute** (Calcolo) e scegliere **Create Compute** (Crea calcolo).

4. Scegliere il tipo di destinazione di calcolo nell'elenco. 

5. Nel campo immettere un nome univoco per questa destinazione di calcolo e specificare la dimensione della macchina virtuale.

6. Specificare le proprietà avanzate nel file di configurazione JSON che viene aperto in una nuova scheda. 

7. Dopo avere configurato la destinazione di calcolo, fare clic su **Fine** in basso a destra.

Ecco un esempio di Azure Batch per intelligenza artificiale: [![Creare un calcolo di Azure Batch per intelligenza artificiale in VS Code](./media/vscode-tools-for-ai/createcompute.gif)](./media/vscode-tools-for-ai/createcompute.gif#lightbox)

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

Usare Azure Machine Learning da Visual Studio Code per eseguire rapidamente le iterazioni del codice, esaminare ogni passaggio ed eseguirne il debug e usare la soluzione di controllo del codice sorgente di propria scelta. 

**Per eseguire l'esperimento con Azure Machine Learning:**

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale Azure: Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning. Nell'immagine animata il nome della sottoscrizione è "OpenMind Studio" e l'area di lavoro è "MyWorkspace". 

1. Sotto il nodo dell'area di lavoro espandere il nodo **Compute** (Calcolo) e fare clic con il pulsante destro del mouse sulla **configurazione di esecuzione** del calcolo che si intende usare. 

1. Selezionare **Run Experiment** (Esegui esperimento).

1. Fare clic su **View Experiment Run** (Visualizza esecuzione esperimento) per vedere il portale di Azure Machine Learning integrato, al fine di monitorare le esecuzioni e visualizzare i modelli sottoposti al training.

   [![Eseguire l'esperimento di Machine Learning da VS Code](./media/vscode-tools-for-ai/runexperiment.gif)](./media/vscode-tools-for-ai/runexperiment.gif#lightbox)

## <a name="deploy-and-manage-models"></a>Distribuire e gestire i modelli
Azure Machine Learning consente di distribuire e gestire i modelli di Machine Learning nel cloud e ai margini. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Registrare il modello in Azure Machine Learning da VS Code

Dopo avere eseguito il training del modello, è possibile registrarlo nell'area di lavoro.
I modelli registrati possono essere verificati e distribuiti.

**Per registrare il modello:**

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale Azure: Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning.

1. Sotto il nodo dell'area di lavoro fare clic con il pulsante destro del mouse su **Models** (Modelli) e scegliere **Register Model** (Registra modello).

1. Nell'elenco scegliere se caricare un **file di modello** (per modelli singoli) o una **cartella di modello** (per modelli con più file, ad esempio Tensorflow). 

1. Usare la finestra di dialogo di selezione file per selezionare il file o la cartella.

   [![calcolo](./media/vscode-tools-for-ai/registermodel.gif)](./media/vscode-tools-for-ai/registermodel.gif#lightbox)

> [!Warning]
> Rimuovere per ora i tag dal file JSON generato.

### <a name="deploy-your-service-from-vs-code"></a>Distribuire il servizio da VS Code

Tramite VS Code è possibile distribuire il servizio Web in:
+ Istanza di contenitore di Azure: per l'ambiente di test
+ Servizio Kubernetes di Azure (AKS): per l'ambiente di produzione 

Non occorre creare un contenitore di Istanza di contenitore di Azure da testare in anticipo in quanto il contenitore viene creato al momento. I cluster AKS non devono necessariamente essere configurati in anticipo. 

Sono disponibili altre informazioni generali sulla [distribuzione con Azure Machine Learning](how-to-deploy-and-where.md).

**Per distribuire un servizio Web:**

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale Azure: Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning.

1. Sotto il nodo dell'area di lavoro espandere il nodo **Models** (Modelli).

1. Fare clic con il pulsante destro del mouse sul modello che si intende distribuire e scegliere il comando **Deploy Service from Registered Model**(Distribuisci servizio da modello registrato) nel menu a comparsa.

1. Nel riquadro comandi di VS Code scegliere nell'elenco la destinazione di calcolo in cui eseguire la distribuzione. 

1. Nel campo immettere un nome per questo servizio. 

1. Nella finestra di dialogo nell'angolo inferiore destro fare clic su **Browse** (Sfoglia) e selezionare lo script di assegnazione dei punteggi. Verrà chiusa la finestra di dialogo.

1. Se si dispone di un file di Docker locale, fare clic su **Browse** (Sfoglia) nella seconda finestra di dialogo che viene visualizzata. 

   Se si annulla la visualizzazione della finestra di dialogo e non si specifica un file di Docker locale, per impostazione predefinita ne viene usato uno di "Azure Machine Learning".

1. Nella terza finestra di dialogo che viene visualizzata fare clic su **Browse** (Sfoglia) e selezionare il percorso del file Conda locale o immettere il percorso del file nell'editor JSON in un secondo momento.

1. Se si dispone di un file schema.json e si intende usarlo, fare clic su **Browse** (Sfoglia) nella quarta finestra di dialogo che viene visualizzata e selezionare il file.

Il servizio Web è ora distribuito.

Ecco un esempio di Istanza di contenitore di Azure: [![Istanza di contenitore di Azure da VS Code](./media/vscode-tools-for-ai/deploy.gif)](./media/vscode-tools-for-ai/deploy.gif#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Per una procedura dettagliata sul training con Machine Learning all'esterno di VS Code, leggere [Esercitazione: Eseguire il training di modelli con Azure Machine Learning](tutorial-train-models-with-aml.md).

Per una procedura dettagliata per la modifica, l'esecuzione e il debug del codice in locale, vedere l'[esercitazione Hello World in Python](https://code.visualstudio.com/docs/python/python-tutorial)
