---
title: Eseguire il debug di moduli Node.js per Azure IoT Edge | Microsoft Docs
description: Usare Visual Studio Code per sviluppare ed eseguire il debug di moduli Node.js per Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: b05492941defc6ac3aa252d6bb29043d55e6b66c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261775"
---
# <a name="use-visual-studio-code-to-develop-and-debug-nodejs-modules-for-azure-iot-edge"></a>Usare Visual Studio Code per sviluppare ed eseguire il debug di moduli Node.js per Azure IoT Edge

È possibile inviare la logica di business per consentirne il funzionamento a livello perimetrale trasformandola in moduli per Azure IoT Edge. Questo articolo fornisce istruzioni dettagliate per l'uso di Visual Studio Code (VS Code) come strumento principale per lo sviluppo di moduli Node.js.

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che si usi un computer o una macchina virtuale Windows, macOS o Linux come computer di sviluppo. Il dispositivo IoT Edge può essere un altro dispositivo fisico.

> [!NOTE]
> Questo articolo sul debug illustra due modi generalmente adottati per eseguire il debug del modulo Node.js in Visual Studio Code. Un modo consiste nell'associare un processo in un contenitore di modulo, mentre l'altro consiste nell'avviare il codice del modulo in modalità di debug. Se non si ha ancora familiarità con le funzionalità di debug di Visual Studio Code, vedere le [informazioni sul debug](https://code.visualstudio.com/Docs/editor/debugging).

Poiché in questo articolo viene usato Visual Studio Code come strumento di sviluppo principale, installare VS Code e quindi aggiungere le estensioni necessarie:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Estensione Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Estensione Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Per creare un modulo, sono necessari Node.js che include npm per compilare la cartella del progetto, Docker per compilare l'immagine del modulo e un registro contenitori in cui memorizzare l'immagine del modulo:
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) o [hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Per prototipi e test è possibile usare un registro Docker locale anziché un registro nel cloud. 

Per impostare l'ambiente di sviluppo locale per effettuare il debug ed eseguire e testare la soluzione IoT Edge, è necessario usare [Azure IoT Edge Hub Dev Tool](https://pypi.org/project/iotedgehubdev/). Installare [Python (2.7/3.6) e Pip](https://www.python.org/). PIP è incluso con il programma di installazione di Python. Installare **iotedgehubdev** eseguendo questo comando nel terminale.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

Per testare il modulo in un dispositivo, è necessario un hub IoT attivo con almeno un ID dispositivo IoT Edge creato. Se si esegue il daemon IoT Edge nel computer di sviluppo, potrebbe essere necessario arrestare EdgeHub ed EdgeAgent prima di andare al passaggio successivo. 

## <a name="create-a-new-solution-template"></a>Creare un nuovo modello di soluzione

La procedura seguente illustra come creare un modulo IoT Edge basato su Node.js tramite Visual Studio Code e l'estensione Azure IoT Edge. Per iniziare, si crea una soluzione e si genera quindi il primo modulo all'interno della soluzione. Ogni soluzione può contenere più moduli. 

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
9. Specificare il repository di immagini per il modulo. Poiché VS Code popola automaticamente il nome del modulo, è sufficiente sostituire **localhost:5000** con le informazioni del registro specifiche. Se per il test si usa un registro Docker locale, localhost è corretto. Se si usa Registro contenitori di Azure, specificare il server di accesso indicato nelle impostazioni del registro. Il server di accesso ha un nome simile a **\<nome registro\>.azurecr.io**. Sostituire solo la parte localhost della stringa, non eliminare il nome del modulo.

   ![Specificare il repository di immagini Docker](./media/how-to-develop-node-module/repository.png)

VS Code usa le informazioni fornite per creare una soluzione IoT Edge, quindi la carica in una nuova finestra.

All'interno della soluzione sono presenti tre elementi: 
* Una cartella **.vscode** contenente le configurazioni di debug.
* Una cartella **modules** contenente le sottocartelle di ogni modulo. Al momento è presente un solo modulo, ma è possibile aggiungerne altri nel riquadro comandi usando il comando **Azure IoT Edge: Aggiungi modulo IoT Edge**. 
* Un file con estensione **env** in cui sono elencate le variabili di ambiente. Se Registro contenitori di Azure è il registro, si avranno un nome utente e una password per Registro contenitori di Azure.

   >[!NOTE]
   >Il file dell'ambiente viene creato solo se si specifica un repository di immagini per il modulo. Se sono state accettate le impostazioni predefinite di localhost per testare ed eseguire il debug in locale, non è necessario dichiarare le variabili di ambiente. 

* Un file **deployment.template.json** in cui sono elencati il nuovo modulo e un modulo **tempSensor** di esempio che simula i dati utilizzabili per il test. Per altre informazioni sul funzionamento dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).

