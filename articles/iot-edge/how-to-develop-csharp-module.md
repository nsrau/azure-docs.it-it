---
title: Eseguire il debug di moduli C# per Azure IoT Edge | Microsoft Docs
description: Usare Visual Studio Code per sviluppare, compilare ed eseguire il debug di un modulo C# per Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c237b1eb9874357afa922f809109cf8f2181561e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048187"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Usare Visual Studio Code per sviluppare ed eseguire il debug di moduli C# per Azure IoT Edge

È possibile inviare la logica di business trasformandola in moduli per Azure IoT Edge per consentirne l'uso a livello perimetrale. Questo articolo fornisce istruzioni dettagliate per l'uso di Visual Studio Code (VS Code) come strumento di sviluppo principale per sviluppare moduli C#.

## <a name="prerequisites"></a>prerequisiti
Questo articolo presuppone che si usi un computer o una macchina virtuale Windows o Linux come computer di sviluppo. Il dispositivo IoT Edge può essere un altro dispositivo fisico oppure è possibile simulare il dispositivo IoT Edge nel computer di sviluppo.

> [!NOTE]
> Questa esercitazione sul debug descrive come collegare un processo in un contenitore di modulo ed eseguirne il debug con VS Code. È solo possibile eseguire il debug di funzioni C# solo nei contenitori linux-amd64. Se non si ha familiarità con le funzionalità di debug di Visual Studio Code, vedere [Debug](https://code.visualstudio.com/Docs/editor/debugging). 

Dato che in questo articolo viene usato Visual Studio Code come strumento di sviluppo principale, installare VS Code e quindi aggiungere le estensioni necessarie:
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

La procedura seguente illustra come creare un modulo di IoT Edge basato su .NET Core 2.0 tramite Visual Studio Code e l'estensione Azure IoT Edge. Si inizia creando una soluzione per poi generare il primo modulo all'interno della soluzione. Ogni soluzione può contenere più moduli. 

1. In Visual Studio Code selezionare **Visualizza** > **Terminale integrato**.
3. Selezionare **Visualizza** > **Riquadro comandi**. 
4. Nel riquadro comandi digitare ed eseguire il comando **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: Nuova soluzione IoT Edge).

   ![Eseguire il comando New IoT Edge Solution (Nuova soluzione IoT Edge)](./media/how-to-develop-csharp-module/new-solution.png)

5. Passare alla cartella in cui si vuole creare la nuova soluzione e fare clic su **Seleziona cartella**. 
6. Specificare un nome per la soluzione. 
7. Scegliere **Modulo C#** come modello per il primo modulo nella soluzione.
8. Specificare un nome per il modulo. Scegliere un nome univoco all'interno del registro contenitori. 
9. Specificare il repository di immagini per il modulo. VS Code popola automaticamente il nome del modulo, quindi è sufficiente sostituire **localhost:5000** con le informazioni sul registro specifiche. Se si usa un registro Docker locale per il testing, localhost è corretto. Se si usa Registro contenitori di Azure, usare il server di accesso dalle impostazioni del registro. Il server di accesso è simile a **\<nome registro\>.azurecr.io**.

VS Code usa le informazioni fornite per creare una soluzione IoT Edge, quindi la carica in una nuova finestra.

   ![Visualizzare la soluzione IoT Edge](./media/how-to-develop-csharp-module/view-solution.png)

All'interno della soluzione sono presenti tre elementi: 
* Una cartella **.vscode** contenente le configurazioni di debug.
* Una cartella **modules** contenente le sottocartelle per ogni modulo. Al momento è presente un solo modulo, ma è possibile aggiungerne altri nel riquadro comandi con il comando **Azure IoT Edge: Aggiungi modulo Edge IoT**. 
* Le variabili di ambiente sono elencate in un file con estensione **env**. Se si usa Registro contenitori di Azure come registro, sono disponibili nome utente e password di Registro contenitori di Azure. 
* In un file **deployment.template.json** viene elencato il nuovo modulo insieme a un modulo **tempSensor** di esempio che simula i dati utilizzabili per il testing. Per altre informazioni sul funzionamento dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).

## <a name="build-and-deploy-your-module-for-debugging"></a>Compilare e distribuire il modulo per il debug

In ogni cartella di modulo, esistono più file di Docker per diversi tipi di contenitori. È possibile usare uno di questi file che terminano con l'estensione **debug** per compilare un modulo per il testing. Attualmente, i moduli C# supportano solo il debug in contenitori linux-amd64.

1. In VS Code passare al file `deployment.template.json`. Aggiornare l'URL dell'immagine della funzione aggiungendo **.debug** alla fine.

   ![Aggiungere .debug al nome dell'immagine](./media/how-to-develop-csharp-module/image-debug.png)

2. Nel riquadro comandi di VS Code digitare ed eseguire il comando **Edge: Build IoT Edge solution** (Edge: Compila soluzione IoT Edge).
3. Selezionare il file `deployment.template.json` per la soluzione dal riquadro comandi. 
4. Fare clic con il pulsante destro del mouse sull'ID di un dispositivo IoT Edge nell'apposito strumento di esplorazione per l'hub IoT di Azure, quindi scegliere **Create deployment for IoT Edge device** (Crea distribuzione per dispositivo Edge). 
5. Aprire la cartella **config** della soluzione e quindi selezionare il file `deployment.json`. Fare clic su **Select Edge deployment manifest** (Seleziona il manifesto della distribuzione di Edge). 

Si noterà quindi che la distribuzione è stata creata correttamente con un ID distribuzione nel terminale integrato di Visual Studio Code.

È possibile controllare lo stato del contenitore nello strumento di esplorazione di Visual Studio Code o tramite il comando `docker ps` nel terminale.

## <a name="start-debugging-c-module-in-vs-code"></a>Avviare il debug del modulo C# in Visual Studio Code
Visual Studio Code consente di mantenere le informazioni di configurazione del debug in un file `launch.json` che si trova nella cartella `.vscode` nell'area di lavoro. Il file `launch.json` è stato generato durante la creazione di una nuova soluzione IoT Edge e viene aggiornato ogni volta che si aggiunge un nuovo modulo con il supporto per il debug. 

1. Passare alla visualizzazione di debug di VS Code e selezionare il file di configurazione del debug per il modulo. Il nome dell'opzione di debug deve essere simile a **Debug remoto NomeModulo (.NET Core)** ![Selezionare la configurazione di debug](./media/how-to-develop-csharp-module/debug-config.png)

2. Accedere a `program.cs`. Aggiungere un punto di interruzione in questo file.

3. Fare clic su pulsante **Avvia debug** oppure premere **F5** e selezionare il processo a cui collegarsi.

4. Nel pannello di sinistra della visualizzazione di debug di Visual Studio Code è possibile vedere le variabili. 

> [!NOTE]
> L'esempio precedente illustra come eseguire il debug di moduli IoT Edge in .NET Core sui contenitori. Si basa sulla versione di debug di `Dockerfile.debug`, che include VSDBG (debugger della riga di comando di .NET Core) nell'immagine del contenitore durante la creazione. Al termine del debug dei moduli C#, si consiglia di usare direttamente o personalizzare il file `Dockerfile` senza VSDBG per moduli IoT Edge per l'ambiente di produzione.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver compilato il modulo, vedere le informazioni su come [distribuire i moduli di Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md)

