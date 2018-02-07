---
title: Usare Visual Studio Code per eseguire il debug di Funzioni di Azure con Azure IoT Edge | Microsoft Docs
description: Eseguire il debug di Funzioni di Azure in C# con Azure IoT Edge in Visual Studio Code
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: db86a08a19e97f8f415849aa060fe87d77cccf68
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Usare Visual Studio Code per eseguire il debug di Funzioni di Azure con Azure IoT Edge

Questo articolo fornisce istruzioni dettagliate per usare [Visual Studio Code](https://code.visualstudio.com/) come strumento di sviluppo principale per eseguire il debug di Funzioni di Azure in IoT Edge.

## <a name="prerequisites"></a>Prerequisiti
Questa esercitazione presuppone che si usi un computer o una macchina virtuale Windows o Linux come computer di sviluppo. Il dispositivo IoT Edge può essere un altro dispositivo fisico oppure è possibile simulare il dispositivo IoT Edge nel computer di sviluppo.

Completare le esercitazioni seguenti prima di procedere con queste istruzioni.
- [Usare Visual Studio Code per sviluppare e distribuire Funzioni di Azure con Azure IoT Edge](how-to-vscode-develop-azure-function.md)

Dopo aver completato l'esercitazione precedente, è necessario avere a disposizione quanto segue:
- Un registro Docker locale in esecuzione nel computer di sviluppo. È consigliabile usare un registro Docker locale come prototipo e a scopo di test.
- Il file `run.csx` con il codice dalla funzione di filtro più recente.
- Un file `deployment.json` aggiornato per il modulo del sensore e il modulo della funzione di filtro.
- Un runtime Edge in esecuzione nel computer di sviluppo.

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>Compilare il modulo IoT Edge a scopo di debug
1. Per iniziare il debug, è necessario usare **dockerfile.debug** per ricompilare l'immagine Docker e distribuire nuovamente la soluzione Edge. Nello strumento di esplorazione di Visual Studio Code fare clic sulla cartella Docker per aprirla. Fare quindi clic sulla cartella `linux-x64`, fare clic con il pulsante destro del mouse su **Dockerfile.debug** e scegliere **Build IoT Edge module Docker image** (Compila l'immagine Docker per il modulo IoT Edge).

    ![Compilare l'immagine di debug](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Nella finestra **Seleziona cartella** passare al progetto **FilterFunction** e fare clic su **Select Folder as EXE_DIR** (Seleziona cartella come EXE_DIR).
3. Nella casella di testo popup nella parte superiore della finestra di Visual Studio Code immettere il nome dell'immagine. Ad esempio: `<your container registry address>/filterfunction:latest`. Se si esegue la distribuzione nel registro locale, il nome deve essere `localhost:5000/filterfunction:latest`.

    ![Immagine di push](./media/how-to-debug-csharp-function/push-image.png)

4. Eseguire il push dell'immagine nel repository di Docker. Usare il comando **Edge: Push IoT Edge module Docker image** (Edge: Esegui il push dell'immagine Docker per il modulo IoT Edge) e immettere l'URL dell'immagine nella casella di testo popup nella parte superiore della finestra di Visual Studio Code. Usare lo stesso URL di immagine del passaggio precedente.
5. Per ripetere la distribuzione, è possibile riusare `deployment.json`. Nel riquadro comandi digitare e selezionare **Edge: Restart Edge** (Edge: Riavvia Edge) per eseguire la funzione di filtro con la versione di debug.

## <a name="start-debugging-in-vs-code"></a>Avviare il debug in Visual Studio Code
1. Passare alla finestra di debug di Visual Studio Code. Premere **F5** e selezionare **IoT Edge (.NET Core)**

    ![Premere F5](./media/how-to-debug-csharp-function/f5-debug-option.png)

2. In `launch.json` passare alla sezione **Debug IoT Edge Function (.NET Core)** (Debug della funzione IoT Edge - .NET Core) e compilare il campo `<container_name>` in `pipeArgs`. In questa esercitazione, deve essere `filterfunction`.

    ![Aggiornare launch.json](./media/how-to-debug-csharp-function/update-launch-json.png)

3. Passare a run.csx. Aggiungere un punto di interruzione nella funzione.
4. Passare alla finestra di debug (Ctrl + Maiusc + D) e selezionare **Debug IoT Edge Function (.NET Core)** (Debug della funzione IoT Edge - .NET Core) nell'elenco a discesa. 

    ![Selezionare la modalità Debug](./media/how-to-debug-csharp-function/choose-debug-mode.png)

5. Fare clic su pulsante Avvia debug oppure premere **F5** e selezionare il processo a cui collegarsi.

    ![Collegamento al processo della funzione](./media/how-to-debug-csharp-function/attach-function-process.png)

6. Nel pannello di sinistra della finestra di debug di Visual Studio Code è possibile visualizzare le variabili. 

> [!NOTE]
> L'esempio precedente illustra come eseguire il debug della funzione IoT Edge in .NET Core sui contenitori. Si basa sulla versione di debug di `Dockerfile.debug`, che include VSDBG (debugger della riga di comando di .NET Core) nell'immagine del contenitore durante la creazione. Si consiglia di usare direttamente o personalizzare il file `Dockerfile` senza VSDBG per la funzione IoT Edge per l'ambiente di produzione al termine del debug della funzione C#.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una funzione di Azure, che è stata distribuita in IoT Edge a scopo di debug e ne è stato avviato il debug in Visual Studio Code. Per informazioni su altri scenari durante lo sviluppo di Azure IoT Edge in Visual Studio Code, è possibile continuare con l'esercitazione seguente. 

> [!div class="nextstepaction"]
> [Sviluppare e distribuire un modulo C# in Visual Studio Code](how-to-vscode-develop-csharp-module.md)