## <a name="develop-your-module"></a>Sviluppare il modulo

Il codice Node.js predefinito fornito con la soluzione si trova in **modules** > [nome del modulo] > **app.js**. Il modulo e il file deployment.template.json sono impostati in modo che sia possibile compilare la soluzione, inviarla al registro del contenitore e distribuirla in un dispositivo per avviare il test senza toccare alcun codice. Il modulo viene compilato solo per accettare l'input da un'origine (in questo caso, il modulo tempSensor che simula i dati) e collegarlo all'hub IoT. 

Quando si è pronti per personalizzare il modello Node.js con il proprio codice, usare gli [SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md) per compilare i moduli che rispondano alle esigenze chiave delle soluzioni IoT quali sicurezza, gestione dei dispositivi e affidabilità. 

Visual Studio Code include il supporto per Node.js. Altre informazioni su [come usare Node.js in Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="launch-and-debug-module-code-without-container"></a>Avviare il codice del modulo ed eseguirne il debug senza contenitore

Il modulo Node.js per IoT Edge dipende da Azure IoT Node.js Device SDK. Nel codice del modulo predefinito si inizializza un **ModuleClient** con le impostazioni di ambiente e il nome di input. Questo significa che il modulo Node.js per IoT Edge richiede l'avvio e l'esecuzione delle impostazioni di ambiente ed è anche necessario inviare o indirizzare i messaggi ai canali di input. Il modulo Node.js predefinito contiene solo un canale di input, denominato **input1**.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>Impostare il simulatore di IoT Edge per l'app a modulo singolo

1. Per impostare e avviare il simulatore, nel riquadro comandi di Visual Studio Code digitare e selezionare **Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module** (Azure IoT Edge: Avvia simulatore hub di IoT Edge per modulo singolo). È necessario anche specificare i nomi di input per l'applicazione a modulo singolo, digitare **input1** e premere INVIO. Il comando attiverà l'interfaccia della riga di comando **iotedgehubdev** e avvierà il simulatore di IoT Edge e un contenitore di modulo utilità di test. Se il simulatore è stato avviato correttamente in modalità modulo singolo, è possibile vedere l'output seguente nel terminale integrato. È anche possibile vedere un comando `curl` che aiuta a inviare messaggi e che sarà necessario più avanti.

   ![Impostare il simulatore di IoT Edge per l'app a modulo singolo](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   È possibile passare a Docker Explorer e vedere lo stato di esecuzione del modulo.

   ![Stato del modulo del simulatore](media/how-to-develop-csharp-module/simulator-status.png)

   Il contenitore **edgeHubDev** costituisce il nucleo del simulatore di IoT Edge locale. Può essere eseguito nel computer di sviluppo senza il daemon di sicurezza di IoT Edge e fornire le impostazioni di ambiente per l'app a modulo nativo o i contenitori del modulo. Il contenitore **input** espone API REST per agevolare il bridging dei messaggi al canale di input di destinazione nel modulo.

2. Nel riquadro comandi di Visual Studio Code digitare e selezionare **Azure IoT Edge: Set Module Credentials to User Settings** (Azure IoT Edge: Impostare le credenziali del modulo sulle impostazioni utente) per specificare le impostazioni di ambiente del modulo in `azure-iot-edge.EdgeHubConnectionString` e `azure-iot-edge.EdgeModuleCACertificateFile` nelle impostazioni utente. È possibile trovare queste impostazioni di ambiente in **vscode** > **launch.json** e nelle [impostazioni utente di Visual Studio Code](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="debug-nodejs-module-in-launch-mode"></a>Eseguire il debug del modulo Node.js in modalità di avvio

1. Nel terminale integrato passare alla cartella **NodeModule** ed eseguire questo comando per installare pacchetti Node.

   ```cmd
   npm install
   ```

2. Accedere a `app.js`. Aggiungere un punto di interruzione in questo file.

3. Passare alla visualizzazione di debug di VS Code. Selezionare la configurazione di debug **Debug locale NomeModulo (Node.js)**. 

4. Fare clic su **Avvia debug** o premere **F5**. Verrà avviata la sessione di debug.

5. Nel terminale integrato di Visual Studio Code eseguire questo comando per inviare un messaggio **Hello World** al modulo. Questo è il comando mostrato nei passaggi precedenti quando è stato impostato correttamente il simulatore di IoT Edge. Potrebbe essere necessario creare o passare a un altro terminale integrato, se quello corrente è bloccato.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Se si usa Windows, assicurarsi che la shell del terminale integrato di Visual Studio Code sia **Git Bash** oppure **WSL Bash**. Non è possibile eseguire il comando `curl` in PowerShell o nel prompt dei comandi. 
   
   > [!TIP]
   > È anche possibile usare [PostMan](https://www.getpostman.com/) o altri strumenti API per inviare messaggi invece di `curl`.

6. Nella visualizzazione di debug di Visual Studio Code è possibile vedere le variabili nel pannello di sinistra. 

7. Per interrompere la sessione di debug, fare clic sul pulsante Arresta o premere **MAIUSC+F5**. Nel riquadro comandi di Visual Studio Code digitare e selezionare **Azure IoT Edge: Stop IoT Edge Simulator** (Azure IoT Edge: Arresta il simulatore di IoT Edge) per arrestare e pulire il simulatore.


## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>Creare il contenitore di modulo per il debug ed eseguire il debug in modalità connessione

La soluzione predefinita contiene due moduli, uno è un modulo di sensore di temperatura simulato e l'altro è il modulo di pipe Node.js. Il sensore di temperatura simulato invia costantemente messaggi al modulo di pipe Node.js e questi vengono poi inviati tramite pipe all'hub IoT. Nella cartella del modulo creato sono presenti diversi file Docker per tipi di contenitore differenti. Usare uno di questi file che terminano con l'estensione **debug** per compilare il modulo per il test. Attualmente, i moduli Node.js supportano solo il debug in contenitori linux-amd64, windows-amd64 e linux-arm32v7.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Impostare il simulatore di IoT Edge per la soluzione IoT Edge

Nel computer di sviluppo è possibile avviare il simulatore di IoT Edge invece di installare il daemon di sicurezza IoT Edge per eseguire la soluzione IoT Edge. 

1. In Device Explorer sul lato sinistro fare clic con il pulsante destro del mouse sull'ID del dispositivo IoT Edge, selezionare **Setup IoT Edge Simulator** (Installa simulatore IoT Edge) per avviare il simulatore con la stringa di connessione del dispositivo.

2. Nel terminale integrato è possibile vedere che il simulatore di IoT Edge è stato correttamente configurato.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Creare ed eseguire il contenitore per il debug ed eseguire il debug in modalità connessione

1. In VS Code passare al file `deployment.template.json`. Aggiornare l'URL dell'immagine del modulo aggiungendo **.debug** alla fine.

2. Nel modulo Node.js sostituire createOptions in **deployment.template.json** con il contenuto seguente e salvare il file: 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

3. Passare alla visualizzazione di debug di VS Code. Selezionare il file di configurazione del debug per il modulo. Il nome dell'opzione di debug deve essere simile a **Debug remoto NomeModulo (Node. js)** oppure **Debug remoto NomeModulo (Node. js nel contenitore Windows)**, che dipende dal tipo di contenitore nel computer di sviluppo.

4. Selezionare **Avvia debug** o premere **F5**. Selezionare il processo a cui collegarsi.

5. Nella visualizzazione di debug di Visual Studio Code è possibile vedere le variabili nel pannello di sinistra.

6. Per interrompere la sessione di debug, fare clic sul pulsante Arresta o premere **MAIUSC+F5**. Nel riquadro comandi di Visual Studio Code digitare e selezionare **Azure IoT Edge: Stop IoT Edge Simulator** (Azure IoT Edge: Arresta il simulatore di IoT Edge).

> [!NOTE]
> L'esempio precedente illustra come eseguire il debug di moduli IoT Edge Node.js nei contenitori. Sono state aggiunte le porte esposte nel contenitore del modulo createOptions. Al termine del debug dei moduli Node.js, si consiglia di rimuovere le porte esposte per predisporre i moduli IoT Edge per l'ambiente di produzione.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver compilato il modulo, vedere le informazioni su come [distribuire i moduli Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md).

Per sviluppare moduli per i dispositivi IoT Edge, [Comprendere e usare gli SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md).
