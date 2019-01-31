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
ms.openlocfilehash: d6f49eb0359db6184b5ecd146d7328a64611a9f9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245567"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Introduzione ad Azure Machine Learning per Visual Studio Code

In questo articolo si apprenderà come installare l'estensione **Azure Machine Learning per Visual Studio Code** e creare il primo esperimento con il servizio Azure Machine Learning in Visual Studio Code (VS Code).

Usare l'estensione Azure Machine Learning in Visual Studio code per usufruire del servizio Azure Machine Learning per preparare i dati, eseguire il training e testare i modelli di apprendimento automatico in destinazioni di calcolo locali e remote, distribuire tali modelli e tenere traccia di esperimenti e metriche personalizzate.

## <a name="prerequisite"></a>Prerequisito


+ Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Accedere alla [versione gratuita o a pagamento del servizio Azure Machine Learning](http://aka.ms/AMLFree).

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

1. Verrà visualizzata una scheda di benvenuto in Visual Studio Code per l'estensione e il simbolo di Azure viene aggiunto alla barra delle attività.

   ![Icona Azure nella barra delle attività di Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Nella finestra di dialogo, fare clic su **Accedi** e seguire le istruzioni visualizzate sullo schermo per eseguire l'autenticazione con Azure. 
   
   L'estensione Account Azure, che è stata installata con Azure Machine Learning per l'estensione di Visual Studio Code, consente di eseguire l'autenticazione con il proprio account di Azure. Vedere l'elenco dei comandi nella pagina [estensione Account di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Consultare l'[estensione IntelliCode per Visual Studio Code (anteprima)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode fornisce un set di funzionalità supportate da AI per IntelliSense in Python, ad esempio dedurre i completamenti automatici più pertinenti in base al contesto di codice corrente.

## <a name="install-the-sdk"></a>Installare l'SDK

1. Assicurarsi che Python 3.5 o versione successiva sia installato e riconosciuto da Visual Studio Code. Se si installa ora, riavviare Visual Studio Code e selezionare un interprete Python seguendo le istruzioni in https://code.visualstudio.com/docs/python/python-tutorial.

1. In Visual Studio Code, aprire il riquadro comandi **CTRL+MAIUSC+P**.

1. Digitare "installare Azure Machine Learning SDK" per trovare il comando di installazione pip per l'SDK. Viene creato un ambiente Python privato locale che presenta i prerequisiti di Visual Studio Code per l'uso di Azure Machine Learning.

   ![Installare Azure Machine Learning SDK per Python](./media/vscode-tools-for-ai/install-sdk.png)

1. Nella finestra del terminale integrato, specificare l'interprete Python da usare oppure premere **INVIO** per usare l'interprete Python predefinito.

   ![Scegliere l'interprete](./media/vscode-tools-for-ai/python.png)

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

### <a name="use-keyboard-shortcuts"></a>Usare i tasti di scelta rapida

Analogamente alla maggior parte di Visual Studio Code, le funzionalità di Azure Machine Learning in Visual Studio Code sono accessibili dalla tastiera. La combinazione di tasti più importante da sapere è Ctrl + MAIUSC + P, che consente di visualizzare il riquadro comandi. A questo punto, è possibile accedere a tutte le funzionalità di Visual Studio Code, inclusi i tasti di scelta rapida per le operazioni più comuni.

[![Tasti di scelta rapida per Azure Machine Learning per Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile usare Visual Studio Code per lavorare con Azure Machine Learning.

Informazioni su come [creare destinazioni di calcolo, eseguire il training e distribuire modelli in Visual Studio Code](how-to-vscode-train-deploy.md).
