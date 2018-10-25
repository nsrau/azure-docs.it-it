---
title: Sviluppare ed eseguire il debug di moduli C# per Azure IoT Edge in Visual Studio 2017 | Microsoft Docs
description: Usare Visual Studio 2017 per sviluppare ed eseguire il debug di moduli C# per Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/24/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: ab4dd1186715fde51fbf188ace902c8092d192d0
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647188"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Usare Visual Studio 2017 per sviluppare ed eseguire il debug di moduli C# per Azure IoT Edge (anteprima)

È possibile trasformare la logica di business in moduli per Azure IoT Edge. Questo articolo illustra come usare Visual Studio 2017 come strumento principale per sviluppare ed eseguire il debug di moduli C#.

Azure IoT Edge Tools for Visual Studio offre i vantaggi seguenti:

- Possibilità di creare, modificare, compilare, eseguire ed effettuare il debug di soluzioni e moduli per Azure IoT Edge nel computer di sviluppo locale.
- Capacità per la distribuzione della soluzione Azure IoT Edge a un dispositivo Azure IoT Edge tramite l'hub IoT di Azure.
- Possibilità di scrivere il codice dei moduli per Azure IoT in C# sfruttando al contempo tutti i vantaggi dello sviluppo in Visual Studio.
- Funzionalità di gestione di dispositivi e moduli per Azure IoT Edge con l'interfaccia utente. 

Questo articolo illustra come usare Azure IoT Edge Tools for Visual Studio 2017 per sviluppare moduli per IoT Edge in C#. Si apprenderà anche come distribuire il progetto a un dispositivo Azure IoT Edge.

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che si usi un computer o una macchina virtuale Windows come computer di sviluppo. Il dispositivo IoT Edge può essere un altro dispositivo fisico.

Poiché questo articolo usa Visual Studio 2017 come strumento di sviluppo principale, installare Visual Studio. Assicurarsi inoltre di includere il **carico di lavoro di sviluppo di Azure** nell'installazione di Visual Studio 2017. È possibile [modificare Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) e aggiungere il carico di lavoro di sviluppo di Azure.

Una volta preparato Visual Studio 2017, è anche necessario:

- Scaricare e installare l'[estensione Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) da Visual Studio Marketplace per creare il progetto IoT Edge in Visual Studio 2017.
- [Docker Community Edition](https://docs.docker.com/install/) nel computer di sviluppo per compilare ed eseguire le immagini del modulo. È necessario configurare correttamente Docker CE per l'esecuzione in modalità contenitore Linux o in modalità contenitore Windows.
- Per impostare l'ambiente di sviluppo locale per eseguire e testare la soluzione IoT Edge ed effettuarne il debug, è necessario usare [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/). Installare [Python (2.7/3.6) e Pip](https://www.python.org/). Installare **iotedgehubdev** eseguendo questo comando nel terminale. Assicurarsi che la versione di Azure IoT EdgeHub Dev Tool sia superiore a 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) o [hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   - Per prototipi e test è possibile usare un registro Docker locale anziché un registro nel cloud. 

- Per testare il modulo, è necessario un hub IoT attivo con almeno un ID dispositivo IoT Edge creato. Se si esegue il daemon di sicurezza di IoT Edge nel computer di sviluppo, potrebbe essere necessario arrestare EdgeHub ed EdgeAgent prima di iniziare lo sviluppo in Visual Studio. 

### <a name="check-your-tools-version"></a>Controllare la versione degli strumenti

1. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**. Espandere **Installati > Strumenti** per visualizzare **Azure IoT Edge** e **Cloud Explorer**.

2. Prendere nota della versione installata. È possibile confrontare questa versione con quella più recente in Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.azureiotedgetools)).

3. Se la versione è precedente, aggiornare gli strumenti in Visual Studio come illustrato nella sezione seguente.

### <a name="update-your-tools"></a>Aggiornare gli strumenti

