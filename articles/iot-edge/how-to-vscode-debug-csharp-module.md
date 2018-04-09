---
title: Usare Visual Studio Code per eseguire il debug di un modulo C# con Azure IoT Edge | Microsoft Docs
description: Eseguire il debug di un modulo C# con Azure IoT Edge in Visual Studio Code.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c2a1acd2c249bdbc92119bc92f055b095f318f00
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Usare Visual Studio Code per eseguire il debug di un modulo C# con Azure IoT Edge
Questo articolo fornisce istruzioni dettagliate per l'uso di [Visual Studio Code](https://code.visualstudio.com/) come strumento di sviluppo principale per eseguire il debug dei moduli Azure IoT Edge.

## <a name="prerequisites"></a>Prerequisiti
Questa esercitazione presuppone che si usi un computer o una macchina virtuale Windows o Linux come computer di sviluppo. Il dispositivo IoT Edge può essere un altro dispositivo fisico oppure è possibile simulare il dispositivo IoT Edge nel computer di sviluppo.

Completare l'esercitazione seguente prima di procedere con queste istruzioni:
- [Sviluppare una soluzione IoT Edge con più moduli in Visual Studio Code](tutorial-multiple-modules-in-vscode.md)

Dopo aver completato l'esercitazione precedente, è necessario avere a disposizione quanto segue:
- Un registro Docker locale in esecuzione nel computer di sviluppo. È consigliabile usare un registro Docker locale come prototipo e a scopo di test. È possibile aggiornare il registro contenitori nel file `module.json` in ogni cartella di modulo.
- Un'area di lavoro di un progetto di soluzione IoT Edge in cui è inclusa la sottocartella di un modulo C#.
- Il file `Program.cs` con il codice del modulo più recente.
- Un runtime Edge in esecuzione nel computer di sviluppo.

## <a name="build-your-iot-edge-c-module-for-debugging"></a>Compilare il modulo C# di IoT Edge per il debug
1. Per iniziare il debug, è necessario usare **Dockerfile.amd64.debug** per ricompilare l'immagine Docker e distribuire nuovamente la soluzione Edge. Nello strumento di esplorazione di Visual Studio Code passare al file `deployment.template.json`. Aggiornare l'URL dell'immagine della funzione aggiungendo `.debug` alla fine.

2. Ricompilare la soluzione. Nel riquadro comandi di Visual Studio Code digitare ed eseguire il comando **Edge: Build IoT Edge solution** (Edge: Compila soluzione IoT Edge).

3. Fare clic con il pulsante destro del mouse sull'ID di un dispositivo IoT Edge nell'apposito strumento di esplorazione per l'hub IoT di Azure, quindi scegliere **Create deployment for Edge device** (Crea distribuzione per dispositivo Edge). Selezionare `deployment.json` nella cartella `config`. Si noterà quindi che la distribuzione è stata creata correttamente con un ID distribuzione nel terminale integrato di Visual Studio Code.

> [!NOTE]
> È possibile controllare lo stato del contenitore nello strumento di esplorazione di Visual Studio Code o tramite il comando `docker images` nel terminale.

## <a name="start-debugging-c-module-in-vs-code"></a>Avviare il debug del modulo C# in Visual Studio Code
1. Visual Studio Code consente di mantenere le informazioni di configurazione del debug in un file `launch.json` che si trova una cartella `.vscode` nell'area di lavoro. Il file `launch.json` viene generato durante la creazione di una nuova soluzione IoT Edge e viene aggiornato ogni volta che si aggiunge un nuovo modulo con il supporto per il debug. Passare alla visualizzazione di debug e selezionare il file di configurazione del debug corrispondente.
    ![Selezionare la configurazione del debug](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Accedere a `program.cs`. Aggiungere un punto di interruzione in questo file.

3. Fare clic su pulsante Avvia debug oppure premere **F5** e selezionare il processo a cui collegarsi.

4. Nel pannello di sinistra della visualizzazione di debug di Visual Studio Code è possibile vedere le variabili. 

> [!NOTE]
> L'esempio precedente illustra come eseguire il debug di moduli IoT Edge in .NET Core sui contenitori. Si basa sulla versione di debug di `Dockerfile.debug`, che include VSDBG (debugger della riga di comando di .NET Core) nell'immagine del contenitore durante la creazione. Al termine del debug dei moduli C#, si consiglia di usare direttamente o personalizzare il file `Dockerfile` senza VSDBG per moduli IoT Edge per l'ambiente di produzione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo IoT Edge, successivamente distribuito per il debug. Ne è stato quindi avviato il debug in Visual Studio Code. Per informazioni su altri scenari durante lo sviluppo di Azure IoT Edge in Visual Studio Code, vedere: 

> [!div class="nextstepaction"]
> [Sviluppare una soluzione IoT Edge con più moduli in Visual Studio Code](tutorial-multiple-modules-in-vscode.md)

