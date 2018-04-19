---
title: Usare Visual Studio Code per eseguire il debug di Funzioni di Azure con Azure IoT Edge | Microsoft Docs
description: Eseguire il debug di Funzioni di Azure in C# con Azure IoT Edge in Visual Studio Code
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 3/20/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8c266a01375bf74fd4df9290255e84bc28e6089c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Usare Visual Studio Code per eseguire il debug di Funzioni di Azure con Azure IoT Edge

Questo articolo fornisce istruzioni dettagliate per usare [Visual Studio Code](https://code.visualstudio.com/) come strumento di sviluppo principale per eseguire il debug di Funzioni di Azure in IoT Edge.

## <a name="prerequisites"></a>prerequisiti
Questo articolo presuppone che si usi un computer o una macchina virtuale Windows o Linux come computer di sviluppo. Il dispositivo IoT Edge può essere un altro dispositivo fisico oppure è possibile simulare il dispositivo IoT Edge nel computer di sviluppo.

Prima di seguire le istruzioni riportate in questo articolo, completare i passaggi descritti in [Sviluppare una soluzione IoT Edge con più moduli in Visual Studio Code](tutorial-multiple-modules-in-vscode.md). Successivamente, è necessario disporre degli elementi seguenti:
- Un registro Docker locale in esecuzione nel computer di sviluppo. È consigliabile usare un registro Docker locale come prototipo e a scopo di test. È possibile aggiornare il registro contenitori nel file `module.json` in ogni cartella di modulo.
- Un'area di lavoro di un progetto di soluzione IoT Edge in cui è inclusa la sottocartella di un modulo Funzione di Azure.
- File `run.csx` con il codice della funzione.
- Un runtime Edge in esecuzione nel computer di sviluppo.

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Compilare il modulo della funzione IoT Edge a scopo di debug
1. Per iniziare il debug, è necessario usare **Dockerfile.amd64.debug** per ricompilare l'immagine Docker e distribuire nuovamente la soluzione Edge. In Esplora codice di Visual Studio passare al file `deployment.template.json`. Aggiornare l'URL dell'immagine della funzione aggiungendo `.debug` alla fine.

    ![Compilare l'immagine di debug](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Ricompilare la soluzione. Nel riquadro comandi di Visual Studio Code digitare ed eseguire il comando **Edge: Build IoT Edge solution** (Edge: Compila soluzione IoT Edge).

3. Fare clic con il pulsante destro del mouse sull'ID di un dispositivo IoT Edge nell'apposito strumento di esplorazione per l'hub IoT di Azure, quindi scegliere **Create deployment for Edge device** (Crea distribuzione per dispositivo Edge). Selezionare `deployment.json` nella cartella `config`. Si noterà quindi che la distribuzione è stata creata correttamente con un ID distribuzione nel terminale integrato di Visual Studio Code.

> [!NOTE]
> È possibile controllare lo stato del contenitore nello strumento di esplorazione di Visual Studio Code o tramite il comando `docker images` nel terminale.

## <a name="start-debugging-c-function-in-vs-code"></a>Avviare il debug della funzione C# in Visual Studio Code
1. Visual Studio Code consente di mantenere le informazioni di configurazione del debug in un file `launch.json` che si trova nella cartella `.vscode` nell'area di lavoro. Il file `launch.json` viene generato durante la creazione di una nuova soluzione IoT Edge e viene aggiornato ogni volta che si aggiunge un nuovo modulo con il supporto per il debug. Passare alla visualizzazione di debug e selezionare il file di configurazione del debug corrispondente.
    ![Selezionare la configurazione del debug](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Accedere a `run.csx`. Aggiungere un punto di interruzione nella funzione.

3. Fare clic su pulsante Avvia debug oppure premere **F5** e selezionare il processo a cui collegarsi.

4. Nel pannello di sinistra della visualizzazione di debug di Visual Studio Code è possibile vedere le variabili. 


> [!NOTE]
> L'esempio precedente illustra come eseguire il debug della funzione IoT Edge in .NET Core sui contenitori. Si basa sulla versione di debug di `Dockerfile.amd64.debug`, che include VSDBG (debugger della riga di comando di .NET Core) nell'immagine del contenitore durante la creazione. Si consiglia di usare direttamente o personalizzare il file `Dockerfile` senza VSDBG per la funzione IoT Edge per l'ambiente di produzione al termine del debug della funzione C#.

## <a name="next-steps"></a>Passaggi successivi


[Usare Visual Studio Code per eseguire il debug di un modulo C# con Azure IoT Edge](how-to-vscode-debug-csharp-module.md)

