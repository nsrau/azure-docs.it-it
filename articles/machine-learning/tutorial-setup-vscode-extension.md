---
title: "Esercitazione: Configurare l'estensione di Visual Studio Code"
titleSuffix: Azure Machine Learning
description: Informazioni su come configurare l'estensione Azure Machine Learning per Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 02/24/2020
ms.openlocfilehash: 583071ee22e4fb9cffc741520b1583790002a5bf
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604827"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Configurare l'estensione Azure Machine Learning per Visual Studio Code

Informazioni su come installare ed eseguire script usando l'estensione Azure Machine Learning per Visual Studio Code.

In questa esercitazione si apprenderanno informazioni sulle attività seguenti:

> [!div class="checklist"]
> * Installare l'estensione Azure Machine Learning per Visual Studio Code
> * Accedere all'account Azure da Visual Studio Code
> * Usare l'estensione Azure Machine Learning per eseguire uno script di esempio

## <a name="prerequisites"></a>Prerequisites

- Sottoscrizione di Azure. Se non se ne possiede una, iscriversi per provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Se non è disponibile, [installarlo](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>Installare l'estensione

1. Aprire Visual Studio Code.
1. Per aprire la visualizzazione Estensioni, fare clic sull'icona **Estensioni** nella **Barra attività**.
1. Nella visualizzazione Estensioni cercare "Azure Machine Learning".
1. Selezionare **Installa**.

    > [!div class="mx-imgBorder"]
    > ![Installare l'estensione Azure Machine Learning per VS Code](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> In alternativa, è possibile installare l'estensione Azure Machine Learning tramite Visual Studio Marketplace [scaricando direttamente il programma di installazione](https://aka.ms/vscodetoolsforai). 

I passaggi successivi di questa esercitazione sono stati testati con la **versione 0.6.8** dell'estensione.

## <a name="sign-in-to-your-azure-account"></a>Accedere all'account Azure

Per effettuare il provisioning delle risorse ed eseguire i carichi di lavoro in Azure, è necessario eseguire l'accesso con le credenziali dell'account Azure. Per semplificare la gestione degli account, Azure Machine Learning installa automaticamente l'estensione Account Azure. Visitare il sito seguente per [altre informazioni sull'estensione Account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Per aprire il riquadro comandi selezionare **Visualizza > Riquadro comandi** nella barra dei menu. 
1. Immettere il comando "Azure: Sign In" nel riquadro comandi per avviare il processo di accesso.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Eseguire uno script di training del modello di Machine Learning in Azure

Ora che è stato effettuato l'accesso ad Azure con le credenziali dell'account, seguire la procedura descritta in questa sezione per informazioni su come usare l'estensione per eseguire il training di un modello di Machine Learning.

1. Scaricare e decomprimere il repository [VS Code Tools for AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) nel computer.
1. Aprire la directory `mnist-vscode-docs-sample` in Visual Studio Code.
1. Selezionare l'icona di **Azure** nella barra attività.
1. Selezionare l'icona **Run Experiment** (Esegui esperimento) nella parte superiore della visualizzazione Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Esegui esperimento](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Quando si espande il riquadro comandi, seguire le istruzioni.

    1. Selezionare la sottoscrizione di Azure.
    1. Selezionare **Create a new Azure ML workspace** (Crea una nuova area di lavoro di Azure ML)
    1. Selezionare **TensorFlow Single-Node Training** (Training su nodo singolo TensorFlow) come tipo di processo.
    1. Immettere `train.py` come script per il training. Si tratta del file che contiene il codice di un modello di Machine Learning che categorizza le immagini di cifre scritte a mano.
    1. Specificare i pacchetti seguenti come requisiti per l'esecuzione.

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. A questo punto, nell'editor di testo viene visualizzato un file di configurazione simile a quello riportato di seguito. La configurazione contiene le informazioni necessarie per eseguire il processo di training, ad esempio il file che contiene il codice per eseguire il training del modello e le eventuali dipendenze di Python specificate nel passaggio precedente.

    ```json
    {
        "workspace": "WS01311608",
        "resourceGroup": "WS01311608-rg1",
        "location": "South Central US",
        "experiment": "WS01311608-exp1",
        "compute": {
            "name": "WS01311608-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS01311608-com1-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. Al termine della configurazione, per inviare l'esperimento aprire il riquadro comandi e immettere il comando seguente:

    ```text
    Azure ML: Submit Experiment
    ```

    Questa operazione consente di inviare il file `train.py` e il file di configurazione all'area di lavoro di Azure Machine Learning. Il processo di training viene quindi avviato in una risorsa di calcolo in Azure.

### <a name="track-the-progress-of-the-training-script"></a>Tenere traccia dello stato di avanzamento dello script di training

L'esecuzione dello script può richiedere qualche minuto. Per tenere traccia dello stato di avanzamento:

1. Selezionare l'icona di **Azure** nella barra attività.
1. Espandere il nodo della sottoscrizione.
1. Espandere il nodo dell'esperimento attualmente in esecuzione. Si trova all'interno del nodo `{workspace}/Experiments/{experiment}` in cui i valori per l'area di lavoro e l'esperimento sono gli stessi delle proprietà definite nel file di configurazione.
1. Vengono elencate tutte le esecuzioni dell'esperimento, nonché il relativo stato. Per ottenere lo stato più recente, fare clic sull'icona di aggiornamento nella parte superiore della visualizzazione Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Tenere traccia dello stato dell'esperimento](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Scaricare il modello con training

Al termine dell'esecuzione dell'esperimento, l'output è un modello con training. Per scaricare gli output in locale:

1. Fare clic con il pulsante destro del mouse sull'esecuzione più recente e scegliere **Download Outputs** (Scarica output).

    > [!div class="mx-imgBorder"]
    > ![Scaricare un modello con training](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Selezionare un percorso in cui salvare gli output.
1. Una cartella con il nome dell'esecuzione viene scaricata localmente. Passare ad essa.
1. I file del modello si trovano all'interno della directory `outputs/outputs/model`.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Eseguire il training e distribuire un modello di classificazione delle immagini TensorFlow usando l'estensione Azure Machine Learning per Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md).
