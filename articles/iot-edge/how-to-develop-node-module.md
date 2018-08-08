---
title: Eseguire il debug di moduli Node.js per Azure IoT Edge | Microsoft Docs
description: Usare Visual Studio Code per sviluppare ed eseguire il debug di moduli Node.js per Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: a5ab49beed79a8ea3a7ded0848c09acad27a5fb1
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390538"
---
# <a name="develop-and-debug-nodejs-modules-with-azure-iot-edge-for-visual-studio-code"></a>Sviluppare ed eseguire il debug di moduli Node.js con Azure IoT Edge per Visual Studio Code

È possibile inviare la logica di business per consentirne il funzionamento a livello perimetrale trasformandola in moduli per Azure IoT Edge. Questo articolo fornisce istruzioni dettagliate per l'uso di Visual Studio Code (VS Code) come strumento principale per lo sviluppo di moduli C#.

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che si usi un computer o una macchina virtuale Windows o Linux come computer di sviluppo. Il dispositivo IoT Edge può essere un altro dispositivo fisico oppure è possibile simulare il dispositivo IoT Edge nel computer di sviluppo.

> [!NOTE]
> Questa esercitazione sul debug descrive come collegare un processo in un contenitore di modulo ed eseguirne il debug con VS Code. È possibile eseguire il debug dei moduli Node.js in contenitori linux-amd64, windows e arm32. Se non si ha ancora familiarità con le funzionalità di debug di Visual Studio Code, vedere le [informazioni sul debug](https://code.visualstudio.com/Docs/editor/debugging). 

Poiché in questo articolo viene usato Visual Studio Code come strumento di sviluppo principale, installare VS Code e quindi aggiungere le estensioni necessarie:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Estensione Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Estensione Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Per creare un modulo, sono necessari Node.js che include npm per compilare la cartella del progetto, Docker per compilare l'immagine del modulo e un registro contenitori in cui memorizzare l'immagine del modulo:
* [Node.JS](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) o [hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   >[!TIP]
   >Per prototipi e test è possibile usare un registro Docker locale anziché un registro nel cloud. 

Per testare il modulo in un dispositivo, è necessario un hub IoT attivo con almeno un dispositivo IoT Edge. Se si vuole usare il computer come dispositivo IoT Edge, è possibile farlo seguendo le procedure indicate nelle esercitazioni per [Windows](quickstart.md) o [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Creare un nuovo modello di soluzione

La procedura seguente illustra come creare un modulo IoT Edge basato su .NET Core 2.0 tramite Visual Studio Code e l'estensione Azure IoT Edge. Per iniziare, si crea una soluzione e si genera quindi il primo modulo all'interno della soluzione. Ogni soluzione può contenere più moduli. 

1. In Visual Studio Code selezionare **Visualizza** > **Terminale integrato**.
2. Nel terminale integrato immettere il comando seguente per installare o aggiornare l'ultima versione del modello di modulo Azure IoT Edge per Node.js:

   ```cmd/sh
   npm install -g yo generator-azure-iot-edge-module
   ```
3. In Visual Studio Code selezionare **Visualizza** > **Riquadro comandi**. 
4. Nel riquadro comandi digitare ed eseguire il comando **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nuova soluzione IoT Edge).

   ![Eseguire il comando per creare una nuova soluzione IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Passare alla cartella in cui si vuole creare la nuova soluzione e fare clic su **Seleziona cartella**. 
6. Specificare un nome per la soluzione. 
7. Scegliere **Node.js Module** (Modulo Node.js) come modello per il primo modulo della soluzione.
8. Specificare un nome per il modulo. Scegliere un nome univoco all'interno del registro contenitori. 
9. Specificare il repository di immagini per il modulo. Poiché VS Code popola automaticamente il nome del modulo, è sufficiente sostituire **localhost:5000** con le informazioni del registro specifiche. Se per il test si usa un registro Docker locale, localhost è corretto. Se si usa Registro contenitori di Azure, specificare il server di accesso indicato nelle impostazioni del registro. Il server di accesso ha un nome simile a **\<nome registro\>.azurecr.io**.

VS Code usa le informazioni fornite per creare una soluzione IoT Edge, quindi la carica in una nuova finestra.

All'interno della soluzione sono presenti tre elementi: 
* Una cartella **.vscode** contenente le configurazioni di debug.
* Una cartella **modules** contenente le sottocartelle di ogni modulo. Al momento è presente un solo modulo, ma è possibile aggiungerne altri nel riquadro comandi usando il comando **Azure IoT Edge: Aggiungi modulo IoT Edge**. 
* Un file con estensione **env** in cui sono elencate le variabili di ambiente. Se come registro si usa Registro contenitori di Azure, sono presenti il nome utente e la password di Registro contenitori di Azure. 

   >[!NOTE]
   >Il file dell'ambiente viene creato solo se si specifica un repository di immagini per il modulo. Se sono state accettate le impostazioni predefinite di localhost per testare ed eseguire il debug in locale, non è necessario dichiarare le variabili di ambiente. 

* Un file **deployment.template.json** in cui sono elencati il nuovo modulo e un modulo **tempSensor** di esempio che simula i dati utilizzabili per il test. Per altre informazioni sul funzionamento dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).

## <a name="devlop-your-module"></a>Sviluppare un modulo

Il codice funzione di Azure predefinito fornito con la soluzione si trova in **moduli** > **\<nome del modulo\>** > **app.js**. Il modulo e il file deployment.template.json sono impostati in modo che sia possibile compilare la soluzione, inviarla al registro del contenitore e distribuirla in un dispositivo per avviare il test senza toccare alcun codice. Il modulo viene compilato solo per accettare l'input da un'origine (in questo caso, il modulo tempSensor che simula i dati) e collegarlo all'hub IoT. 

Quando si è pronti per personalizzare il modello Node.js con il proprio codice, usare gli [SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md) per compilare i moduli che rispondano alle esigenze chiave delle soluzioni IoT quali sicurezza, gestione dei dispositivi e affidabilità. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Compilare e distribuire il modulo per il debug

In ogni cartella di modulo sono presenti più file Docker per diversi tipi di contenitore. Per compilare il modulo per il test, è possibile usare uno di questi file che terminano con l'estensione **debug**. Attualmente, i moduli C# supportano solo il debug in contenitori linux-amd64.

1. In VS Code passare al file `deployment.template.json`. Aggiornare l'URL dell'immagine del modulo aggiungendo **.debug** alla fine.
2. Nel modulo Node.js sostituire createOptions in **deployment.template.json** con il contenuto seguente e salvare il file: 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

2. Nel riquadro comandi di VS Code digitare ed eseguire il comando **Azure IoT Edge: Build IoT Edge solution** (Azure IoT Edge: Compila soluzione IoT Edge).
3. Selezionare il file `deployment.template.json` per la soluzione dal riquadro comandi. 
4. Nella finestra di esplorazione dei dispositivi dell'hub Azure IoT fare clic con il pulsante destro del mouse su un ID dispositivo IoT Edge, quindi selezionare **Create deployment for IoT Edge device** (Crea distribuzione per dispositivo IoT Edge). 
5. Aprire la cartella **config** della soluzione, quindi selezionare il file `deployment.json`. Fare clic su **Select Edge deployment manifest** (Seleziona il manifesto della distribuzione di Edge). 

Si noterà quindi che la distribuzione è stata creata correttamente con un ID distribuzione nel terminale integrato di Visual Studio Code.

È possibile controllare lo stato del contenitore nello strumento di esplorazione di Visual Studio Code o tramite il comando `docker ps` nel terminale.

## <a name="start-debugging-nodejs-module-in-vs-code"></a>Avviare il debug del modulo Node.Js in VS Code

Visual Studio Code consente di mantenere le informazioni di configurazione del debug in un file `launch.json` che si trova nella cartella `.vscode` nell'area di lavoro. Il file `launch.json` è stato generato durante la creazione di una nuova soluzione IoT Edge e viene aggiornato ogni volta che si aggiunge un nuovo modulo con il supporto per il debug. 

1. Passare alla visualizzazione di debug di VS Code e selezionare il file di configurazione del debug per il modulo.

2. Accedere a `app.js`. Aggiungere un punto di interruzione in questo file.

3. Fare clic su pulsante **Avvia debug** oppure premere **F5** e selezionare il processo a cui collegarsi.

4. Nel pannello di sinistra della visualizzazione di debug di Visual Studio Code è possibile vedere le variabili. 

L'esempio precedente illustra come eseguire il debug di moduli IoT Edge Node.js nei contenitori. Sono state aggiunte le porte esposte nel contenitore del modulo createOptions. Al termine del debug dei moduli Node.js, si consiglia di rimuovere le porte esposte per predisporre i moduli IoT Edge per l'ambiente di produzione.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver compilato il modulo, vedere le informazioni su come [distribuire i moduli Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md).

Per sviluppare moduli per i dispositivi IoT Edge, [Comprendere e usare gli SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md).
