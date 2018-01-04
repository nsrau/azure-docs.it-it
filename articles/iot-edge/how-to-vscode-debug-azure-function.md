---
title: Utilizzare Visual Studio Code per il debug di funzioni di Azure con Azure IoT Edge | Documenti Microsoft
description: Eseguire il debug in c# le funzioni di Azure con Azure IoT Edge, in Visual Studio Code
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4344a450d218a7424cd055cf086c1e4865c9af10
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Utilizzare Visual Studio Code per il debug di funzioni di Azure con Azure IoT Edge

In questo articolo vengono fornite istruzioni dettagliate per l'utilizzo di [codice di Visual Studio](https://code.visualstudio.com/) come lo strumento di sviluppo principali per eseguire il debug delle funzioni di Azure IoT lato.

## <a name="prerequisites"></a>Prerequisiti
In questa esercitazione si presuppone che si utilizza un computer o macchina virtuale che esegue Windows o Linux come computer di sviluppo. Il dispositivo perimetrale IoT può essere un altro dispositivo fisico oppure è possibile simulare il dispositivo perimetrale IoT nel computer di sviluppo.

Assicurarsi di avere completato le esercitazioni seguenti prima di iniziare questa Guida.
- [Usare codice di Visual Studio per sviluppare e distribuire le funzioni di Azure al bordo IoT di Azure](how-to-vscode-develop-azure-function.md)

Dopo aver completati l'esercitazione precedente, è necessario disporre dei seguenti elementi,
- Un Docker Registro di sistema locale in esecuzione nel computer di sviluppo. È consigliabile per utilizzare un registro di sistema locale di Docker per prototipo e lo scopo di test.
- Il `run.csx` con codice di funzione di filtro più recente.
- Aggiornata `deployment.json` file per il modulo sensore e modulo di funzione di filtro.
- Un bordo runtime in esecuzione nel computer di sviluppo.

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>Compilare il modulo di IoT Edge per scopi di debug
1. Per avviare il debug, è necessario utilizzare il **dockerfile.debug** per ricreare l'immagine di docker e distribuire nuovamente la soluzione di bordo. In Esplora il codice di Visual Studio, fare clic sulla cartella di Docker per aprirlo. Quindi fare clic su di `linux-x64` cartella, fare doppio clic sul **Dockerfile.debug**, fare clic su **immagine Docker modulo di compilazione IoT Edge**.
3. Nel **selezionare la cartella** finestra, passare al **FilterFunction** sul progetto e scegliere **Seleziona cartella come EXE_DIR**.
4. Nella casella di testo popup nella parte superiore della finestra di Visual Studio Code immettere il nome dell'immagine. Ad esempio: `<your container registry address>/filterfunction:latest`. Se si distribuisce nel Registro di sistema locale, deve essere `localhost:5000/filterfunction:latest`.
5. Eseguire il push dell'immagine nel repository di Docker. Utilizzare il **Edge: immagine di Docker modulo Push IoT Edge** comandi e immettere l'URL dell'immagine nella casella di testo popup nella parte superiore della finestra del codice di Visual Studio. Utilizzare lo stesso URL di immagini utilizzato nel precedente passaggio.
6. È possibile riutilizzare il `deployment.json` ridistribuire. Nella tavolozza di comando, digitare e selezionare **bordo: bordo riavviare** per ottenere la funzione di filtro in esecuzione con la versione di debug.

## <a name="start-debugging-in-vs-code"></a>Avviare il debug in Visual Studio Code
1. Passare alla finestra di debug di Visual Studio Code. Premere **F5** e selezionare **IoT Edge(.Net Core)**
2. In `launch.json`, passare a **Debug IoT Edge funzione (.NET Core)** sezione e compilare il `<container_name>`in `pipeArgs`. Deve essere `filterfunction` in questa esercitazione.
3. Passare a run.csx. Aggiungere un punto di interruzione nella funzione.
4. Premere **F5** nuovamente. Selezionare il processo a cui connettersi.
5. Nella finestra di Debug di codice di Visual Studio, è possibile visualizzare le variabili nel riquadro sinistro. 

> [!NOTE]
> Esempio precedente viene illustrato come il debug di .net Core IoT Edge funzione sui contenitori. Cui si basa la versione di debug di `Dockerfile.debug`, VSDBG (debugger della riga di comando di .NET Core) che include l'immagine del contenitore durante la creazione. Si consiglia utilizzare direttamente o personalizzare il `Dockerfile` senza VSDBG per la funzione IoT bordo di ambiente di produzione al termine del debug di una funzione c#.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è creata una funzione di Azure distribuita al bordo IoT a scopo di debug e avviato il debug in Visual Studio Code. È possibile continuare a una delle esercitazioni seguenti per ulteriori informazioni sugli altri scenari durante lo sviluppo di IoT Edge di Azure in Visual Studio Code. 

> [!div class="nextstepaction"]
> [Sviluppare e distribuire il modulo c# in Visual Studio Code](how-to-vscode-develop-csharp-module.md)

