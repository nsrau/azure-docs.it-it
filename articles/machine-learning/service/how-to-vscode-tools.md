---
title: Usare Visual Studio Code per machine learning
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
ms.openlocfilehash: 70f9c34957b977aff9fc6211bf79415ed9abe255
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66016521"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Introduzione ad Azure Machine Learning per Visual Studio Code

In questo articolo si apprenderà come usare l'estensione per Azure Machine Learning per Visual Studio Code per eseguire il training e distribuire modelli di deep learning e machine learning.

Il [servizio di Azure Machine Learning](overview-what-is-azure-ml.md) offre il supporto per le destinazioni di calcolo di esperimenti eseguiti in locale e in remoto. Per ogni esperimento è possibile tenere traccia di più esecuzioni in quanto spesso occorre provare in modo interattivo altre tecniche, iperparametri diversi e altro ancora. È possibile usare Azure Machine Learning per tenere traccia delle metriche personalizzate e delle esecuzioni dell'esperimento, consentendo così la riproducibilità e la verificabilità di data science.

È anche possibile distribuire questi modelli per le esigenze di test e produzione.

## <a name="prerequisites"></a>Prerequisiti

+ Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare il [versione a pagamento o gratuita del servizio Azure Machine Learning](https://aka.ms/AMLFree).

+ È necessario installare Visual Studio Code. Visual Studio Code è un editor di codice sorgente leggero ma potente che viene eseguito sul desktop. Include supporto integrato per Python e altri linguaggi di programmazione. Se non è ancora installato Visual Studio Code [scoprire come](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Installare Python 3.5 o versioni successive](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Installare l'estensione per Azure Machine Learning per Visual Studio Code

Quando si installa l'estensione di Azure Machine Learning, due ulteriori estensioni installate automaticamente (se si ha accesso a internet). Sono le [estensione Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) e il [estensione Python per Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Per usare Azure Machine Learning, è necessario trasformare Visual Studio Code in un ambiente di sviluppo Python integrati (IDE). È necessario usare l'estensione Python di Microsoft [Python in Visual Studio Code](https://code.visualstudio.com/docs/languages/python). Questa estensione viene installata automaticamente con l'estensione di Azure Machine Learning. L'estensione rende Visual Studio Code un IDE eccellente e funziona su qualsiasi sistema operativo con una serie di interpreti Python. L'estensione di Python di Microsoft utilizza tutta la potenza di Visual Studio Code per fornire funzionalità Completamento automatico, IntelliSense, linting, debug e gli unit test. L'estensione consente anche di passare facilmente tra ambienti Python, tra cui virtuale e gli ambienti conda. Per altre informazioni sulla modifica, esecuzione e debug del codice Python, vedere la [esercitazione su Python per hello-world](https://code.visualstudio.com/docs/python/python-tutorial).

Per installare l'estensione di Azure Machine Learning:

1. Aprire Visual Studio Code.

1. In un web browser, passare a [Azure Machine Learning per l'estensione di Visual Studio Code (anteprima)](https://aka.ms/vscodetoolsforai).

1. Nella pagina web, selezionare **installare**. 

1. Nella scheda estensioni, selezionare **installare**.

1. Verrà visualizzata una scheda di completamento dell'installazione per l'estensione in Visual Studio Code e il simbolo di Azure (evidenziato in rosso nello screenshot seguente) viene aggiunto alla barra attività.

   ![Icona di Azure sulla barra delle attività di Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Nella finestra di dialogo, selezionare **Accedi** e seguire le istruzioni per l'autenticazione con Azure. 
   
   L'estensione Azure Account, che è stato installato con Azure Machine Learning per l'estensione di Visual Studio Code, consente di eseguire l'autenticazione con il tuo account Azure. Per un elenco dei comandi, vedere la pagina per il [estensione Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Consultare il [IntelliCode estensione per Visual Studio Code (anteprima)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode hai a disposizione un set di funzionalità basato su intelligenza artificiale per IntelliSense in Python, ad esempio l'inferenza di autocompletions più rilevante in base al contesto codice corrente.

## <a name="install-the-azure-machine-learning-sdk"></a>Installare Azure Machine Learning SDK

1. Assicurarsi che Python 3.5 o versione successiva sia installato e riconosciuto da Visual Studio Code. Se si installa questo punto, riavviare Visual Studio Code e [selezionare un interprete Python](https://code.visualstudio.com/docs/python/python-tutorial).

1. Nella finestra del terminale integrata, specificare l'interprete Python da usare. O premere INVIO per usare l'interprete Python predefinito.

   ![Scegliere l'interprete](./media/vscode-tools-for-ai/python.png)

1. Nell'angolo inferiore destro della finestra, viene visualizzata una notifica, che indica che il [SDK di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) viene installato automaticamente. L'ambiente di Python appena creato è privato e locale e disporre dei prerequisiti di Visual Studio Code per l'uso con il servizio di Azure Machine Learning.

   ![Installare Azure Machine Learning SDK per Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Introduzione ad Azure Machine Learning

Prima di iniziare i corsi di formazione e la distribuzione di modelli di machine learning in Visual Studio Code, è necessario creare un [dell'area di lavoro del servizio di Azure Machine Learning](concept-workspace.md) nel cloud. Questa area di lavoro contiene i modelli e le risorse. 

Per creare un'area di lavoro e aggiungere il primo esperimento:

1. Nella barra delle attività di Visual Studio Code, selezionare l'icona di Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

   [![Creare un'area di lavoro](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Fare clic con il pulsante destro sulla sottoscrizione di Azure e selezionare **Creare area di lavoro**. Viene visualizzato un elenco. Nell'immagine animata di esempio, è il nome della sottoscrizione **versione di valutazione gratuita**, e l'area di lavoro **TeamWorkspace**. 

1. Selezionare un gruppo di risorse dall'elenco o crearne uno nuovo usando la procedura guidata nel riquadro comandi.

1. Nel campo, digitare un nome univoco e non crittografato per la nuova area di lavoro. Nell'immagine di esempio, l'area di lavoro è denominata **TeamWorkspace**.

1. Premere INVIO per creare la nuova area di lavoro. Viene visualizzato nell'albero, sotto il nome della sottoscrizione.

1. Fare doppio clic il **esperimento** nodo, scegliere **crea esperimento** dal menu di scelta rapida.  Gli esperimenti tengono traccia delle esecuzioni usando Azure Machine Learning.

1. Nella casella, immettere un nome per l'esperimento. Negli screenshot di esempio, l'esperimento è denominato **MNIST**.
 
1. Premere INVIO per creare il nuovo esperimento. L'esperimento viene visualizzato nell'albero della sotto il nome dell'area di lavoro.

1. In un'area di lavoro, è possibile fare doppio clic su un esperimento per impostarla come la **Active** fare delle prove. Il **Active** esperimento è l'esperimento corrente. Verrà collegata di Apri cartella in Visual Studio Code per questo esperimento nel cloud. Questa cartella deve contenere gli script di Python locali.

A questo punto le metriche chiave verranno archiviate all'interno della cronologia dell'esperimento. Analogamente, i modelli di cui che si esegue il training verranno automaticamente caricati in Azure Machine Learning e archiviati insieme al tuo esperimento metriche e log. 

[![Collega una cartella in Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Creare e gestire destinazioni di calcolo

Con Azure Machine Learning per Visual Studio Code, è possibile preparare i dati, il training dei modelli e distribuirli in locale e sulle destinazioni di calcolo remoto.

L'estensione supporta diverse destinazioni di calcolo remoto per Azure Machine Learning. Per altre informazioni, vedere l'elenco completo dei supportati [destinazioni di calcolo di Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Creare destinazioni di calcolo di Azure Machine Learning in Visual Studio Code

Per creare una destinazione di calcolo:

1. Nella barra delle attività di Visual Studio Code, selezionare l'icona di Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

2. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning. Nell'immagine di esempio seguente, è il nome della sottoscrizione **versione di valutazione gratuita**, e l'area di lavoro **TeamWorkspace**. 

3. Sotto il nodo dell'area di lavoro fare clic con il pulsante destro del mouse sul nodo **Compute** (Calcolo) e scegliere **Create Compute** (Crea calcolo).

4. Scegliere il tipo di destinazione di calcolo nell'elenco. 

5. Nel riquadro comandi selezionare una dimensione di macchina virtuale.

6. Nel riquadro comandi, immettere un nome per la destinazione di calcolo nel campo. 

7. Nel file di configurazione JSON che si apre in una nuova scheda, specificare le proprietà avanzate. È possibile specificare le proprietà, ad esempio un numero massimo di nodi.

8. Al termine della configurazione di destinazione di calcolo, nell'angolo inferiore destro della finestra, selezionare **Submit**.

Di seguito è riportato un esempio di come creare un calcolo di Azure Machine Learning (AMLCompute):

[![Creare il calcolo di Azure Machine Learning in Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Il file di configurazione di esecuzione

L'estensione di Visual Studio Code crea automaticamente una destinazione di calcolo locale e le configurazioni di esecuzione per gli ambienti locali e di docker nel computer locale. È possibile trovare i file di configurazione di esecuzione sotto il nodo di destinazione di calcolo associate. 

## <a name="train-and-tune-models"></a>Eseguire il training e ottimizzare i modelli

Usare Azure Machine Learning per Visual Studio Code (anteprima) per rapidamente iterazioni del codice, eseguire il debug e usare una soluzione di controllo del codice sorgente. 

Per eseguire l'esperimento in locale usando Azure Machine Learning:

1. Nella barra delle attività di Visual Studio Code, selezionare l'icona di Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning. 

1. Sotto il nodo dell'area di lavoro, espandere la **Compute** nodo e pulsante destro del mouse il **Run Config** dell'attività di calcolo da usare. 

1. Selezionare **Run Experiment** (Esegui esperimento).

1. Da Esplora File, selezionare lo script da eseguire. 

1. Selezionare **Visualizza l'esecuzione esperimento** per visualizzare il portale di Azure Machine Learning integrato per monitorare le esecuzioni e visualizzare i modelli con training.

Ecco un esempio di come eseguire un esperimento in locale:

[![Eseguire un esperimento in locale](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Usare risorse di calcolo remoti per esperimenti in Visual Studio Code

Per usare una destinazione di calcolo remoto per il training, è necessario creare un file di configurazione di esecuzione. Questo file indica ad Azure Machine Learning non solo la posizione in cui eseguire l'esperimento, ma anche come preparare l'ambiente.

#### <a name="the-conda-dependencies-file"></a>File delle dipendenze Conda

Per impostazione predefinita, viene creato un nuovo ambiente di conda e vengono gestite la dipendenze di installazione. Tuttavia, è necessario specificare le dipendenze e le relative versioni nel *aml_config* file. 

Il frammento seguente del valore predefinito *aml_config* specifica `tensorflow=1.12.0`. Se non si specifica la versione della dipendenza, verrà utilizzata la versione più recente. È possibile inserire dipendenze aggiuntive nel file di configurazione.

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

Per eseguire l'esperimento con Azure Machine Learning in un server remoto destinazione di calcolo:

1. Nella barra delle attività di Visual Studio Code, selezionare l'icona di Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning. 

1. Nella finestra dell'editor, lo script Python e scegliere **Azure Machine Learning: Run as Experiment in Azure** (AML: Esegui come esperimento in Azure). 

1. Nel riquadro comandi selezionare la destinazione di calcolo. 

1. Nel riquadro comandi immettere il nome di configurazione di esecuzione nel campo. 

1. Modificare il *conda_dependencies. yml* file per specificare le dipendenze di runtime dell'esperimento. Nell'angolo inferiore destro della finestra, quindi selezionare **Submit**. 

1. Selezionare **Visualizza l'esecuzione esperimento** per visualizzare il portale di Azure Machine Learning integrato per monitorare le esecuzioni e visualizzare i modelli con training.

Ecco un esempio di come eseguire un esperimento in una destinazione di calcolo remoto:

[![Eseguire un esperimento in una destinazione remota](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Distribuire e gestire i modelli
In Azure Machine Learning, è possibile distribuire e gestire i modelli di machine learning nel cloud e nei dispositivi perimetrali. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Registrare il modello di Azure Machine Learning da Visual Studio Code

Ora che è stato eseguito il training del modello, è possibile registrarlo nell'area di lavoro. È possibile tenere traccia e distribuire modelli registrati.

Per registrare il modello:

1. Nella barra delle attività di Visual Studio Code, selezionare l'icona di Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning.

1. Sotto il nodo dell'area di lavoro fare clic con il pulsante destro del mouse su **Models** (Modelli) e scegliere **Register Model** (Registra modello).

1. Nel riquadro comandi, immettere un nome di modello nel campo. 

1. Nell'elenco, scegliere se caricare un **file di modello** (per i modelli singoli) o una **cartella di modello** (per i modelli con più file, ad esempio TensorFlow). 

1. Selezionare la cartella o il file.

1. Al termine della configurazione delle proprietà di modello, nell'angolo inferiore destro della finestra, selezionare **Submit**. 

Ecco un esempio di come registrare il modello di Azure Machine Learning:

[![Registrazione di un modello in AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Distribuire il servizio da Visual Studio Code

In Visual Studio Code, è possibile distribuire il servizio web:
+ Azure le istanze di contenitore per i test.
+ Azure Kubernetes Service (AKS) per la produzione.

Non è necessario creare un contenitore ACI per verificare in anticipo, poiché i contenitori ACI vengono creati in tempo reale. Tuttavia, è necessario configurare cluster servizio contenitore di AZURE in anticipo. Per altre informazioni, vedere [distribuire modelli con il servizio di Azure Machine Learning](how-to-deploy-and-where.md).

Per distribuire un servizio web:

1. Nella barra delle attività di Visual Studio Code, selezionare l'icona di Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning.

1. Sotto il nodo dell'area di lavoro espandere il nodo **Models** (Modelli).

1. Fare doppio clic il modello da distribuire e scegliere **distribuzione servizio dal modello registrato** dal menu di scelta rapida.

1. Nel riquadro comandi, scegliere la destinazione di calcolo da distribuire per. 

1. Nel riquadro comandi, immettere un nome per questo servizio nel campo.  

1. Nel riquadro comandi selezionare il tasto INVIO sulla tastiera per cercare e selezionare il file di script.

1. Nel riquadro comandi selezionare il tasto INVIO sulla tastiera per cercare e selezionare il file delle dipendenze conda.

1. Al termine della configurazione delle proprietà del servizio, nell'angolo inferiore destro della finestra, selezionare **Submit** per la distribuzione. Nel file di proprietà del servizio, è possibile specificare un file di docker locale o un file di schema. JSON.

Il servizio Web è ora distribuito.

Ecco un esempio di come distribuire un servizio web:

[![Distribuire un servizio web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Usare i tasti di scelta rapida

È possibile usare la tastiera per accedere alle funzionalità di Azure Machine Learning in Visual Studio Code. Tasti di scelta rapida più importanti da sapere sono Ctrl + MAIUSC + P, che consente di visualizzare il riquadro comandi. Nel riquadro comandi, è possibile accedere a tutte le funzionalità di Visual Studio Code, inclusi i tasti di scelta rapida per le operazioni più comuni.

[![Tasti di scelta rapida per Azure Machine Learning per Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Per una procedura dettagliata di come eseguire il training con Azure Machine Learning all'esterno di Visual Studio Code, vedere [esercitazione: Eseguire il training di modelli con Azure Machine Learning](tutorial-train-models-with-aml.md).
* Per una procedura dettagliata per modificare, eseguire e il debug del codice in locale, vedere la [esercitazione su Python per hello-world](https://code.visualstudio.com/docs/python/python-tutorial).
