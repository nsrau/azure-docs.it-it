---
title: Eseguire il debug di moduli C# per Azure IoT Edge | Microsoft Docs
description: Usare Visual Studio Code per sviluppare, compilare ed eseguire il debug di un modulo C# per Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 275ee95261b168b0da7f0a4638679fe38fc0581b
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443888"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Usare Visual Studio Code per sviluppare ed eseguire il debug di moduli C# per Azure IoT Edge

È possibile trasformare la logica di business in moduli per Azure IoT Edge. Questo articolo illustra come usare Visual Studio Code (VS Code) come strumento principale per sviluppare ed eseguire il debug di moduli C#.

## <a name="prerequisites"></a>Prerequisiti

È possibile usare un computer o una macchina virtuale Windows, macOS o Linux come computer di sviluppo. Un dispositivo IoT Edge può essere un altro dispositivo fisico.

Esistono due modi per eseguire il debug del modulo C# in Visual Studio Code. Un modo consiste nell'associare un processo in un contenitore di modulo, mentre l'altro consiste nell'avviare il codice del modulo in modalità di debug. Se non si ha ancora familiarità con le funzionalità di debug di Visual Studio Code, vedere le [informazioni sul debug](https://code.visualstudio.com/Docs/editor/debugging).

Installare prima Visual Studio Code e quindi aggiungere le estensioni necessarie seguenti:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Estensione Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Estensione Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Per creare un modulo, è necessario .NET per creare la cartella del progetto, Docker per creare l'immagine del modulo e un registro contenitori in cui memorizzare l'immagine del modulo:
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker Community Edition](https://docs.docker.com/install/) nel computer di sviluppo. 
* [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) o [hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Per prototipi e test è possibile usare un registro Docker locale anziché un registro nel cloud. 

Per impostare l'ambiente di sviluppo locale per effettuare il debug ed eseguire e testare la soluzione IoT Edge, è necessario usare [Azure IoT Edge Hub Dev Tool](https://pypi.org/project/iotedgehubdev/). Installare [Python (2.7/3.6) e Pip](https://www.python.org/). Installare **iotedgehubdev** eseguendo questo comando nel terminale.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

Per testare il modulo in un dispositivo, è necessario un hub IoT attivo con almeno un ID dispositivo IoT Edge creato. Se si esegue il daemon IoT Edge nel computer di sviluppo, potrebbe essere necessario arrestare EdgeHub ed EdgeAgent prima di andare al passaggio successivo. 

## <a name="create-a-new-solution-with-c-module"></a>Creare una nuova soluzione con il modulo C#

Eseguire questi passaggi per creare un modulo IoT Edge basato su .NET Core 2.1 tramite Visual Studio Code e l'estensione Azure IoT Edge. Viene creata prima una soluzione e quindi viene generato il primo modulo in tale soluzione. Ogni soluzione può contenere più di un modulo. 

1. In Visual Studio Code selezionare **Visualizza** > **Terminale integrato**.
2. Selezionare **Visualizza** > **Riquadro comandi**. 
3. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nuova soluzione IoT Edge).

   ![Eseguire il comando per creare una nuova soluzione IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

4. Passare alla cartella in cui si vuole creare la nuova soluzione. Scegliere **Seleziona cartella**. 
5. Immettere un nome per la soluzione. 
6. Selezionare **Modulo C#** come modello per il primo modulo nella soluzione.
7. Immettere un nome per il modulo. Scegliere un nome univoco all'interno del registro contenitori. 
8. Specificare il nome del repository di immagini del modulo. VS Code popola automaticamente il nome del modulo con il valore **localhost:5000**. Sostituire tale valore con le proprie informazioni di registro. Se per il test si usa un registro Docker locale, **localhost** è corretto. Se si usa Registro contenitori di Azure, specificare il server di accesso indicato nelle impostazioni del registro. Il server di accesso ha un nome simile a **\<nome registro\>.azurecr.io**. Sostituire solo la parte localhost della stringa, non eliminare il nome del modulo. La stringa finale è simile a \<nome registro\>.azurecr.io/\<nomemodulo\>.

   ![Specificare il repository di immagini Docker](./media/how-to-develop-csharp-module/repository.png)

VS Code usa le informazioni specificate per creare una soluzione IoT Edge e quindi la carica in una nuova finestra.

   ![Visualizzare la soluzione IoT Edge](./media/how-to-develop-csharp-module/view-solution.png)

Nella soluzione sono presenti quattro elementi: 

* Una cartella **.vscode** contenente le configurazioni di debug.
* Una cartella **modules** contenente le sottocartelle di ogni modulo. A questo punto è presente un solo elemento. È possibile tuttavia aggiungere altri elemento nel riquadro comandi con il comando **Azure IoT Edge: Aggiungi modulo Edge IoT**. 
* Un file con estensione **env** in cui sono elencate le variabili di ambiente. Se Registro contenitori di Azure è il registro, si avranno un nome utente e una password per Registro contenitori di Azure. 

   >[!NOTE]
   >Il file dell'ambiente viene creato solo se si specifica un repository di immagini per il modulo. Se sono state accettate le impostazioni predefinite di localhost per testare ed eseguire il debug in locale, non è necessario dichiarare le variabili di ambiente. 

* Un file **deployment.template.json** in cui sono elencati il nuovo modulo e un modulo **tempSensor** di esempio che simula i dati utilizzabili per il test. Per altre informazioni su come funzionano i manifesti di distribuzione, vedere [Informazioni su come usare i manifesti della distribuzione per distribuire moduli e definire route](module-composition.md). 
* Un file **deployment.debug.template.json** contenente la versione di debug delle immagini del modulo con le opzioni per il contenitore appropriato.


## <a name="develop-your-module"></a>Sviluppare il modulo

Il codice del modulo C# predefinito fornito con la soluzione si trova in **modules** > ** [nome del modulo] ** > **Program.cs**. Il modulo e il file deployment.template.json sono impostati in modo che sia possibile compilare la soluzione, inviarla al registro del contenitore e distribuirla in un dispositivo per avviare il test senza toccare alcun codice. Il modulo viene compilato solo per accettare l'input da un'origine (in questo caso, il modulo tempSensor che simula i dati) e collegarlo all'hub IoT. 

Quando si è pronti per personalizzare il modello C# con il proprio codice, usare gli [SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md) per compilare i moduli che rispondano alle esigenze chiave delle soluzioni IoT quali sicurezza, gestione dei dispositivi e affidabilità. 

Il supporto per C# in Visual Studio Code è ottimizzato per lo sviluppo di .NET Core multipiattaforma. Altre informazioni su [come usare C# in Visual Studio Code](https://code.visualstudio.com/docs/languages/csharp).

## <a name="launch-and-debug-module-code-without-container"></a>Avviare il codice del modulo ed eseguirne il debug senza contenitore

Il modulo C# di IoT Edge è un'applicazione .NET Core e dipende da Azure IoT C# Device SDK. Il modulo C# di IoT richiede l'avvio e l'esecuzione delle impostazioni di ambiente; nel codice del modulo predefinito si inizializza quindi un **ModuleClient** con le impostazioni di ambiente e il nome di input. È necessario anche inviare o indirizzare i messaggi ai canali di input. Il modulo C# predefinito contiene solo un canale di input, denominato **input1**.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Impostare il simulatore di IoT Edge per la soluzione IoT Edge

Nel computer di sviluppo è possibile avviare il simulatore di IoT Edge invece di installare il daemon di sicurezza IoT Edge per eseguire la soluzione IoT Edge. 

1. In Device Explorer sul lato sinistro fare clic con il pulsante destro del mouse sull'ID del dispositivo IoT Edge, selezionare **Setup IoT Edge Simulator** (Installa simulatore IoT Edge) per avviare il simulatore con la stringa di connessione del dispositivo.

2. Nel terminale integrato è possibile vedere che il simulatore di IoT Edge è stato correttamente configurato.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>Impostare il simulatore di IoT Edge per l'app a modulo singolo
Nel computer di sviluppo è possibile avviare il simulatore di IoT Edge invece di installare il daemon di sicurezza IoT Edge per eseguire la soluzione IoT Edge. 

1. In Device Explorer sul lato sinistro fare clic con il pulsante destro del mouse sull'ID del dispositivo IoT Edge, selezionare **Setup IoT Edge Simulator** (Installa simulatore IoT Edge) per avviare il simulatore con la stringa di connessione del dispositivo.

2. Nel terminale integrato è possibile vedere che il simulatore di IoT Edge è stato correttamente configurato.

3. Nel riquadro comandi di Visual Studio Code digitare e selezionare **Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module** (Azure IoT Edge: Avvia simulatore hub di IoT Edge per modulo singolo). È necessario anche specificare i nomi di input per l'applicazione a modulo singolo, digitare **input1** e premere INVIO. Il comando attiverà l'interfaccia della riga di comando **iotedgehubdev** e avvierà il simulatore di IoT Edge e un contenitore di modulo utilità di test. Se il simulatore è stato avviato correttamente in modalità modulo singolo, è possibile vedere l'output seguente nel terminale integrato. È anche possibile vedere un comando `curl` che aiuta a inviare messaggi e che sarà necessario più avanti.

   ![Impostare il simulatore di IoT Edge per l'app a modulo singolo](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   È possibile passare a Docker Explorer e vedere lo stato di esecuzione del modulo.

   ![Stato del modulo del simulatore](media/how-to-develop-csharp-module/simulator-status.png)

   Il contenitore **edgeHubDev** costituisce il nucleo del simulatore di IoT Edge locale. Può essere eseguito nel computer di sviluppo senza il daemon di sicurezza di IoT Edge e fornire le impostazioni di ambiente per l'app a modulo nativo o i contenitori del modulo. Il contenitore **input** espone API REST per agevolare il bridging dei messaggi al canale di input di destinazione nel modulo.

4. Nel riquadro comandi di Visual Studio Code digitare e selezionare **Azure IoT Edge: Set Module Credentials to User Settings** (Azure IoT Edge: Impostare le credenziali del modulo sulle impostazioni utente) per specificare le impostazioni di ambiente del modulo in `azure-iot-edge.EdgeHubConnectionString` e `azure-iot-edge.EdgeModuleCACertificateFile` nelle impostazioni utente. È possibile trovare queste impostazioni di ambiente in **vscode** > **launch.json** e nelle [impostazioni utente di Visual Studio Code](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="build-module-app-and-debug-in-launch-mode"></a>Compilare l'app modulo ed eseguirne il debug in modalità di avvio

1. Nel terminale integrato passare alla cartella **CSharpModule** ed eseguire questo comando per compilare l'applicazione .NET Core.

    ```cmd
    dotnet build
    ```

2. Accedere a `program.cs`. Aggiungere un punto di interruzione in questo file.

3. Passare alla visualizzazione di debug di Visual Studio Code: Visualizza > Debug. Selezionare la configurazione di debug **Debug locale NomeModulo (.NET Core)** nell'elenco a discesa. 

  ![Passare alla modalità di debug di Visual Studio Code](media/how-to-develop-csharp-module/debug-view.png)

4. Fare clic su **Avvia debug** o premere **F5**. Verrà avviata la sessione di debug.

   > [!NOTE]
   > Se il `TargetFramework` di .NET Core non è coerente con il percorso del programma in `launch.json`, è necessario aggiornare manualmente il percorso del programma in `launch.json` per rispettare il `TargetFramework` nel file con estensione csproj. In questo modo, Visual Studio Code può avviare correttamente il programma.

5. Nel terminale integrato di Visual Studio Code eseguire questo comando per inviare un messaggio **Hello World** al modulo. Questo è il comando mostrato nei passaggi precedenti quando è stato impostato correttamente il simulatore di IoT Edge.

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Se si usa Windows, assicurarsi che la shell del terminale integrato di Visual Studio Code sia **Git Bash** oppure **WSL Bash**. Non è possibile eseguire il comando `curl` in PowerShell o nel prompt dei comandi. 
   
   > [!TIP]
   > È anche possibile usare [PostMan](https://www.getpostman.com/) o altri strumenti API per inviare messaggi invece di `curl`.

6. Nella visualizzazione di debug di Visual Studio Code è possibile vedere le variabili nel pannello di sinistra. 

    ![Variabili delle espressioni di controllo](media/how-to-develop-csharp-module/single-module-variables.png)

7. Per interrompere la sessione di debug, fare clic sul pulsante Arresta o premere **MAIUSC+F5**. Nel riquadro comandi di Visual Studio Code digitare e selezionare **Azure IoT Edge: Stop IoT Edge Simulator** (Azure IoT Edge: Arresta il simulatore di IoT Edge) per arrestare e pulire il simulatore.

## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>Creare il contenitore di modulo per il debug ed eseguire il debug in modalità connessione

La soluzione predefinita contiene due moduli, uno è un modulo di sensore di temperatura simulato e l'altro è il modulo di pipe C#. Il sensore di temperatura simulato invia costantemente messaggi al modulo di pipe C# e questi vengono poi inviati tramite pipe all'hub IoT. Nella cartella del modulo creato sono presenti diversi file Docker per tipi di contenitore differenti. Usare uno di questi file che terminano con l'estensione **debug** per compilare il modulo per il test. Per impostazione predefinita, **deployment.debug.template.json** contiene la versione di debug dell'immagine. I moduli C# supportano attualmente il debug solo in contenitori amd64 Linux in modalità connessione. È possibile impostare la piattaforma predefinita di Azure IoT Edge nella barra di stato di Visual Studio Code.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Impostare il simulatore di IoT Edge per la soluzione IoT Edge

Nel computer di sviluppo è possibile avviare il simulatore di IoT Edge invece di installare il daemon di sicurezza IoT Edge per eseguire la soluzione IoT Edge. 

1. In Device Explorer sul lato sinistro fare clic con il pulsante destro del mouse sull'ID del dispositivo IoT Edge, selezionare **Setup IoT Edge Simulator** (Installa simulatore IoT Edge) per avviare il simulatore con la stringa di connessione del dispositivo.

2. Nel terminale integrato è possibile vedere che il simulatore di IoT Edge è stato correttamente configurato.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Creare ed eseguire il contenitore per il debug ed eseguire il debug in modalità connessione

1. Accedere a `program.cs`. Aggiungere un punto di interruzione in questo file.

2. In Esplora file di Visual Studio Code selezionare il file `deployment.debug.template.json` della soluzione e nel menu di scelta rapida fare clic su **Build and Run IoT Edge solution in Simulator** (Compila ed esegui la soluzione IoT Edge nel simulatore). È possibile esaminare tutti i log del contenitore del modulo nella stessa finestra. È anche possibile passare a Docker Explorer per controllare lo stato del contenitore.

   ![Variabili delle espressioni di controllo](media/how-to-develop-csharp-module/view-log.png)

3. Passare alla visualizzazione di debug di VS Code. Selezionare il file di configurazione del debug per il modulo. Il nome dell'opzione di debug deve essere simile a **Debug remoto NomeModulo (.NET Core)**.

   ![Selezionare la configurazione](media/how-to-develop-csharp-module/debug-config.png)

4. Selezionare **Avvia debug** o premere **F5**. Selezionare il processo a cui collegarsi.

5. Nella visualizzazione di debug di Visual Studio Code è possibile vedere le variabili nel pannello di sinistra.

6. Per interrompere la sessione di debug, fare clic sul pulsante Arresta o premere **MAIUSC+F5**. Nel riquadro comandi di Visual Studio Code digitare e selezionare **Azure IoT Edge: Stop IoT Edge Simulator** (Azure IoT Edge: Arresta il simulatore di IoT Edge).

    > [!NOTE]
    > Questo esempio illustra come eseguire il debug di moduli IoT Edge in .NET Core in contenitori. L'esempio si basa sulla versione di debug di `Dockerfile.debug`, che include VSDBG, ovvero il debugger della riga di comando di Visual Studio .NET Core, nell'immagine del contenitore durante la compilazione. Dopo aver eseguito il debug dei moduli C#, si consiglia di usare direttamente Dockerfile senza VSDBG per moduli IoT Edge per l'ambiente di produzione.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver compilato il modulo, vedere le informazioni su come [distribuire i moduli Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md).

Per sviluppare moduli per i dispositivi IoT Edge, [Comprendere e usare gli SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md).
