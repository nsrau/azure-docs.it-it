---
title: Usare Visual Studio Code per Machine Learning
titleSuffix: Azure Machine Learning service
description: Informazioni su come installare Azure Machine Learning per Visual Studio Code e creare un semplice esperimento in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 7/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3b49def1af7f13e38c3a9daea32d56bf3c633261
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871758"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Introduzione ad Azure Machine Learning per Visual Studio Code

In questo articolo si apprenderà come usare l'estensione per Azure Machine Learning per Visual Studio Code per eseguire il training e distribuire modelli di machine learning e Deep Learning.

Il [servizio Azure Machine Learning](overview-what-is-azure-ml.md) fornisce supporto per gli esperimenti eseguiti localmente e sulle destinazioni di calcolo remote. Per ogni esperimento è possibile tenere traccia di più esecuzioni in quanto spesso occorre provare in modo interattivo altre tecniche, iperparametri diversi e altro ancora. È possibile usare Azure Machine Learning per tenere traccia delle metriche personalizzate e delle esecuzioni dell'esperimento, consentendo così la riproducibilità e la verificabilità di data science.

È anche possibile distribuire questi modelli in base alle esigenze di test e produzione.

## <a name="prerequisites"></a>Prerequisiti

+ Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Prova la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

+ È necessario installare Visual Studio Code. Visual Studio Code è un editor di codice sorgente leggero ma potente che viene eseguito sul desktop. È dotato del supporto incorporato per Python e altri linguaggi di programmazione. Se non è già stato installato Visual Studio Code, [Scopri come](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Installare Python 3,5 o versione successiva](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Installare l'estensione per Azure Machine Learning per Visual Studio Code

Quando si installa l'estensione Azure Machine Learning, vengono installate automaticamente due altre estensioni (se si dispone di accesso a Internet). Si tratta dell' [estensione dell'account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) e dell' [estensione Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Per lavorare con Azure Machine Learning, è necessario trasformare Visual Studio Code in un Integrated Development Environment Python (IDE). Per usare [Python in Visual Studio Code](https://code.visualstudio.com/docs/languages/python), è necessaria l'estensione Microsoft Python. Questa estensione viene installata automaticamente con l'estensione Azure Machine Learning. L'estensione rende Visual Studio Code un IDE eccellente e funziona in qualsiasi sistema operativo con un'ampia gamma di interpreti Python. Microsoft Python Extension usa tutta la potenza di Visual Studio Code per fornire funzionalità di completamento automatico, IntelliSense, pelucchi, debug e testing unità. L'estensione consente inoltre di passare facilmente tra gli ambienti Python, inclusi gli ambienti virtuali e conda. Per altre informazioni sulla modifica, l'esecuzione e il debug del codice Python, vedere l' [esercitazione Hello-World di Python](https://code.visualstudio.com/docs/python/python-tutorial).

Per installare l'estensione Azure Machine Learning:

1. Aprire Visual Studio Code.

1. In un Web browser passare a [Azure Machine Learning per Visual Studio Code Extension (anteprima)](https://aka.ms/vscodetoolsforai).

1. Nella pagina Web selezionare **Installa**. 

1. Nella scheda estensione selezionare **Installa**.

1. Viene aperta una scheda iniziale per l'estensione in Visual Studio Code e il simbolo di Azure, descritto in rosso nello screenshot seguente, viene aggiunto alla barra attività.

   ![Icona di Azure sulla barra delle attività di Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Nella finestra di dialogo selezionare **Accedi** e seguire le istruzioni per l'autenticazione con Azure. 
   
   L'estensione dell'account Azure, che è stata installata insieme alla Azure Machine Learning per Visual Studio Code estensione, consente di eseguire l'autenticazione con l'account Azure. Per un elenco di comandi, vedere la pagina relativa all' [estensione dell'account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Vedere l' [estensione IntelliCode per Visual Studio Code (anteprima)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode fornisce un set di funzionalità di intelligenza artificiale per IntelliSense in Python, ad esempio l'inferenza dei completamenti automatico più rilevanti in base al contesto del codice corrente.

## <a name="install-the-azure-machine-learning-sdk"></a>Installare l'SDK di Azure Machine Learning

1. Verificare che sia installato Python 3,5 o versione successiva e che sia riconosciuto dal Visual Studio Code. Se lo si installa ora, riavviare Visual Studio Code e [selezionare un interprete Python](https://code.visualstudio.com/docs/python/python-tutorial).

1. Nella finestra del terminale integrato specificare l'interprete Python da usare. In alternativa, premere INVIO per usare l'interprete Python predefinito.

   ![Scegliere l'interprete](./media/vscode-tools-for-ai/python.png)

1. Nell'angolo inferiore destro della finestra viene visualizzata una notifica che indica che è in corso l'installazione automatica di [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) . L'ambiente Python appena creato è locale e privato e include i prerequisiti Visual Studio Code per l'uso del servizio Azure Machine Learning.

   ![Installare Azure Machine Learning SDK per Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Introduzione ad Azure Machine Learning

Prima di iniziare a eseguire il training e distribuire modelli di Machine Learning in Visual Studio Code, è necessario creare un' [area di lavoro del servizio Azure Machine Learning](concept-workspace.md) nel cloud. Questa area di lavoro conterrà i modelli e le risorse. 

Per creare un'area di lavoro e aggiungere il primo esperimento:

1. Nella barra attività Visual Studio Code selezionare l'icona Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

   [![Creare un'area di lavoro](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Fare clic con il pulsante destro sulla sottoscrizione di Azure e selezionare **Creare area di lavoro**. Viene visualizzato un elenco. Nell'immagine animata di esempio, il nome della sottoscrizione è una **versione di valutazione gratuita**e l'area di lavoro è **TeamWorkspace**. 

1. Selezionare un gruppo di risorse dall'elenco o crearne uno nuovo usando la procedura guidata nel riquadro comandi.

1. Nel campo, digitare un nome univoco e non crittografato per la nuova area di lavoro. Nell'immagine di esempio, l'area di lavoro è denominata **TeamWorkspace**.

1. Premere INVIO per creare la nuova area di lavoro. Viene visualizzato nell'albero sotto il nome della sottoscrizione.

1. Fare clic con il pulsante destro del mouse sul nodo **esperimento** e scegliere **Crea esperimento** dal menu di scelta rapida.  Gli esperimenti tengono traccia delle esecuzioni usando Azure Machine Learning.

1. Nel campo immettere un nome per l'esperimento. Nelle schermate di esempio l'esperimento è denominato **MNIST**.
 
1. Premere INVIO per creare il nuovo esperimento. L'esperimento viene visualizzato nell'albero sotto il nome dell'area di lavoro.

1. In un'area di lavoro è possibile fare clic con il pulsante destro del mouse su un esperimento per impostarlo come esperimento **attivo** . L'esperimento **attivo** è l'esperimento corrente. La cartella aperta in Visual Studio Code sarà collegata a questo esperimento nel cloud. Questa cartella deve contenere gli script di Python locali.

Ora le metriche chiave verranno archiviate nella cronologia dell'esperimento. Analogamente, i modelli sottoposti a training verranno caricati automaticamente in Azure Machine Learning e archiviati insieme alle metriche e ai log dell'esperimento. 

[![Alleghi una cartella in Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Creare e gestire destinazioni di calcolo

Con Azure Machine Learning per Visual Studio Code è possibile preparare i dati, eseguire il training di modelli e distribuirli sia localmente che su destinazioni di calcolo remote.

L'estensione supporta diverse destinazioni di calcolo Remote per Azure Machine Learning. Per ulteriori informazioni, vedere l'elenco completo delle [destinazioni di calcolo supportate per Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Creare destinazioni di calcolo per Azure Machine Learning in Visual Studio Code

Per creare una destinazione di calcolo:

1. Nella barra attività Visual Studio Code selezionare l'icona Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

2. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning. Nell'immagine di esempio seguente, il nome della sottoscrizione è una **versione di valutazione gratuita**e l'area di lavoro è **TeamWorkspace**. 

3. Sotto il nodo dell'area di lavoro fare clic con il pulsante destro del mouse sul nodo **Compute** (Calcolo) e scegliere **Create Compute** (Crea calcolo).

4. Scegliere il tipo di destinazione di calcolo nell'elenco. 

5. Nel riquadro comandi selezionare le dimensioni della macchina virtuale.

6. Nel riquadro comandi, nel campo, immettere un nome per la destinazione di calcolo. 

7. Nel file di configurazione JSON che viene aperto in una nuova scheda specificare le proprietà avanzate. È possibile specificare proprietà come il numero massimo di nodi.

8. Al termine della configurazione della destinazione di calcolo, nell'angolo inferiore destro della finestra selezionare **Invia**.

Ecco un esempio di come creare un Azure Machine Learning calcolo (AMLCompute):

[![Creare un calcolo AML in Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Il file di configurazione di esecuzione

L'estensione Visual Studio Code crea automaticamente una destinazione di calcolo locale ed esegue le configurazioni per gli ambienti locali e Docker nel computer locale. È possibile trovare i file di configurazione di esecuzione nel nodo di destinazione di calcolo associato. 

## <a name="train-and-tune-models"></a>Eseguire il training e ottimizzare i modelli

Usare Azure Machine Learning per Visual Studio Code (anteprima) per eseguire rapidamente l'iterazione sul codice, eseguire un'istruzione alla volta ed eseguire il debug e usare la soluzione per il controllo del codice sorgente. 

Per eseguire l'esperimento localmente usando Azure Machine Learning:

1. Nella barra attività Visual Studio Code selezionare l'icona Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning. 

1. Nel nodo area di lavoro espandere il nodo **calcolo** e fare clic con il pulsante destro del mouse sulla **configurazione di esecuzione** del calcolo che si vuole usare. 

1. Selezionare **Run Experiment** (Esegui esperimento).

1. In Esplora file selezionare lo script che si desidera eseguire. 

1. Selezionare **Visualizza esecuzione esperimento** per visualizzare il portale integrato Azure Machine Learning per monitorare le esecuzioni e visualizzare i modelli sottoposti a training.

Ecco un esempio di come eseguire un esperimento in locale:

[![Eseguire un esperimento localmente](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Usare i calcoli remoti per gli esperimenti in Visual Studio Code

Per usare una destinazione di calcolo remota per il training, è necessario creare un file di configurazione di esecuzione. Questo file indica ad Azure Machine Learning non solo la posizione in cui eseguire l'esperimento, ma anche come preparare l'ambiente.

#### <a name="the-conda-dependencies-file"></a>File delle dipendenze Conda

Per impostazione predefinita, viene creato automaticamente un nuovo ambiente conda e vengono gestite le dipendenze di installazione. Tuttavia, è necessario specificare le dipendenze e le relative versioni nel file *aml_config/conda_dependencies. yml* . 

Il frammento di codice seguente del valore predefinito *aml_config/conda_dependencies. yml* specifica `tensorflow=1.12.0`. Se non si specifica la versione della dipendenza, verrà usata la versione più recente. È possibile inserire dipendenze aggiuntive nel file di configurazione.

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

Per eseguire l'esperimento con Azure Machine Learning in una destinazione di calcolo remota:

1. Nella barra attività Visual Studio Code selezionare l'icona Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning. 

1. Nella finestra dell'editor fare clic con il pulsante destro del mouse sullo script **Python e selezionare AML: Run as Experiment in Azure** (AML: Esegui come esperimento in Azure). 

1. Nel riquadro comandi selezionare la destinazione di calcolo. 

1. Nel riquadro comandi, nel campo, immettere il nome della configurazione di esecuzione. 

1. Modificare il file *conda_dependencies. yml* per specificare le dipendenze di runtime dell'esperimento. Quindi, nell'angolo in basso a destra della finestra, selezionare **Submit (Invia**). 

1. Selezionare **Visualizza esecuzione esperimento** per visualizzare il portale integrato Azure Machine Learning per monitorare le esecuzioni e visualizzare i modelli sottoposti a training.

Ecco un esempio di come eseguire un esperimento in una destinazione di calcolo remota:

[![Eseguire un esperimento in una destinazione remota](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Distribuire e gestire i modelli
In Azure Machine Learning, è possibile distribuire e gestire i modelli di machine learning nel cloud e nei dispositivi perimetrali. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Registrare il modello in Azure Machine Learning da Visual Studio Code

Ora che è stato eseguito il training del modello, è possibile registrarlo nell'area di lavoro. È possibile tenere traccia dei modelli registrati e distribuirli.

Per registrare il modello:

1. Nella barra attività Visual Studio Code selezionare l'icona Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning.

1. Sotto il nodo dell'area di lavoro fare clic con il pulsante destro del mouse su **Models** (Modelli) e scegliere **Register Model** (Registra modello).

1. Nel riquadro comandi, nel campo, immettere un nome di modello. 

1. Dall'elenco scegliere se caricare un **file di modello** (per i singoli modelli) o una **cartella del modello** (per i modelli con più file, ad esempio TensorFlow). 

1. Selezionare la cartella o il file.

1. Al termine della configurazione delle proprietà del modello, nell'angolo inferiore destro della finestra selezionare **Invia**. 

Di seguito è riportato un esempio di come registrare il modello per Azure Machine Learning:

[![Registrazione di un modello in AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Distribuire il servizio da Visual Studio Code

In Visual Studio Code, è possibile distribuire il servizio Web in:
+ Istanze di contenitore di Azure (ACI) per il test.
+ Azure Kubernetes Service (AKS) per la produzione.

Non è necessario creare un contenitore ACI da testare in anticipo, perché i contenitori ACI vengono creati in tempo reale. Tuttavia, è necessario configurare in anticipo i cluster AKS. Per altre informazioni, vedere [distribuire modelli con il servizio Azure Machine Learning](how-to-deploy-and-where.md).

Per distribuire un servizio Web:

1. Nella barra attività Visual Studio Code selezionare l'icona Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione struttura ad albero espandere la sottoscrizione di Azure e l'area di lavoro del servizio Azure Machine Learning.

1. Sotto il nodo dell'area di lavoro espandere il nodo **Models** (Modelli).

1. Fare clic con il pulsante destro del mouse sul modello che si desidera distribuire, quindi scegliere **Distribuisci servizio dal modello registrato** dal menu di scelta rapida.

1. Nel riquadro comandi scegliere la destinazione di calcolo in cui si vuole eseguire la distribuzione. 

1. Nel riquadro comandi, nel campo, immettere un nome per il servizio.  

1. Nel riquadro comandi selezionare il tasto INVIO sulla tastiera per cercare e selezionare il file di script.

1. Nel riquadro comandi selezionare il tasto INVIO sulla tastiera per cercare e selezionare il file di dipendenza conda.

1. Al termine della configurazione delle proprietà del servizio, nell'angolo inferiore destro della finestra selezionare **Invia** per distribuire. Nel file delle proprietà del servizio è possibile specificare un file Docker locale o un file schema. JSON.

Il servizio Web è ora distribuito.

Di seguito è riportato un esempio di come distribuire un servizio Web:

[![Distribuire un servizio Web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Usare i tasti di scelta rapida

È possibile utilizzare la tastiera per accedere alle funzionalità di Azure Machine Learning Visual Studio Code. Il tasto di scelta rapida più importante da tenere presente è CTRL + MAIUSC + P, che consente di visualizzare il riquadro comandi. Dal riquadro comandi è possibile accedere a tutte le funzionalità di Visual Studio Code, incluse le scelte rapide da tastiera per le operazioni più comuni.

[![Tasti di scelta rapida per Azure Machine Learning per Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Per una procedura dettagliata su come eseguire il training con Azure machine learning all'esterno di [Visual Studio Code, vedere Esercitazione: Eseguire il training di modelli con Azure Machine Learning](tutorial-train-models-with-aml.md).
* Per una procedura dettagliata su come modificare, eseguire ed eseguire il debug di codice localmente, vedere l'esercitazione relativa a [Python Hello-World](https://code.visualstudio.com/docs/python/python-tutorial).
