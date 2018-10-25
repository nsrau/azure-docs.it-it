---
title: Utilizzo dell'estensione Visual Studio Code Tools for AI con Azure Machine Learning
description: Informazioni su Visual Studio Code Tools for AI e su come avviare il training e distribuire modelli di Machine Learning e di apprendimento avanzato con il servizio Azure Machine Learning in VS Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: a2f37cffb37ce7008c3e372763784240e0d4250b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945553"
---
# <a name="vs-code-tools-for-ai-get-started-with-azure-machine-learning-from-visual-studio-code"></a>Visual Studio Code Tools for AI: introduzione a Azure Machine Learning da Visual Studio Code

L'articolo contiene informazioni sull'estensione Visual Studio Code (VS Code), su **Tools for AI** e su come avviare il training e distribuire modelli di Machine Learning e di apprendimento avanzato con il servizio Azure Machine Learning in VS Code.

Usare l'estensione Tools for AI in Visual Studio code per usufruire del servizio Azure Machine Learning per preparare i dati, eseguire il training e testare i modelli di Machine Learning in destinazioni di calcolo locale e remote, distribuire tali modelli e tenere traccia di esperimenti e metriche personalizzate.

## <a name="prerequisite"></a>Prerequisito

+ È necessario installare Visual Studio Code. Visual Studio Code è un editor di codice sorgente leggero, ma potente che viene eseguito sul desktop. Include supporto integrato per Python e altro ancora.  [Informazioni su come installare Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Installare Python 3.5 o versione successiva](https://www.anaconda.com/download/).

+ Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="install-vs-code-tools-for-ai-extension"></a>Installare l'estensione Visual Studio Code Tools for AI

Quando si installa l'estensione **Tools for AI**, due ulteriori estensioni vengono installate automaticamente (se si ha accesso a Internet). Sono l'estensione [Account di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) e l'estensione [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

Per usare Azure Machine Learning, è necessario trasformare Visual Studio Code in un ambiente di sviluppo integrato Python. Lavorare con [Python in Visual Studio Code](https://code.visualstudio.com/docs/languages/python) richiede l'estensione Microsoft Python, che viene installata automaticamente con Tools for AI. L'estensione rende il Visual Studio Code un ambiente di sviluppo integrato eccellente e funziona su qualsiasi sistema operativo con una serie di interpreti Python. Sfrutta tutte potenzialità di Visual Studio Code per fornire completamento automatico e IntelliSense, linting, debug e testing unità, oltre alla possibilità di passare facilmente da un ambiente Python all'altro, compresi gli ambienti virtuali e conda. Consultare questa procedura dettagliata per la modifica, l'esecuzione e il debug del codice Python, vedere l'[esercitazione Hello World in Python](https://code.visualstudio.com/docs/python/python-tutorial)

**Per installare l'estensione Tools for AI:**

1. Avviare VS Code.

1. In un browser, visitare: http://aka.ms/vscodetoolsforai. 

1. Nella pagina web, fare clic su **Installa**. 

1. Nella scheda estensioni, fare clic su **Installa**.

1. Verrà visualizzata una scheda di benvenuto in Visual Studio Code per l'estensione e il simbolo di Azure viene aggiunto alla barra delle attività.

   ![Icona Azure nella barra delle attività di Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Nella finestra di dialogo, fare clic su **Accedi** e seguire le istruzioni visualizzate sullo schermo per eseguire l'autenticazione con Azure. 
   
   L'estensione Account di Azure, che è stata installata con il Visual Studio Code Tools for AI, consente di eseguire l'autenticazione con il proprio account di Azure. Vedere l'elenco dei comandi nella pagina [estensione Account di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Consultare l'[estensione IntelliCode per Visual Studio Code (anteprima)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode fornisce un set di funzionalità supportate da AI per IntelliSense in Python, ad esempio dedurre i completamenti automatici più pertinenti in base al contesto di codice corrente.

## <a name="install-the-sdk"></a>Installare l'SDK

1. Assicurarsi che Python 3.5 o versione successiva sia installato e riconosciuto da Visual Studio Code. Se si installa ora, riavviare Visual Studio Code e selezionare un interprete Python seguendo le istruzioni in https://code.visualstudio.com/docs/python/python-tutorial.

1. In Visual Studio Code, aprire il riquadro comandi **CTRL+MAIUSC+P**.

1. Digitare "installare Azure Machine Learning SDK" per trovare il comando di installazione pip per l'SDK. Viene creato un ambiente Python privato locale che presenta i prerequisiti di Visual Studio Code per l'uso di Azure Machine Learning.
   ![install](./media/vscode-tools-for-ai/install-sdk.png)

1. Nella finestra del terminale integrato, specificare l'interprete Python da usare oppure premere **Invio** per usare l'interprete Python predefinito.

   ![Installazione](./media/vscode-tools-for-ai/python.png)

## <a name="get-started-with-azure-machine-learning"></a>Introduzione ad Azure Machine Learning

Prima di eseguire il training e distribuire i modelli ML usando Visual Studio Code, è necessario creare un'[area di lavoro del servizio Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) nel cloud per contenere i modelli e le risorse. Informazioni su come crearne uno e creare il primo esperimento nell'area di lavoro.

1. Fare clic sull'icona Azure nella barra delle attività di Visual Studio Code. Verrà visualizzata la barra laterale Azure: Machine Learning.

   ![Installazione](./media/vscode-tools-for-ai/createworkspace.gif)

1. Fare clic con il pulsante destro sulla sottoscrizione di Azure e selezionare **Creare area di lavoro**. Viene visualizzato un elenco. Nell'immagine animata il nome della sottoscrizione è "OpenMind Studio" e l'area di lavoro è "MyWorkspace". 

1. Selezionare un gruppo di risorse esistenti dall'elenco o crearne uno nuovo usando la procedura guidata nel riquadro comandi.

1. Nel campo, digitare un nome univoco e non crittografato per la nuova area di lavoro. Negli screenshot, l'area di lavoro è denominato 'AreaDiLavoro'.

1. Premere Invio per creare la nuova area di lavoro. Viene visualizzata nell'albero sotto il nome della sottoscrizione.

1. Fare clic con il pulsante destro sul nome dell'area di lavoro e scegliere **Crea esperimento** dal menu a comparsa.  Gli esperimenti tengono traccia delle esecuzioni usando Azure Machine Learning.

1. Nel campo, immettere un nome per l'esperimento. Negli screenshot, l'esperimento è denominato "MNIST".
 
1. Premere Invio per creare il nuovo esperimento. Viene visualizzato nell'albero sotto il nome dell'area di lavoro.

1. Fare clic con il pulsante destro sul nome dell'esperimento e scegliere **Allegare una cartella locale**. Questa cartella deve contenere gli script di Python locali. La cartella viene quindi collegata all'esperimento nel cloud. 

   Ora ogni esperimento viene eseguito con l'esperimento in modo che tutte le metriche chiave vengano archiviate nella cronologia degli esperimenti e i modelli di cui si esegue il training vengano automaticamente caricati in Azure Machine Learning e archiviati con metriche e log dell'esperimento.

   [![Allegare una cartella in Visual Studio Code](./media/vscode-tools-for-ai/attachfolder.gif)](./media/vscode-tools-for-ai/attachfolder.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Usare i tasti di scelta rapida

Analogamente alla maggior parte di Visual Studio Code, le funzionalità di Azure Machine Learning in Visual Studio Code sono accessibili dalla tastiera. La combinazione di tasti più importante da sapere è Ctrl + MAIUSC + P, che consente di visualizzare il riquadro comandi. A questo punto, è possibile accedere a tutte le funzionalità di Visual Studio Code, inclusi i tasti di scelta rapida per le operazioni più comuni.

[![Tasti di scelta rapida per Visual Studio Code Tools for AI](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile usare Visual Studio Code per lavorare con Azure Machine Learning.

Informazioni su come [creare destinazioni di calcolo, eseguire il training e distribuire modelli in Visual Studio Code](how-to-vscode-train-deploy.md).