1. Nella finestra di dialogo **Estensioni e aggiornamenti** espandere **Aggiornamenti > Visual Studio Marketplace**, scegliere **Azure IoT Edge** o **Cloud Explorer** e selezionare **Aggiorna**.

2. Dopo aver scaricato l'aggiornamento degli strumenti, chiudere Visual Studio per attivare l'aggiornamento degli strumenti con il programma di installazione di VSIX.

3. Nel programma di installazione scegliere **OK** per avviare il processo e quindi Modifica per aggiornare gli strumenti.

4. Al termine dell'aggiornamento, scegliere Chiudi e riavviare Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Creare un progetto Azure IoT Edge

Il modello di progetto Azure IoT Edge in Visual Studio consente di creare un progetto che può essere distribuito in dispositivi Azure IoT Edge nell'hub IoT di Azure. Viene creata prima una soluzione Azure IoT Edge e quindi viene generato il primo modulo C# in tale soluzione. Ogni soluzione IoT Edge può contenere più di un modulo. 

1. In Visual Studio selezionare **Nuovo** > **Progetto** dal menu **File**.

2. Nella finestra di dialogo **Nuovo progetto** selezionare **Installato**, espandere **Visual C# > Cloud**, selezionare **Azure IoT Edge**, digitare un **nome** per il progetto e specificare la posizione e quindi fare clic su **OK**. Il nome del progetto predefinito è **AzureIoTEdgeApp1**. 

   ![Nuovo progetto](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

3. Nella finestra **Configurazione del modulo IoT Edge** selezionare **Modulo C#** e quindi digitare e specificare il nome del modulo e il repository dell'immagine del modulo.  Visual Studio popola automaticamente il nome del modulo con il valore **localhost:5000**. Sostituire tale valore con le proprie informazioni di registro. Se per il test si usa un registro Docker locale, localhost è corretto. Se si usa Registro contenitori di Azure, specificare il server di accesso indicato nelle impostazioni del registro. Il server di accesso ha un nome simile a **<registry name>.azurecr.io**. Sostituire solo la parte localhost della stringa, non eliminare il nome del modulo. Il nome del modulo predefinito è **IoTEdgeModule1**.

4. Fare clic su **OK** per creare il progetto Azure IoT Edge con un modulo C#.

Nella soluzione sono ora disponibili un progetto **AzureIoTEdgeApp1** e un progetto **IoTEdgeModule1**. Il progetto **AzureIoTEdgeApp1** include un file **deployment.template.json**. Questo file definisce i moduli da compilare e distribuire per la soluzione IoT Edge e le route tra i moduli. La soluzione predefinita contiene un modulo **tempSensor** e un modulo **IoTEdgeModule1**. Il modulo **tempSensor** genera dati simulati per il modulo **IoTEdgeModule1**, mentre il codice predefinito nel modulo **IoTEdgeModule1** è un modulo di messaggi della pipe, che invia direttamente i messaggi ricevuti all'hub IoT tramite pipe.

Il progetto **IoTEdgeModule1** è un'applicazione console .NET Core 2.1. Contiene i **Dockerfiles** richiesti per l'esecuzione del dispositivo IoT Edge con un contenitore Windows o un contenitore Linux. Il file **module.json** descrive i metadati di un modulo. **program.cs** è il codice del modulo effettivo, che usa Azure IoT SDK per dispositivi come dipendenza.

## <a name="develop-your-module"></a>Sviluppare il modulo

Il codice del modulo C# predefinito fornito con la soluzione si trova in **IoTEdgeModule1** > **Program.cs**. Il modulo e il file deployment.template.json sono impostati in modo che sia possibile compilare la soluzione, inviarla al registro del contenitore e distribuirla in un dispositivo per avviare il test senza toccare alcun codice. Il modulo viene compilato solo per accettare l'input da un'origine (in questo caso, il modulo tempSensor che simula i dati) e collegarlo all'hub IoT. 

Quando si è pronti per personalizzare il modello C# con il proprio codice, usare gli [SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md) per compilare i moduli che rispondano alle esigenze chiave delle soluzioni IoT quali sicurezza, gestione dei dispositivi e affidabilità. 

## <a name="initialize-iotegehubdev-with-iot-edge-device-connection-string"></a>Inizializzare **iotegehubdev** con la stringa di connessione del dispositivo IoT Edge

1. È necessario ottenere la stringa di connessione di qualsiasi dispositivo IoT Edge; è possibile copiare il valore dell'opzione "Stringa di connessione primaria" da Cloud Explorer in Visual Studio 2017 come descritto di seguito. Non copiare la stringa di connessione di dispositivi non Edge; l'icona dei dispositivi IoT Edge è diversa da quella dei dispositivi non Edge.

   ![Copiare la stringa di connessione del dispositivo Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

2. Fare clic con il pulsante destro del mouse sul progetto **AzureIoTEdgeApp1** per aprire il menu di scelta rapida e quindi fare clic su **Set Edge Device Connection String** (Imposta stringa di connessione dispositivo Edge). Verrà visualizzata la finestra di configurazione di Azure IoT Edge.

   ![Apertura della finestra di impostazione della stringa di connessione del dispositivo Edge](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

3. Nella finestra di configurazione immettere la stringa di connessione ottenuta nel primo passaggio e fare clic sul pulsante **OK**.

>[!NOTE]
>Si tratta di un'operazione una tantum che è necessario eseguire solo una volta in un computer: per tutte le soluzione Azure IoT Edge successive verrà eseguita automaticamente. È comunque possibile eseguire nuovamente questo passaggio se la stringa di connessione non è valida o se è necessario passare a un'altra stringa di connessione.

## <a name="build-and-debug-single-c-module"></a>Compilare ed eseguire il debug di un modulo C# singolo

In genere, è consigliabile testare o eseguire il debug di ogni modulo prima di procedere all'esecuzione all'interno di un'intera soluzione con più moduli.

1. Selezionare **IoTEdgeModule1** come progetto di avvio nel menu di scelta rapida.

   ![Impostare il progetto di avvio](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

2. Premere **F5** o fare clic sul pulsante riportato di seguito per eseguire il modulo, potrebbero occorrere circa 10-20 secondi alla prima esecuzione.

   ![Eseguire il modulo](./media/how-to-visual-studio-develop-csharp-module/run-module.png)


3. Si noterà che viene avviata un'app console .NET Core se il modulo è stato inizializzato correttamente.

   ![Modulo in esecuzione](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

4. A questo punto è possibile impostare un punto di interruzione in **PipeMessage** in **Program.cs** e quindi inviare il messaggio eseguendo il comando seguente in **Git Bash** o **WSL Bash** (non eseguirlo in CMD o PowerShell). Il comando è disponibile anche nella finestra di output di Visual Studio:

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Eseguire il debug del modulo singolo](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Il punto di interruzione deve essere attivato. È possibile controllare le variabili nella finestra Variabili locali di Visual Studio.

   > [!TIP]
   > È anche possibile usare [PostMan](https://www.getpostman.com/) o altri strumenti API per inviare messaggi invece di `curl`.

5. Premere **CTRL+F5** oppure fare clic sul pulsante Arresta per arrestare il debug.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Compilare ed eseguire il debug di una soluzione IoT Edge con più moduli

Dopo aver completato lo sviluppo di un modulo singolo, è possibile eseguire ed effettuare il debug dell'intera soluzione con più moduli.

1. Aggiungere il secondo modulo C# alla soluzione. Fare clic con il pulsante destro del mouse su **AzureIoTEdgeApp1** e selezionare **Aggiungi** -> **Nuovo modulo IoT Edge**. Il nome predefinito del secondo modulo è **IoTEdgeModule2** ed è ancora un modulo di pipe.

2. Passare a **deployment.template.json**. Si noterà che **IoTEdgeModule2** è stato aggiunto nella sezione **modules**. Sostituire la sezione **routes** con il contenuto seguente. Se i nomi dei moduli sono stati personalizzati, assicurarsi di aggiornare i nomi nell'esempio seguente dopo la sostituzione.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

3. Impostare il progetto **AzureIoTEdgeApp1** come progetto di avvio nel menu di scelta rapida.

4. Impostare i punti di interruzione e premere F5. È quindi possibile eseguire ed effettuare il debug di più moduli contemporaneamente. Si dovrebbero vedere più finestre dell'app console .NET Core, ognuna delle quali indica il modulo C#. 

   ![Eseguire il debug di più moduli](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

5. Premere **CTRL+F5** oppure fare clic sul pulsante Arresta per arrestare il debug.

## <a name="build-and-push-images"></a>Compilare le immagini ed eseguirne il push

1. Assicurarsi che **AzureIoTEdgeApp1** sia il progetto di avvio. Selezionare la configurazione **Debug** oppure **Versione** per la compilazione delle immagini del modulo.

    > [!NOTE]
    > Quando si sceglie **Debug**, Visual Studio userà `Dockerfile.debug` per compilare le immagini Docker. Ciò include VSDBG, ovvero il debugger della riga di comando di .NET Core, nell'immagine del contenitore durante la compilazione. È consigliabile usare la configurazione **Versione** che usa `Dockerfile` senza VSDBG per moduli IoT Edge per l'ambiente di produzione.

2. Se si usa un registro privato come Registro contenitori di Azure, eseguire il log di Docker con il comando seguente nel terminale. Se si usa un registro locale, è possibile [eseguire un registro locale](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server> 
    ```

3. Se si usa un registro privato come Registro contenitori di Azure, è necessario inserire il nome utente e la password del registro in `deployment.template.json` nelle impostazioni di runtime con il contenuto seguente. Ricordarsi di sostituire il segnaposto con il nome utente amministratore e la password effettivi.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

4. Fare clic con il pulsante destro del mouse su **AzureIoTEdgeApp1** e scegliere la voce del menu di scelta rapida **Build and Push Edge Solution** (Compila ed esegui il push della soluzione Edge). Per ogni modulo si compilerà un'immagine Docker e ne verrà eseguito il push.

   ![Compilare le immagini ed eseguirne il push](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)


## <a name="deploy-the-solution"></a>Distribuire la soluzione

Nell'articolo della guida introduttiva usato per configurare il dispositivo IoT Edge è stato distribuito un modulo usando il portale di Azure. È anche possibile distribuire i moduli con Cloud Explorer per Visual Studio. Il manifesto della distribuzione, il file `deployment.json`, è già disponibile per questo scenario. Ora è sufficiente selezionare un dispositivo che riceverà la distribuzione.

1. Aprire **Cloud Explorer** facendo clic su **Visualizza** > **Cloud Explorer**. Assicurarsi di avere eseguito l'accesso a Visual Studio 2017 con il proprio account.

2. In **Cloud Explorer** espandere la sottoscrizione, trovare l'hub IoT di Azure e il dispositivo Azure IoT Edge da distribuire.

3. Fare clic con il pulsante destro del mouse sul dispositivo IoT Edge per creare una distribuzione. È necessario scegliere il file manifesto della distribuzione in `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

>>[!NOTE]
>>Non si deve selezionare `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

4. Fare clic sul pulsante Aggiorna. Dovrebbero essere visualizzati i nuovi moduli in esecuzione insieme al modulo **TempSensor** e a **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Visualizzare i dati generati

1. Per monitorare il messaggio D2C per un dispositivo specifico, fare clic sul dispositivo nell'elenco e selezionare **Start Monitoring D2C Messages** (Avvia il monitoraggio dei messaggi D2C) nella finestra **Azione**.

2. Per arrestare il monitoraggio dei dati, fare clic sul dispositivo nell'elenco e selezionare **Stop Monitoring D2C Messages** (Arresta il monitoraggio dei messaggi D2C) nella finestra **Azione**.

## <a name="next-steps"></a>Passaggi successivi

Per sviluppare moduli per i dispositivi IoT Edge, [Comprendere e usare gli SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md).
