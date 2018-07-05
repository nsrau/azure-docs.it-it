---
title: Modulo funzioni di debug per Azure IoT Edge | Microsoft Docs
description: Usare Visual Studio Code per eseguire il debug C# di Funzioni di Azure con Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ee5d368bde0f176442a01ba266791f8a9c2cbc3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052828"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Usare Visual Studio Code per sviluppare ed eseguire il debug di Funzioni di Azure per Azure IoT Edge

Questo articolo fornisce istruzioni dettagliate per usare [Visual Studio Code (VS)](https://code.visualstudio.com/) per eseguire il debug di Funzioni di Azure in IoT Edge.

## <a name="prerequisites"></a>prerequisiti
Questo articolo presuppone che si usi un computer o una macchina virtuale Windows o Linux come computer di sviluppo. Il dispositivo IoT Edge può essere un altro dispositivo fisico oppure è possibile simulare il dispositivo IoT Edge nel computer di sviluppo.

> [!NOTE]
> Questa esercitazione sul debug descrive come collegare un processo in un contenitore di modulo ed eseguirne il debug con VS Code. È possibile eseguire il debug di moduli C# solo in contenitori linux-amd64. Se non si ha familiarità con le funzionalità di debug di Visual Studio Code, vedere [Debug](https://code.visualstudio.com/Docs/editor/debugging). 

In questo articolo viene usato Visual Studio Code come strumento di sviluppo principale. Installare VS Code e quindi aggiungere le estensioni necessarie: 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Estensione Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Estensione Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Per creare un modulo, è necessario .NET per creare la cartella del progetto, Docker per creare l'immagine del modulo e un registro contenitori in cui memorizzare l'immagine del modulo:
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) installato nel computer di sviluppo. 
* [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) o [hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

   > [!TIP]
   > È possibile usare un registro Docker locale per i prototipi e i test, invece di un registro nel cloud. 

Per testare il modulo in un dispositivo, è necessario un hub IoT attivo con almeno un dispositivo IoT Edge. Se si vuole usare il computer come dispositivo IoT Edge, è possibile farlo seguendo le procedure nelle esercitazioni per [Windows](quickstart.md) o [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Creare un nuovo modello di soluzione

La procedura seguente mostra come creare una soluzione IoT Edge che contiene un modulo di funzione C#. Ogni soluzione può contenere più moduli.

1. In Visual Studio Code selezionare **Visualizza** > **Terminale integrato**.
3. Selezionare **Visualizza** > **Riquadro comandi**.
4. Nel riquadro comandi digitare ed eseguire il comando **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: Nuova soluzione IoT Edge). 

   ![Eseguire il comando New IoT Edge Solution (Nuova soluzione IoT Edge)](./media/how-to-develop-csharp-module/new-solution.png)

5. Passare alla cartella in cui si vuole creare la nuova soluzione e fare clic su **Seleziona cartella**. 
6. Specificare un nome per la soluzione. 
7. Scegliere **Funzioni di Azure - C#** come modello per il primo modulo nella soluzione.
8. Specificare un nome per il modulo. Scegliere un nome univoco all'interno del registro contenitori. 
9. Specificare il repository di immagini per il modulo. VS Code popola automaticamente il nome del modulo, quindi è sufficiente sostituire **localhost:5000** con le informazioni sul registro specifiche. Se si usa un registro Docker locale per il testing, localhost è corretto. Se si usa Registro contenitori di Azure, usare il server di accesso dalle impostazioni del registro. Il server di accesso è simile a **\<nome registro\>.azurecr.io**.

VS Code usa le informazioni fornite per creare una soluzione IoT Edge con un progetto per funzione, quindi la carica in una nuova finestra.

All'interno della soluzione sono presenti tre elementi: 

* Una cartella **.vscode** contenente le configurazioni di debug.
* Una cartella **modules** contenente le sottocartelle per ogni modulo. Al momento è presente un solo modulo, ma è possibile aggiungerne altri tramite il riquadro comandi con il comando **Azure IoT Edge: Aggiungi modulo Edge IoT**.
* Le variabili di ambiente sono elencate in un file con estensione **env**. Se si usa Registro contenitori di Azure come registro, sono disponibili nome utente e password di Registro contenitori di Azure. 
* In un file **deployment.template.json** viene elencato il nuovo modulo insieme a un modulo **tempSensor** di esempio che simula i dati utilizzabili per il testing. Per altre informazioni sul funzionamento dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Compilare il modulo della funzione IoT Edge a scopo di debug
1. Per iniziare il debug, è necessario usare **Dockerfile.amd64.debug** per ricompilare l'immagine Docker e distribuire nuovamente la soluzione Edge. In Esplora codice di Visual Studio passare al file `deployment.template.json`. Aggiornare l'URL dell'immagine della funzione aggiungendo `.debug` alla fine.

    ![Compilare l'immagine di debug](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Ricompilare la soluzione. Nel riquadro comandi di VS Code digitare ed eseguire il comando **Azure IoT Edge: Build IoT Edge solution** (Azure IoT Edge: Compila soluzione IoT Edge).
3. Fare clic con il pulsante destro del mouse sull'ID di un dispositivo IoT Edge nell'apposito strumento di esplorazione per l'hub IoT di Azure, quindi scegliere **Create deployment for Edge device** (Crea distribuzione per dispositivo Edge). Selezionare il file `deployment.json` nella cartella `config`. Si noterà quindi che la distribuzione è stata creata correttamente con un ID distribuzione nel terminale integrato di Visual Studio Code.

È possibile controllare lo stato del contenitore nello strumento di esplorazione di Visual Studio Code o tramite il comando `docker images` nel terminale.

## <a name="start-debugging-c-function-in-vs-code"></a>Avviare il debug della funzione C# in Visual Studio Code
1. Visual Studio Code consente di mantenere le informazioni di configurazione del debug in un file `launch.json` che si trova nella cartella `.vscode` nell'area di lavoro. Il file `launch.json` è stato generato durante la creazione di una nuova soluzione IoT Edge e viene aggiornato ogni volta che si aggiunge un nuovo modulo con il supporto per il debug. Passare alla visualizzazione di debug e selezionare il file di configurazione del debug corrispondente. Il nome dell'opzione di debug deve essere simile a **Debug remoto NomeModulo (.NET Core)** ![Selezionare la configurazione di debug](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Accedere a `run.csx`. Aggiungere un punto di interruzione nella funzione.
3. Fare clic su pulsante **Avvia debug** oppure premere **F5** e selezionare il processo a cui collegarsi.
4. Nel pannello di sinistra della visualizzazione di debug di Visual Studio Code è possibile vedere le variabili. 


> [!NOTE]
> L'esempio precedente illustra come eseguire il debug della funzione IoT Edge in .NET Core sui contenitori. Si basa sulla versione di debug di `Dockerfile.amd64.debug`, che include VSDBG (debugger della riga di comando di .NET Core) nell'immagine del contenitore durante la creazione. Si consiglia di usare direttamente o personalizzare il file `Dockerfile` senza VSDBG per la funzione IoT Edge per l'ambiente di produzione al termine del debug della funzione C#.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver compilato il modulo, vedere le informazioni su come [distribuire i moduli di Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md)
