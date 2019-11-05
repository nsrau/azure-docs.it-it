---
title: Usare Visual Studio Code per Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come installare Azure Machine Learning per Visual Studio Code e creare un esperimento in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jimgries
author: greazer
ms.date: 09/20/2019
ms.custom: seodec18
ms.openlocfilehash: df23c41629dfe86058f0f2fb6602e85fd5a6d494
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489299"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Introduzione ad Azure Machine Learning per Visual Studio Code

Questo articolo illustra come usare la **Azure Machine Learning per Visual Studio Code** estensione per eseguire il training e distribuire modelli di machine learning.

Il [Azure Machine Learning](overview-what-is-azure-ml.md) semplifica la creazione, il training e la distribuzione dei modelli di machine learning.
+ Per il training, fornisce il supporto per l'esecuzione di esperimenti in locale o in remoto. Per ogni esperimento è possibile registrare metriche personalizzate di più esecuzioni per ottimizzare gli iperparametri
+ È anche possibile usare la Azure Machine Learning per distribuire facilmente modelli di apprendimento automatico per le esigenze di test e produzione.

## <a name="prerequisites"></a>Prerequisiti

+ Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Prova la [versione gratuita o a pagamento del Azure Machine Learning](https://aka.ms/AMLFree).

+ Installare [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), un editor di codice leggero che viene eseguito in Windows, Mac e Linux.

+ [Installare Python 3,5 o versione successiva](https://www.anaconda.com/download/).


## <a name="install-the-extension"></a>Installare l'estensione

Quando si installa l'estensione Azure Machine Learning, vengono installate automaticamente due altre estensioni. Si tratta dell' [estensione dell'account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) e dell' [estensione Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-Python.Python). Per altre informazioni sull'uso dell'estensione Python per la modifica, l'esecuzione e il debug del codice Python, vedere l'esercitazione relativa a [Python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).

Per installare l'estensione Azure Machine Learning:

1. Aprire Visual Studio Code.

1. Passare alla scheda estensioni e cercare "Azure Machine Learning".

1. Nella scheda estensione selezionare **Installa**.

1. Viene aperta una scheda iniziale per l'estensione in Visual Studio Code e il simbolo di Azure, evidenziato in rosso nello screenshot seguente, viene aggiunto alla barra attività.

   ![Icona di Azure sulla barra delle attività di Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Nella finestra di dialogo selezionare **Accedi** e seguire le istruzioni per l'autenticazione con Azure.

   L'estensione dell'account Azure, che è stata installata insieme alla Azure Machine Learning per Visual Studio Code estensione, consente di eseguire l'autenticazione con l'account Azure. Per un elenco di comandi, vedere la pagina relativa all' [estensione dell'account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!TIP]
> È anche possibile scaricare il programma di installazione dell'estensione direttamente da [Azure Machine Learning per Visual Studio Code Extension (anteprima)](https://aka.ms/vscodetoolsforai).

## <a name="quickstart-with-azure-machine-learning"></a>Guida introduttiva con Azure Machine Learning
Esistono diversi modi per eseguire gli script di training usando Azure Machine Learning. Se si è appena iniziato, si analizzerà prima di tutto come inviare rapidamente uno script di training per l'esecuzione in Azure.

Se si ha già familiarità con i concetti di Azure Machine Learning e si desiderano altre informazioni su come gestirli e usarli con l'estensione, vedere [Azure Machine Learning approfondita con vs code](./how-to-vscode-tools.md#azure-machine-learning-in-depth-with-vs-code) di seguito.

## <a name="run-an-existing-python-training-script-in-azure"></a>Eseguire uno script di training Python esistente in Azure
Se si dispone di uno script di training esistente, l'estensione Azure Machine Learning per VS Code non solo fornisce un'ottima esperienza di modifica, debug e gestione del codice sorgente, ma consente anche di eseguire e archiviare le metriche per lo script in Azure.

Di seguito sono riportati i requisiti iniziali. È possibile usare uno script di training personalizzato se è pronto oppure clonare il [repository di esempio VSCODE-Tools-for-intelligenza artificiale](https://github.com/microsoft/vscode-tools-for-ai). Si tratta del repository pubblico per la presentazione di problemi relativi a questa estensione. Contiene anche una piccola cartella di esempio **mnist** che verrà usata per questo esempio.

1. Aprire la cartella **mnist** in vs code.

1. Creare un nuovo ambiente Python usando il pacchetto dell'ambiente virtuale preferito o Anaconda e installare i pacchetti TensorFlow e numpy.

1. Selezionare il nuovo ambiente creato come interprete Python nell'angolo inferiore sinistro della barra di stato VS Code.

1. Aprire **Train.py** ed eseguirlo aprendo il debugger e premendo il pulsante Esegui (o semplicemente premendo F5).

   [![eseguire il training MNIST](./media/vscode-tools-for-ai/run-mnist.gif)](./media/vscode-tools-for-ai/run-mnist.gif#lightbox)

Se tutti gli elementi sono installati correttamente, lo script verrà eseguito e creerà un modello TensorFlow nella cartella Outputs.

[![Mostra il modello TensorFlow](./media/vscode-tools-for-ai/show-tensorflow-model.gif)](./media/vscode-tools-for-ai/show-tensorflow-model.gif#lightbox)

Ora che si è certi che lo script viene eseguito correttamente, è possibile eseguirlo in Azure.

Questa operazione può essere eseguita facilmente senza alcuna modifica aggiuntiva di **Train.py**. Tuttavia, con poche semplici modifiche, è possibile usare Azure Machine Learning per tenere traccia automaticamente di metriche importanti scelte in merito a ogni esecuzione di training.

### <a name="make-azure-aware-of-your-run-metrics"></a>Azure è in grado di riconoscere le metriche di esecuzione
Per modificare il progetto in modo che Azure possa essere tenuto a conoscenza di importanti informazioni nelle esecuzioni:

1. Creare un file denominato **amlrun.py** nella stessa cartella di **Train.py**

    ```Python
    import azureml
    from azureml.core import Run

    # access the Azure ML run
    # init run param to check if running within AML
    def get_AMLRun():
        try:
            run = Run.get_submitted_run()
            return run
        except Exception as e:
            print("Caught = {}".format(e.message))
            return None
    ```

2. Importare il file amlrun in **Train.py**

    ```Python
    ...
    from utils import prepare_data
    from amlrun import get_AMLRun
    ...
    ```
3. Inizializzare l'oggetto Run in **Train.py**

    ```Python
    ...
    init = tf.global_variables_initializer()
    saver = tf.train.Saver()
    run = get_AMLRun()
    ...
    ```
4. Registrare le metriche in Azure con la funzione Run. log ():

    ```Python
    ...
            acc_val = acc_op.eval(feed_dict = {X: X_test, y: y_test})

            # log accuracies to AML logger if using AML
            if run != None:
                run.log('Validation Accuracy', np.float(acc_val))
                run.log('Training Accuracy', np.float(acc_train))

            print(epoch, '-- Training accuracy:', acc_train, '\b Validation
    ...
    ```
### <a name="run-the-script-in-azure"></a>Eseguire lo script in Azure
L'operazione è terminata. Ora è sufficiente usare l'estensione per eseguire lo script nel cloud. Si noti che il video seguente illustra la libertà di comprimere la quantità di tempo necessaria per creare una nuova area di lavoro e calcolo di Azure ML, oltre al tempo necessario per eseguire lo script di training.

   [![avviare un esperimento di Azure ML](./media/vscode-tools-for-ai/start-golden-path.gif)](./media/vscode-tools-for-ai/start-golden-path.gif#lightbox)

Dopo aver fatto clic sul pulsante Esegui esperimento, rispondere ai prompt come indicato di seguito:

1. Scegliere la sottoscrizione di Azure
1. Scegliere di creare una *nuova* area di lavoro di Azure ml
1. Scegliere da un set di modelli preconfigurati per inizializzare l'ambiente Python per le esecuzioni. I modelli forniscono un punto di partenza e includono le impostazioni per:
    1. **PyTorch**, **TensorFlow**o **Scikit-learn**
    1. Training di calcolo **singolo** o **distribuito**
    1. **Generale** per ambienti personalizzati
1. Assicurarsi che l'elenco dei pacchetti PIP e conda sia completo per lo script aggiungendo eventuali pacchetti non inclusi nel modello.
1. Esaminare i nomi predefiniti e le specifiche per l'esecuzione dell'esperimento e fare clic sul collegamento **Invia esperimento** nel file JSON. Il file JSON non verrà salvato perché è sufficiente per rivedere o modificare le impostazioni dell'esperimento prima dell'invio.
1. È possibile riposare e rilassarsi mentre l'estensione consente di impostare tutti gli elementi e di eseguire lo script.

    [![Train nel cloud](./media/vscode-tools-for-ai/run-golden-path.gif)](./media/vscode-tools-for-ai/run-golden-path.gif#lightbox)

In pochi secondi si riceverà una notifica che indica che l'esperimento è stato inviato ad Azure e che è possibile visualizzarne lo stato di avanzamento in Azure Machine Learning Studio facendo clic sul collegamento **Visualizza esecuzione esperimento** nella notifica vs code o all'interno vs code da Premere il pulsante Aggiorna nella scheda Azure.

Al momento, la visualizzazione delle metriche di esecuzione è supportata solo in studio. Il collegamento **Visualizza esecuzione esperimento** indicato in precedenza consente di passare all'esecuzione in cui verranno visualizzate le metriche registrate.
[![eseguire l'esperimento nel portale](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG)](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG#lightbox)

## <a name="azure-machine-learning-in-depth-with-vs-code"></a>Azure Machine Learning approfondita con VS Code

Nella procedura dettagliata precedente è stato inviato un esperimento seguendo il percorso più semplice. Come si può notare, l'estensione consente di ridurre al minimo i passaggi necessari per gestire l'esecuzione di un esperimento. In questa sezione verrà illustrato come è possibile gestire tutti i concetti di Azure Machine Learning singolarmente, garantendo il massimo controllo.

Prima di iniziare a eseguire il training e distribuire modelli di Machine Learning in Visual Studio Code, è necessario creare un' [area di lavoro Azure Machine Learning](concept-workspace.md) nel cloud. Questa area di lavoro conterrà i modelli e le risorse.

### <a name="create-a-workspace"></a>Creare un'area di lavoro

1. Nella barra attività Visual Studio Code selezionare l'icona Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

    [![creare un'area di lavoro](./media/vscode-tools-for-ai/create-workspace.gif)](./media/vscode-tools-for-ai/create-workspace.gif#lightbox)


1. Fare clic con il pulsante destro sulla sottoscrizione di Azure e selezionare **Creare area di lavoro**. Per impostazione predefinita, viene generato un nome che contiene la data e l'ora di creazione. Modificare il nome in **TeamWorkspace** e premere INVIO.

1. Selezionare un gruppo di risorse dall'elenco se si sa quale scegliere o crearne uno nuovo. Se ne viene creato uno nuovo, scegliere un percorso più vicino alla posizione in cui si intende distribuire il modello. In questo caso, abbiamo scelto **Stati Uniti occidentali 2**.

1. Dopo aver premuto INVIO, Azure Machine Learning riceverà la richiesta di creazione dell'area di lavoro. Si riceverà una notifica del processo nell'area di notifica del Visual Studio Code.

1. Espandere il nodo sottoscrizione per trovare l'area di lavoro appena creata.

### <a name="create-an-experiment"></a>Creare un esperimento
È possibile creare uno o più esperimenti nell'area di lavoro per tenere traccia e analizzare le esecuzioni dei singoli modelli di training. Le esecuzioni possono essere eseguite nel cloud di Azure o nel computer locale.

1. Espandere l'area di lavoro **TeamWorkspace** . Fare clic con il pulsante destro del mouse sul nodo **esperimenti** e scegliere **Crea esperimento** dal menu di scelta rapida.

1. Nella finestra di messaggio immettere un nome per l'esperimento. Nelle schermate di esempio l'esperimento è denominato **MNIST**.

1. Premere INVIO per creare il nuovo esperimento. Il nuovo esperimento viene visualizzato nell'albero come figlio del nodo **esperimenti** .

1. In un'area di lavoro è possibile fare clic con il pulsante destro del mouse su un esperimento per impostarlo come esperimento **attivo** . L'esperimento **attivo** collega l'esperimento nel cloud alla cartella attualmente aperta in Visual Studio Code. Questa cartella deve contenere gli script di Python locali. Impostando un esperimento attivo, le metriche chiave per tutte le esecuzioni di training vengono archiviate all'interno dell'esperimento, indipendentemente dalla posizione in cui vengono eseguite.

    [![creare un esperimento](./media/vscode-tools-for-ai/create-experiment.gif)](./media/vscode-tools-for-ai/create-experiment.gif#lightbox)


### <a name="create-and-manage-compute-targets"></a>Creare e gestire destinazioni di calcolo

Con Azure Machine Learning per Visual Studio Code è possibile preparare i dati, eseguire il training di modelli e distribuirli sia localmente che su destinazioni di calcolo remote.

L'estensione supporta diverse destinazioni di calcolo Remote per Azure Machine Learning. Per ulteriori informazioni, vedere l'elenco completo delle [destinazioni di calcolo supportate per Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Creare destinazioni di calcolo per Azure Machine Learning in Visual Studio Code

Per creare una destinazione di calcolo:

1. Nella barra attività Visual Studio Code selezionare l'icona Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione albero espandere la sottoscrizione di Azure e Azure Machine Learning area di lavoro.

1. Sotto il nodo dell'area di lavoro fare clic con il pulsante destro del mouse sul nodo **Compute** (Calcolo) e scegliere **Create Compute** (Crea calcolo).

1. Scegliere il tipo di destinazione di calcolo nell'elenco.

1. Nel prompt del riquadro comandi selezionare le dimensioni della macchina virtuale. È possibile filtrare i calcoli con testo, ad esempio "GPU".

1. Nel prompt del riquadro comandi immettere un nome per la destinazione di calcolo.

1. Dopo aver immesso il nome, il calcolo verrà creato usando i parametri predefiniti. Per modificare i parametri, fare clic con il pulsante destro del mouse sul nuovo calcolo e scegliere **Modifica calcolo**.

1. Nel file JSON visualizzato, apportare le modifiche desiderate, quindi fare clic sul CodeLens "Salva e continua" (usando la tastiera è possibile premere **CTRL + MAIUSC + p** per richiamare il riquadro comandi ed eseguire il comando **Azure ml: Salva e continua** )

Ecco un esempio di come creare e modificare un Azure Machine Learning Compute (AMLCompute):

[![creare un calcolo di AML in Visual Studio Code](./media/vscode-tools-for-ai/create-remote-compute.gif)](./media/vscode-tools-for-ai/create-remote-compute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Il file di configurazione di esecuzione

Per eseguire un esperimento di Azure Machine Learning su un calcolo, il calcolo deve essere configurato in modo appropriato. Un file di configurazione di esecuzione è il meccanismo mediante il quale viene specificato questo ambiente.

Di seguito è riportato un esempio di come creare una configurazione di esecuzione per AmlCompute, creata in precedenza.

[![creare una configurazione di esecuzione per un calcolo](./media/vscode-tools-for-ai/create-runconfig.gif)](./media/vscode-tools-for-ai/create-runconfig.gif#lightbox)

Per eseguire esperimenti di Azure ML nel computer locale, è ancora necessario un file di configurazione di esecuzione. Quando si crea una configurazione di esecuzione locale, l'ambiente Python utilizzato utilizzerà per impostazione predefinita il percorso dell'interprete impostato all'interno VS Code.

### <a name="train-and-tune-models"></a>Eseguire il training e ottimizzare i modelli

Usando l'estensione Azure ML per VS Code sono disponibili diversi modi per eseguire uno script di training in un esperimento.

1. Fare clic con il pulsante destro del mouse sullo script di training e scegliere **Azure ml: Esegui come esperimento in Azure**
1. Fare clic sull'icona Esegui la barra degli strumenti dell'esperimento.
1. Fare clic con il pulsante destro del mouse su un nodo esecuzione configurazione.
1. Usare il riquadro comandi VS Code per eseguire **Azure ml: Esegui esperimento**

Per eseguire un esperimento di Azure Machine Learning:

1. Nella barra attività Visual Studio Code selezionare l'icona Azure.

1. Nella visualizzazione albero espandere la sottoscrizione di Azure e Azure Machine Learning area di lavoro.

1. Nel nodo area di lavoro espandere il nodo **esperimenti** , quindi fare clic con il pulsante destro del mouse sull'esperimento che si desidera eseguire.

1. Selezionare **Run Experiment** (Esegui esperimento).

1. Scegliere il nome del file Python che si vuole eseguire per eseguire il training del modello e premere INVIO per inviare l'esecuzione. Nota: il file scelto deve trovarsi nella cartella attualmente aperta in VS Code.

1. Dopo l'invio dell'esecuzione, verrà visualizzato un **nodo Run** sotto l'esperimento scelto. Utilizzare questo nodo per monitorare lo stato delle esecuzioni. Nota: potrebbe essere necessario aggiornare periodicamente la finestra per visualizzare lo stato più recente.

Ecco un esempio di come eseguire un esperimento nel calcolo creato in precedenza:

[![eseguire un esperimento localmente](./media/vscode-tools-for-ai/run-experiment.gif)](./media/vscode-tools-for-ai/run-experiment.gif#lightbox)

### <a name="deploy-and-manage-models"></a>Distribuire e gestire i modelli
In Azure Machine Learning, è possibile distribuire e gestire i modelli di machine learning nel cloud e nei dispositivi perimetrali.

#### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Registrare il modello in Azure Machine Learning da Visual Studio Code

Ora che è stato eseguito il training del modello, è possibile registrarlo nell'area di lavoro. È possibile tenere traccia dei modelli registrati e distribuirli.

Per registrare il modello:

1. Nella barra attività Visual Studio Code selezionare l'icona Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione albero espandere la sottoscrizione di Azure e Azure Machine Learning area di lavoro.

1. Sotto il nodo dell'area di lavoro fare clic con il pulsante destro del mouse su **Models** (Modelli) e scegliere **Register Model** (Registra modello).

1. Nel riquadro comandi, nel campo, immettere un nome di modello.

1. Dall'elenco scegliere se caricare un **file di modello** (per i singoli modelli) o una **cartella del modello** (per i modelli con più file, ad esempio TensorFlow).

1. Selezionare la cartella o il file.

1. Al termine della configurazione delle proprietà del modello, nell'angolo inferiore destro della finestra selezionare **Invia**.

Di seguito è riportato un esempio di come registrare il modello per Azure Machine Learning:

[![Registrazione di un modello in AML](./media/vscode-tools-for-ai/register-model.gif)](./media/vscode-tools-for-ai/register-model.gif#lightbox)


#### <a name="deploy-your-service-from-visual-studio-code"></a>Distribuire il servizio da Visual Studio Code

In Visual Studio Code, è possibile distribuire il servizio Web in:
+ Istanze di contenitore di Azure (ACI) per il test.
+ Azure Kubernetes Service (AKS) per la produzione.

Non è necessario creare un contenitore ACI per eseguire il test in anticipo, perché i contenitori ACI vengono creati in base alle esigenze. Tuttavia, è necessario configurare in anticipo i cluster AKS. Per altre informazioni, vedere [distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

Per distribuire un servizio Web:

1. Nella barra attività Visual Studio Code selezionare l'icona Azure. Verrà visualizzata la barra laterale di Azure Machine Learning.

1. Nella visualizzazione albero espandere la sottoscrizione di Azure e l'area di lavoro Azure Machine Learning.

1. Sotto il nodo dell'area di lavoro espandere il nodo **Models** (Modelli).

1. Fare clic con il pulsante destro del mouse sul modello che si desidera distribuire, quindi scegliere **Distribuisci servizio dal modello registrato** dal menu di scelta rapida.

1. Nel riquadro comandi scegliere la destinazione di calcolo in cui si vuole eseguire la distribuzione.

1. Nel riquadro comandi, nel campo, immettere un nome per il servizio.

1. Nel riquadro comandi selezionare il tasto INVIO sulla tastiera per cercare e selezionare il file di script.

1. Nel riquadro comandi selezionare il tasto INVIO sulla tastiera per cercare e selezionare il file di dipendenza conda.

1. Al termine della configurazione delle proprietà del servizio, nell'angolo inferiore destro della finestra selezionare **Invia** per distribuire. Nel file delle proprietà del servizio è possibile specificare un file Docker locale o un file schema. JSON.

Il servizio Web è ora distribuito.

Di seguito è riportato un esempio di come distribuire un servizio Web:

[![distribuire un servizio Web](./media/vscode-tools-for-ai/create-image.gif)](./media/vscode-tools-for-ai/create-image.gif#lightbox)

### <a name="experiment-with-additional-features"></a>Sperimentare funzionalità aggiuntive

È possibile utilizzare il riquadro comandi per accedere a numerose funzionalità di Azure Machine Learning Visual Studio Code. Per richiamare il riquadro comandi, digitare CTRL + MAIUSC + P. Da qui è possibile cercare altre funzionalità di Azure ML dell'estensione.

[tasti di scelta rapida ![per Azure Machine Learning per Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Per una procedura dettagliata su come eseguire il training con Azure Machine Learning all'esterno di Visual Studio Code, vedere [esercitazione: eseguire il training dei modelli con Azure Machine Learning](tutorial-train-models-with-aml.md).
* Per una procedura dettagliata su come modificare, eseguire ed eseguire il debug di codice localmente, vedere l'esercitazione relativa a [Python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).
