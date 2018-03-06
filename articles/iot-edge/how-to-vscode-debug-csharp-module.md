---
title: Usare Visual Studio Code per eseguire il debug di un modulo C# con Azure IoT Edge | Microsoft Docs
description: Eseguire il debug di un modulo C# con Azure IoT Edge in Visual Studio Code.
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5ed517cf8d70cd279a55b79ad448709116cf511b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Usare Visual Studio Code per eseguire il debug di un modulo C# con Azure IoT Edge
Questo articolo fornisce istruzioni dettagliate per l'uso di [Visual Studio Code](https://code.visualstudio.com/) come strumento di sviluppo principale per eseguire il debug dei moduli Azure IoT Edge.

## <a name="prerequisites"></a>prerequisiti
Questa esercitazione presuppone che si usi un computer o una macchina virtuale Windows o Linux come computer di sviluppo. Il dispositivo IoT Edge può essere un altro dispositivo fisico oppure è possibile simulare il dispositivo IoT Edge nel computer di sviluppo.

Completare l'esercitazione seguente prima di procedere con queste istruzioni:
- [Usare Visual Studio Code per sviluppare un modulo C# con Azure IoT Edge](how-to-vscode-develop-csharp-module.md)

Dopo aver completato l'esercitazione precedente, è necessario avere a disposizione quanto segue:
- Un registro Docker locale in esecuzione nel computer di sviluppo. Questo al solo scopo di creare prototipi e test.
- Il file `Program.cs` con il codice del modulo del filtro più recente.
- Un file `deployment.json` aggiornato per i moduli del sensore e del filtro.
- Un runtime di IoT Edge in esecuzione nel computer di sviluppo.

## <a name="build-your-iot-edge-module-for-debugging"></a>Compilare il modulo IoT Edge per il debug
1. Per iniziare il debug, è necessario usare **dockerfile.debug** per ricompilare l'immagine Docker e distribuire nuovamente la soluzione IoT Edge. In Esplora risorse di Visual Studio Code selezionare la cartella Docker per aprirla. Selezionare quindi la cartella **linux-x64**, fare clic con il pulsante destro del mouse su **Dockerfile.debug** e selezionare **Build IoT Edge module Docker image** (Compila l'immagine Docker per il modulo IoT Edge).

    ![Screenshot dello strumento di esplorazione di Visual Studio Code](./media/how-to-debug-csharp-module/build-debug-image.png)

3. Nella finestra **Selezione cartella** passare o immettere **./bin/Debug/netcoreapp2.0/publish**. Selezionare quindi **Select Folder as EXE_DIR** (Selezionare la cartella EXE_DIR).
4. Nella casella di testo popup nella parte superiore della finestra di Visual Studio Code immettere il nome dell'immagine. Ad esempio: `<your container registry address>/filtermodule:latest`. Se si esegue la distribuzione nel registro locale, il nome deve essere `localhost:5000/filtermodule:latest`.
5. Eseguire il push dell'immagine nel repository di Docker. Usare il comando **Edge: Push IoT Edge module Docker image** (Edge: Esegui il push dell'immagine Docker per il modulo IoT Edge) e immettere l'URL dell'immagine nella casella di testo popup nella parte superiore della finestra di Visual Studio Code. Usare lo stesso URL di immagine del passaggio precedente.
6. Per ripetere la distribuzione, è possibile riusare `deployment.json`. Nel riquadro comandi digitare e selezionare **Edge: Restart Edge** (Edge: Riavvia Edge) per eseguire il modulo di filtro con la versione di debug.

## <a name="start-debugging-in-vs-code"></a>Avviare il debug in Visual Studio Code
1. Passare alla finestra di debug di Visual Studio Code. Premere **F5** e selezionare **IoT Edge (.NET Core)**.

    ![Screenshot della finestra di debug di Visual Studio Code](./media/how-to-debug-csharp-module/f5-debug-option.png)

2. In `launch.json` passare alla sezione **Debug IoT Edge Custom Module (.NET Core)** (Debug del modulo personalizzato IoT Edge - .NET Core). In **pipeArgs** compilare il campo `<container_name>`. In questa esercitazione, specificare `filtermodule`.

    ![Screenshot del file launch.json di Visual Studio Code](./media/how-to-debug-csharp-module/add-container-name.png)

3. Passare a **Program.cs**. Aggiungere un punto di interruzione in `method static async Task<MessageResponse> FilterModule(Message message, object userContext)`.
4. Premere di nuovo **F5** e selezionare il processo a cui collegarsi. In questa esercitazione, il nome del processo deve essere `FilterModule.dll`.

    ![Screenshot della finestra di debug di Visual Studio Code](./media/how-to-debug-csharp-module/attach-process.png)

5. Nel pannello di sinistra della finestra di debug di Visual Studio Code è possibile visualizzare le variabili. 

> [!NOTE]
> L'esempio precedente illustra come eseguire il debug di moduli IoT Edge in .NET Core sui contenitori. Si basa sulla versione di debug di `Dockerfile.debug`, che include VSDBG (debugger della riga di comando di .NET Core) nell'immagine del contenitore durante la creazione. Al termine del debug dei moduli C#, si consiglia di usare direttamente o personalizzare il file `Dockerfile` senza VSDBG per moduli IoT Edge per l'ambiente di produzione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo IoT Edge, successivamente distribuito per il debug. Ne è stato quindi avviato il debug in Visual Studio Code. Per informazioni su altri scenari durante lo sviluppo di Azure IoT Edge in Visual Studio Code, vedere: 

> [!div class="nextstepaction"]
> [Sviluppare e distribuire un modulo C# in Visual Studio Code](how-to-vscode-develop-csharp-module.md)
