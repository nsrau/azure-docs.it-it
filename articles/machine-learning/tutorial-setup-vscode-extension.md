---
title: "Esercitazione: Configurare l'estensione Azure Machine Learning per Visual Studio Code"
titleSuffix: Azure Machine Learning
description: Informazioni su come configurare l'estensione Azure Machine Learning per Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 4000fcc80d507d3b1e871d7f3288fc3b77693c76
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157462"
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
- Installare [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), un editor di codice leggero multipiattaforma. 

## <a name="install-the-extension"></a>Installare l'estensione

1. Aprire Visual Studio Code.
1. Per aprire la visualizzazione Estensioni, fare clic sull'icona **Estensioni** nella **Barra attività**.
1. Nella visualizzazione Estensioni cercare "Azure Machine Learning".
1. Selezionare **Installa**.

> [!NOTE]
> In alternativa, è possibile installare l'estensione Azure Machine Learning tramite Visual Studio Marketplace [scaricando direttamente il programma di installazione](https://aka.ms/vscodetoolsforai). 

I passaggi successivi di questa esercitazione sono stati testati con la **versione 0.6.8** dell'estensione.

## <a name="sign-in-to-your-azure-account"></a>Accedere all'account Azure

Per effettuare il provisioning delle risorse ed eseguire i carichi di lavoro in Azure, è necessario eseguire l'accesso con le credenziali dell'account Azure. Per semplificare la gestione degli account, Azure Machine Learning installa automaticamente l'estensione Account Azure. Visitare il sito seguente per [altre informazioni sull'estensione Account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Per aprire il riquadro comandi selezionare **Visualizza > Riquadro comandi** nella barra dei menu. 
1. Immettere il comando "Azure: Sign In" nella casella di testo per avviare il processo di accesso.

## <a name="run-a-script-in-azure"></a>Eseguire uno script in Azure

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
        "workspace": "WS12191742",
        "resourceGroup": "WS12191742-rg2",
        "location": "South Central US",
        "experiment": "WS12191742-exp2",
        "compute": {
            "name": "WS12191742-com2",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS12191742-com2-rc1",
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

1. Selezionare **Submit experiment** (Invia esperimento) per eseguire l'esperimento in Azure. Questa operazione consente di inviare il file `train.py` e il file di configurazione all'area di lavoro di Azure Machine Learning. Il processo di training viene quindi avviato in una risorsa di calcolo in Azure.
1. Dopo alcuni minuti, nell'ambiente locale viene creata una directory denominata `output` che contiene un modello TensorFlow sottoposto a training.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Eseguire il training e distribuire un modello di classificazione delle immagini TensorFlow usando l'estensione Azure Machine Learning per Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md).
