---
title: Usare Visual Studio Code con
titleSuffix: Azure Machine Learning service
description: Informazioni su come installare Azure Machine Learning per Visual Studio Code e creare un semplice esperimento in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: bbeb0dbbd5e9c919eda4b298dc5bee31965e9bac
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995252"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Introduzione ad Azure Machine Learning per Visual Studio Code

Questo articolo illustra come usare l'estensione **Azure Machine Learning for Visual Studio Code** per eseguire il training e la distribuzione di modelli di Machine Learning e Deep Learning con il servizio Azure Machine Learning in Visual Studio Code (VS Code).

Servizio Azure Machine Learning fornisce il supporto per l'esecuzione di esperimenti in locale e sulle destinazioni di calcolo remoto. Per ogni esperimento è possibile tenere traccia di più esecuzioni in quanto spesso occorre provare in modo interattivo altre tecniche, iperparametri diversi e altro ancora. È possibile usare Azure Machine Learning per tenere traccia delle metriche personalizzate e delle esecuzioni dell'esperimento, consentendo così la riproducibilità e la verificabilità di data science.

È anche possibile distribuire questi modelli per esigenze di test e di produzione.

## <a name="prerequisites"></a>Prerequisiti

+ Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Accedere alla [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

+ È necessario installare Visual Studio Code. Visual Studio Code è un editor di codice sorgente leggero, ma potente che viene eseguito sul desktop. Include supporto integrato per Python e altro ancora.  [Informazioni su come installare Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Installare Python 3.5 o versione successiva](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>Installare Azure Machine Learning per l'estensione di Visual Studio Code

Quando si installa l'estensione **Azure Machine Learning**, due ulteriori estensioni vengono installate automaticamente (se si ha accesso a Internet). Sono l'estensione [Account di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) e l'estensione [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

Per usare Azure Machine Learning, è necessario trasformare Visual Studio Code in un ambiente di sviluppo integrato Python. Lavorare con [Python in Visual Studio Code](https://code.visualstudio.com/docs/languages/python) richiede l'estensione Microsoft Python, che viene installata automaticamente con l'estensione Azure Machine Learning. L'estensione rende il Visual Studio Code un ambiente di sviluppo integrato eccellente e funziona su qualsiasi sistema operativo con una serie di interpreti Python. Sfrutta tutte potenzialità di Visual Studio Code per fornire completamento automatico e IntelliSense, linting, debug e testing unità, oltre alla possibilità di passare facilmente da un ambiente Python all'altro, compresi gli ambienti virtuali e conda. Consultare questa procedura dettagliata per la modifica, l'esecuzione e il debug del codice Python, vedere l'[esercitazione Hello World in Python](https://code.visualstudio.com/docs/python/python-tutorial)

**Per installare l'estensione di Azure Machine Learning:**

1. Avviare VS Code.

1. In un browser, visitare: Estensione di [Azure Machine Learning per Visual Studio Code (anteprima)](https://aka.ms/vscodetoolsforai)

1. Nella pagina web, fare clic su **Installa**. 

1. Nella scheda estensioni, fare clic su **Installa**.

1. Verrà visualizzata una scheda di completamento dell'installazione in Visual Studio Code per l'estensione e il simbolo di Azure (descritto nella casella rossa nell'immagine seguente) viene aggiunto alla barra delle attività.

   ![Icona Azure nella barra delle attività di Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Nella finestra di dialogo, fare clic su **Accedi** e seguire le istruzioni visualizzate sullo schermo per eseguire l'autenticazione con Azure. 
   
   L'estensione Account Azure, che è stata installata con Azure Machine Learning per l'estensione di Visual Studio Code, consente di eseguire l'autenticazione con il proprio account di Azure. Vedere l'elenco dei comandi nella pagina [estensione Account di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Consultare l'[estensione IntelliCode per Visual Studio Code (anteprima)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode fornisce un set di funzionalità supportate da AI per IntelliSense in Python, ad esempio dedurre i completamenti automatici più pertinenti in base al contesto di codice corrente.

## <a name="azure-ml-sdk-installation"></a>Installazione di Azure Machine Learning SDK

1. Assicurarsi che Python 3.5 o versione successiva sia installato e riconosciuto da Visual Studio Code. Se si installa ora, riavviare Visual Studio Code e selezionare un interprete Python seguendo le istruzioni in https://code.visualstudio.com/docs/python/python-tutorial.

1. Nella finestra del terminale integrato, specificare l'interprete Python da usare oppure premere **INVIO** per usare l'interprete Python predefinito.

   ![Scegliere l'interprete](./media/vscode-tools-for-ai/python.png)

1. Nell'angolo inferiore destro della finestra verrà visualizzata una notifica indicante che l'installazione automatica di Azure Machine Learning SDK è in corso.    Viene creato un ambiente Python locale privato che presenta i prerequisiti di Visual Studio Code per l'uso con il servizio di Azure Machine Learning.

   ![Installare Azure Machine Learning SDK per Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Introduzione ad Azure Machine Learning

Prima di eseguire il training e distribuire i modelli di Machine Learning usando Visual Studio Code, è necessario creare un'[area di lavoro del servizio Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) nel cloud per contenere i modelli e le risorse. Informazioni su come crearne uno e creare il primo esperimento nell'area di lavoro.

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale di Azure Machine Learning.

   [![Installa](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Fare clic con il pulsante destro sulla sottoscrizione di Azure e selezionare **Creare area di lavoro**. Viene visualizzato un elenco. Nell'immagine animata il nome della sottoscrizione è "Free Trial" e l'area di lavoro è "TeamWorkspace". 

1. Selezionare un gruppo di risorse esistenti dall'elenco o crearne uno nuovo usando la procedura guidata nel riquadro comandi.

1. Nel campo, digitare un nome univoco e non crittografato per la nuova area di lavoro. Negli screenshot, l'area di lavoro è denominata "TeamWorkspace".

1. Premere Invio per creare la nuova area di lavoro. Viene visualizzata nell'albero sotto il nome della sottoscrizione.

1. Fare clic con il pulsante destro sul nodo Esperimento e scegliere **Crea esperimento** dal menu a comparsa.  Gli esperimenti tengono traccia delle esecuzioni usando Azure Machine Learning.

1. Nel campo, immettere un nome per l'esperimento. Negli screenshot, l'esperimento è denominato "MNIST".
 
1. Premere Invio per creare il nuovo esperimento. Viene visualizzato nell'albero sotto il nome dell'area di lavoro.

1. È possibile fare clic con il pulsante destro del mouse su un esperimento in un'area di lavoro e scegliere 'Set as Active Experiment' (Imposta come esperimento attivo). L'esperimento **'attivo'** è quello in uso e la cartella aperta in Visual Studio Code verrà collegata a questo esperimento nel cloud. Questa cartella deve contenere gli script di Python locali.

   Ora ogni esperimento viene eseguito con l'esperimento in modo che tutte le metriche chiave vengano archiviate nella cronologia degli esperimenti e i modelli di cui si esegue il training vengano automaticamente caricati in Azure Machine Learning e archiviati con metriche e log dell'esperimento.

   [![Allegare una cartella in Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Creare e gestire destinazioni di calcolo

Con Azure Machine Learning for VS Code è possibile preparare i dati, eseguire il training di modelli e distribuirli sia in locale sia in destinazioni di calcolo remote.

Questa estensione supporta varie destinazioni di calcolo remote per Azure Machine Learning. Vedere l'[elenco completo delle destinazioni di calcolo supportate](how-to-set-up-training-targets.md) per Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Creare destinazioni di calcolo per Azure Machine Learning in VS Code

**Per creare una destinazione di calcolo:**

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale di Azure Machine Learning.

2. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning. Nell'immagine animata il nome della sottoscrizione è "Free Trial" e l'area di lavoro è "TeamWorkspace". 

3. Sotto il nodo dell'area di lavoro fare clic con il pulsante destro del mouse sul nodo **Compute** (Calcolo) e scegliere **Create Compute** (Crea calcolo).

4. Scegliere il tipo di destinazione di calcolo nell'elenco. 

5. Nel riquadro comandi selezionare una dimensione di macchina virtuale.

6. Nel riquadro comandi immettere un nome per la destinazione di calcolo nel campo. 

7. Specificare le proprietà avanzate nel file di configurazione JSON che viene aperto in una nuova scheda. È possibile specificare le proprietà, ad esempio un numero massimo di nodi.

8. Dopo aver configurato la destinazione di calcolo, fare clic su **Submit** (Invia) nell'angolo in basso a destra della schermata.

Di seguito è riportato un esempio per la creazione di un ambiente di calcolo di Azure Machine Learning (AMLCompute): [![Creare l'ambiente di calcolo di Azure Machine Learning in VS Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>File di "configurazione di esecuzione"

L'estensione VS Code creerà automaticamente una destinazione di calcolo locale e le configurazioni di esecuzione per gli ambienti **locale** e **docker** nel computer locale. I file di configurazione di esecuzione sono disponibili sotto il nodo di destinazione di calcolo associate. 

## <a name="train-and-tune-models"></a>Eseguire il training e ottimizzare i modelli

Usare Azure Machine Learning for VS Code (anteprima) per eseguire rapidamente le iterazioni del codice, esaminare ogni passaggio ed eseguirne il debug e usare la soluzione di controllo del codice sorgente di propria scelta. 

**Per eseguire l'esperimento in locale con Azure Machine Learning:**

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning. 

1. Nel nodo dell'area di lavoro espandere il nodo **Compute** (Calcolo) e fare clic con il pulsante destro del mouse sulla **configurazione di esecuzione** del calcolo che si intende usare. 

1. Selezionare **Run Experiment** (Esegui esperimento).

1. Selezionare lo script da eseguire da Esplora file. 

1. Fare clic su **View Experiment Run** (Visualizza esecuzione esperimento) per vedere il portale di Azure Machine Learning integrato, al fine di monitorare le esecuzioni e visualizzare i modelli sottoposti al training.

Ecco un esempio per l'esecuzione di un esperimento in locale: [![Esecuzione di un esperimento in locale](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Usare calcoli remoti per esperimenti in VS Code

Per usare una destinazione di calcolo remota durante il training, è necessario creare un file di configurazione di esecuzione. Questo file indica ad Azure Machine Learning non solo la posizione in cui eseguire l'esperimento, ma anche come preparare l'ambiente.

#### <a name="the-conda-dependencies-file"></a>File delle dipendenze Conda

Per impostazione predefinita, viene creato automaticamente un nuovo ambiente Conda e vengono gestite le dipendenze dell'installazione. È tuttavia necessario specificare le dipendenze e le relative versioni nel file `aml_config/conda_dependencies.yml`. 

Di seguito è riportato un frammento del file "aml_config/conda_dependencies.yml" predefinito. Ad esempio, è possibile specificare "tensorflow = 1.12.0" come mostrato sotto. Se non si specifica la versione della dipendenza, verrà usata la versione più recente.  
È possibile inserire dipendenze aggiuntive nel file di configurazione.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

**Per eseguire l'esperimento con Azure Machine Learning in una destinazione di calcolo remota:**

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning. 

1. Fare clic con il pulsante destro del mouse sullo script Python nella finestra dell'editor e scegliere **AML: Run as Experiment in Azure** (AML: Esegui come esperimento in Azure). 

1. Nel riquadro comandi selezionare la destinazione di calcolo. 

1. Nel riquadro comandi immettere il nome della configurazione di esecuzione nel campo. 

1. Modificare il file conda_dependencies.yml per specificare le dipendenze di runtime dell'esperimento e quindi fare clic su **Submit** (Invia) nell'angolo in basso a destra della schermata. 

1. Fare clic su **View Experiment Run** (Visualizza esecuzione esperimento) per vedere il portale di Azure Machine Learning integrato, al fine di monitorare le esecuzioni e visualizzare i modelli sottoposti al training.

Ecco un esempio per l'esecuzione di un esperimento in una destinazione di calcolo locale: [![Esecuzione di un esperimento in una destinazione remota](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Distribuire e gestire i modelli
Azure Machine Learning consente di distribuire e gestire i modelli di Machine Learning nel cloud e ai margini. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Registrare il modello in Azure Machine Learning da VS Code

Dopo avere eseguito il training del modello, è possibile registrarlo nell'area di lavoro.
I modelli registrati possono essere verificati e distribuiti.

**Per registrare il modello:**

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning.

1. Sotto il nodo dell'area di lavoro fare clic con il pulsante destro del mouse su **Models** (Modelli) e scegliere **Register Model** (Registra modello).

1. Nel riquadro comandi immettere il nome di un modello nel campo. 

1. Nell'elenco scegliere se caricare un **file di modello** (per modelli singoli) o una **cartella di modello** (per modelli con più file, ad esempio Tensorflow). 

1. Selezionare la cartella o il file.

1. Dopo aver configurato le proprietà del modello, fare clic su **Submit** (Invia) nell'angolo in basso a destra della schermata. 

Ecco un esempio per la registrazione del modello in Azure Machine Learning: [![Registrazione di un modello in AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>Distribuire il servizio da VS Code

Tramite VS Code è possibile distribuire il servizio Web in:
+ Istanza di contenitore di Azure: per l'ambiente di test
+ Servizio Azure Kubernetes: per l'ambiente di produzione 

Non occorre creare un contenitore di Istanza di contenitore di Azure da testare in anticipo in quanto il contenitore viene creato al momento. I cluster servizio Azure Kubernetes non devono necessariamente essere configurati in anticipo. 

Sono disponibili altre informazioni generali sulla [distribuzione con Azure Machine Learning](how-to-deploy-and-where.md).

**Per distribuire un servizio Web:**

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning.

1. Sotto il nodo dell'area di lavoro espandere il nodo **Models** (Modelli).

1. Fare clic con il pulsante destro del mouse sul modello che si intende distribuire e scegliere il comando **Deploy Service from Registered Model**(Distribuisci servizio da modello registrato) nel menu a comparsa.

1. Nel riquadro comandi scegliere dall'elenco la destinazione di calcolo in cui eseguire la distribuzione. 

1. Nel riquadro comandi immettere un nome per questo servizio nel campo.  

1. Nel riquadro comandi premere INVIO sulla tastiera per cercare e selezionare il file di script.

1. Nel riquadro comandi premere INVIO sulla tastiera per cercare e selezionare il file delle dipendenze Conda.

1. Dopo aver configurato le proprietà del servizio, fare clic su **Submit** (Invia) nell'angolo in basso a destra della schermata. In questo file di proprietà del servizio è possibile specificare un file Docker locale o un file schema.json che può essere opportuno usare.

Il servizio Web è ora distribuito.

Ecco un esempio per la distribuzione di un servizio Web: [![Distribuzione di un servizio Web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Usare i tasti di scelta rapida

Analogamente alla maggior parte di Visual Studio Code, le funzionalità di Azure Machine Learning in Visual Studio Code sono accessibili dalla tastiera. La combinazione di tasti più importante da sapere è Ctrl + MAIUSC + P, che consente di visualizzare il riquadro comandi. A questo punto, è possibile accedere a tutte le funzionalità di Visual Studio Code, inclusi i tasti di scelta rapida per le operazioni più comuni.

[![Tasti di scelta rapida per Azure Machine Learning per Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Per una procedura dettagliata sul training con Machine Learning all'esterno di VS Code, leggere [Esercitazione: Eseguire il training di modelli con Azure Machine Learning](tutorial-train-models-with-aml.md).

Per una procedura dettagliata per la modifica, l'esecuzione e il debug del codice in locale, vedere l'[esercitazione Hello World in Python](https://code.visualstudio.com/docs/python/python-tutorial)
