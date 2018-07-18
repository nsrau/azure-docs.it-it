---
title: Eseguire il debug di più moduli per Azure IoT Edge in VS Code | Microsoft Docs
description: Usare Visual Studio Code per eseguire il debug di più moduli con Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 38c66129ac8244d18b0f94c1485c785015e29ab4
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049592"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Usare Visual Studio Code per eseguire il debug di più moduli con Azure IoT Edge
Questo articolo fornisce istruzioni dettagliate per usare [Visual Studio Code (VS)](https://code.visualstudio.com/) per eseguire il debug di più moduli in IoT Edge.

## <a name="prerequisites"></a>prerequisiti
Completare l'esercitazione [Sviluppare una soluzione IoT Edge con più moduli in Visual Studio Code](tutorial-multiple-modules-in-vscode.md) e assicurarsi di disporre di almeno due moduli in esecuzione nel dispositivo IoT Edge.

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>Debug di più destinazioni e debug remoto in VS Code
Con VS Code e l'estensione Azure IoT Edge, è possibile collegare il processo del modulo in un contenitore, sia che il contenitore sia in esecuzione nel computer di sviluppo o in un dispositivo fisico IoT Edge remoto. Per il debug di più moduli in esecuzione in contenitori in realtà viene in effetti collegato più di un processo in contenitori separati. La funzionalità di [debug di più destinazioni](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging) di VS Code può essere usata durante il debug di più moduli.

   > [!TIP]
   > Se il contenitore dei moduli viene eseguito in un dispositivo fisico IoT Edge remoto, potrebbe essere necessario configurare [Docker Machine](https://docs.docker.com/machine/overview/) in modo che il motore Docker nel computer di sviluppo possa comunicare con gli host Docker remoti.

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>Compilare i moduli IoT Edge a scopo di debug
1. Per iniziare il debug di più moduli, è necessario usare **Dockerfile.amd64.debug** per ricompilare l'immagine Docker e distribuire nuovamente la soluzione Edge. In Esplora codice di Visual Studio passare al file `deployment.template.json`. Aggiornare gli URL delle immagini aggiungendo `.debug` alla fine. Sono necessarie almeno due immagini dei moduli con `.debug`. Se si sta lavorando alla soluzione dall'esercitazione precedente, sono necessari un modulo di funzioni C# e un modulo C#. Aggiornare gli URL di queste due immagini aggiungendo `.debug` alla fine e salvare questo file. 
2. Ricompilare la soluzione. Nel riquadro comandi di VS Code digitare ed eseguire il comando **Azure IoT Edge: Build IoT Edge solution** (Azure IoT Edge: Compila soluzione IoT Edge).
3. Fare clic con il pulsante destro del mouse sull'ID di un dispositivo IoT Edge nell'apposito strumento di esplorazione per l'hub IoT di Azure, quindi scegliere **Create deployment for Edge device** (Crea distribuzione per dispositivo Edge). Selezionare il file `deployment.json` nella cartella `config`. Si noterà quindi che la distribuzione è stata creata correttamente con un ID distribuzione nel terminale integrato di Visual Studio Code.

È possibile controllare lo stato del contenitore nello strumento di esplorazione di Visual Studio Code o tramite il comando `docker ps` nel terminale.

### <a name="start-debugging-c-function-in-vs-code"></a>Avviare il debug della funzione C# in Visual Studio Code
1. Visual Studio Code consente di mantenere le informazioni di configurazione del debug in un file `launch.json` che si trova nella cartella `.vscode` nell'area di lavoro. Il file `launch.json` è stato generato durante la creazione di una nuova soluzione IoT Edge e viene aggiornato ogni volta che si aggiunge un nuovo modulo con il supporto per il debug. Passare alla visualizzazione di debug e selezionare il file di configurazione del debug corrispondente per il debug remoto del modulo di funzioni C#.
2. Accedere a `run.csx`. Aggiungere un punto di interruzione nella funzione.
3. Fare clic su pulsante **Avvia debug** oppure premere **F5** e selezionare il processo a cui collegarsi.
4. Nel pannello di sinistra della visualizzazione di debug di Visual Studio Code è possibile vedere le variabili. 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>Avviare contemporaneamente il debug del modulo C# in VS Code
1. Nel riquadro comandi di VS Code, digitare ed eseguire il comando "Workspace: Duplicate Workspace in New Window" (Area di lavoro: Duplica l'area di lavoro in una nuova finestra). Viene aperta una nuova finestra di VS Code con la stessa area di lavoro.
2. Passare alla visualizzazione di debug e selezionare il file di configurazione del debug corrispondente per il debug remoto del modulo C#.
3. Accedere a `program.cs`. Aggiungere un punto di interruzione nel modulo C#.
4. Fare clic su pulsante **Avvia debug** oppure premere **F5** e selezionare il processo a cui collegarsi.
5. Nel pannello di sinistra della visualizzazione di debug di Visual Studio Code è possibile vedere le variabili. 

### <a name="see-variables-in-multiple-debugging-windows"></a>Visualizzare le variabili in più finestre di debug
1. Esistono ora almeno due sessioni di debug in esecuzione in due finestre di VS Code. Verrà raggiunto uno dei punti di interruzione.
2. Premere `F10` o fare clic sul pulsante Esegui istruzione/routine sulla  **barra degli strumenti Debug**.
3. Verrà raggiunto il punto di interruzione in un'altra finestra di VS Code. 
4. Continuare con i due passaggi precedenti per visualizzare le variabili da più moduli in più finestre di debug di VS Code.

> [!NOTE]
> L'esempio precedente illustra come eseguire il debug di più moduli con Azure IoT Edge. Si basa sulla versione di debug di `Dockerfile.amd64.debug`, che include VSDBG (debugger della riga di comando di .NET Core) nell'immagine del contenitore durante la creazione. Si consiglia di usare direttamente o personalizzare il file `Dockerfile` senza VSDBG per la funzione IoT Edge per l'ambiente di produzione al termine del debug della funzione C#.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver compilato il modulo, vedere le informazioni su come [distribuire i moduli di Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md)
