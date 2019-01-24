---
title: Sviluppare ed eseguire il debug di moduli C# in Visual Studio - Azure IoT Edge | Microsoft Docs
description: Usare Visual Studio 2017 per sviluppare ed eseguire il debug di moduli C# per Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/10/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3091e53b47d85e3e58c32b255dc9dcebd54ab2c5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264938"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Usare Visual Studio 2017 per sviluppare ed eseguire il debug di moduli C# per Azure IoT Edge (anteprima)

È possibile trasformare la logica di business in moduli per Azure IoT Edge. Questo articolo illustra come usare Visual Studio 2017 come strumento principale per sviluppare ed eseguire il debug di moduli C#.

Azure IoT Edge Tools for Visual Studio offre i vantaggi seguenti:

- Possibilità di creare, modificare, compilare, eseguire ed effettuare il debug di soluzioni e moduli per Azure IoT Edge nel computer di sviluppo locale.
- Capacità per la distribuzione della soluzione Azure IoT Edge a un dispositivo Azure IoT Edge tramite l'hub IoT di Azure.
- Possibilità di scrivere il codice dei moduli per Azure IoT in C# sfruttando al contempo tutti i vantaggi dello sviluppo in Visual Studio.
- Funzionalità di gestione di dispositivi e moduli per Azure IoT Edge con l'interfaccia utente.

Questo articolo illustra come usare Azure IoT Edge Tools for Visual Studio 2017 per sviluppare moduli per IoT Edge in C#. Si apprenderà anche come distribuire il progetto a un dispositivo Azure IoT Edge. 

  > [!TIP]
  > La struttura del progetto IoT Edge creato da Visual Studio è diversa rispetto a quella in Visual Studio Code.
  
## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si usi un computer o una macchina virtuale Windows come computer di sviluppo. Il dispositivo IoT Edge può essere un altro dispositivo fisico.

Poiché questo articolo usa Visual Studio 2017 come strumento di sviluppo principale, installare Visual Studio. Assicurarsi inoltre di includere il **carico di lavoro di sviluppo di Azure** nell'installazione di Visual Studio 2017. È possibile [modificare Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) e aggiungere il carico di lavoro di sviluppo di Azure.

Una volta preparato Visual Studio 2017, sono necessari anche gli strumenti e i componenti seguenti:

- Scaricare e installare l'[estensione Azure IoT Edge (anteprima)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) da Visual Studio Marketplace per creare un progetto IoT Edge in Visual Studio 2017.

- Scaricare e installare [Docker Community Edition](https://docs.docker.com/install/) nel computer di sviluppo per compilare ed eseguire le immagini del modulo. È necessario configurare Docker CE per l'esecuzione in modalità contenitore Linux o in modalità contenitore Windows.

- Impostare l'ambiente di sviluppo locale per eseguire e testare la soluzione IoT Edge ed effettuarne il debug installando [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/). Installare [Python (2.7/3.6) e Pip](https://www.python.org/) e quindi il pacchetto **iotedgehubdev** eseguendo il comando seguente nel terminale. Assicurarsi che la versione di Azure IoT EdgeHub Dev Tool sia superiore a 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/) o [hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Per prototipi e test è possibile usare un registro Docker locale anziché un registro nel cloud.

- Per testare il modulo in un dispositivo, è necessario un hub IoT attivo con almeno un dispositivo IoT Edge. Per usare il computer come dispositivo IoT Edge, seguire la procedura nella guida di avvio rapido per [Linux](quickstart-linux.md) o [Windows](quickstart.md). Se si esegue il daemon di IoT Edge nel computer di sviluppo, potrebbe essere necessario arrestare EdgeHub ed EdgeAgent prima di iniziare lo sviluppo in Visual Studio.

### <a name="check-your-tools-version"></a>Controllare la versione degli strumenti

1. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**. Espandere **Installati > Strumenti** per visualizzare **Azure IoT Edge Tools** e **Cloud Explorer for Visual Studio**.

1. Prendere nota della versione installata. È possibile confrontare questa versione con quella più recente in Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)).

1. Se la versione è precedente a quella disponibile in Visual Studio Marketplace, aggiornare gli strumenti in Visual Studio come illustrato nella sezione seguente.

### <a name="update-your-tools"></a>Aggiornare gli strumenti

1. Nella finestra di dialogo **Estensioni e aggiornamenti** espandere **Aggiornamenti > Visual Studio Marketplace**, selezionare **Azure IoT Edge Tools** o **Cloud Explorer for Visual Studio** e selezionare **Aggiorna**.

1. Dopo aver scaricato l'aggiornamento degli strumenti, chiudere Visual Studio per attivare l'aggiornamento degli strumenti con il programma di installazione di VSIX.

1. Nel programma di installazione selezionare **OK** per avviare il processo e quindi **Modifica** per aggiornare gli strumenti.

1. Al termine dell'aggiornamento, selezionare **Chiudi** e riavviare Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Creare un progetto Azure IoT Edge

Il modello di progetto Azure IoT Edge in Visual Studio consente di creare un progetto che può essere distribuito in dispositivi Azure IoT Edge nell'hub IoT di Azure. Viene creata prima una soluzione Azure IoT Edge e quindi viene generato il primo modulo C# in tale soluzione. Ogni soluzione IoT Edge può contenere più di un modulo.

1. In Visual Studio selezionare **Nuovo** > **Progetto** dal menu **File**.

1. Nella finestra di dialogo **Nuovo progetto** selezionare **Installato**, espandere **Visual C# > Cloud**, selezionare **Azure IoT Edge**, immettere un nome per il progetto e specificare la posizione e quindi selezionare **OK**. Il nome del progetto predefinito è **AzureIoTEdgeApp1**.

   ![Nuovo progetto](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

1. Nella finestra **Configurazione del modulo IoT Edge** selezionare **Modulo C#** e quindi specificare il nome del modulo e il repository dell'immagine del modulo. Visual Studio popola automaticamente il nome del modulo con il valore **localhost:5000/<nome del modulo\>**. Sostituire tale valore con le proprie informazioni di registro. Se per il test si usa un registro Docker locale, **localhost** è corretto. Se si usa Registro contenitori di Azure, specificare il server di accesso indicato nelle impostazioni del registro. Il server di accesso ha un nome simile a ***\<nome registro\>*.azurecr.io**. Sostituire solo la parte **localhost:5000** della stringa, in modo che il risultato finale sia simile a **\<* nome registro*\>.azurecr.io/*\<nome del modulo\>***. Il nome del modulo predefinito è **IoTEdgeModule1**.

1. Selezionare **OK** per creare il progetto Azure IoT Edge con un modulo C#.

Nella soluzione sono ora disponibili un progetto **AzureIoTEdgeApp1** e un progetto **IoTEdgeModule1**. Il progetto **AzureIoTEdgeApp1** include un file **deployment.template.json**. Questo file definisce i moduli da compilare e distribuire per la soluzione IoT Edge, oltre alle route tra i moduli. La soluzione predefinita contiene un modulo **tempSensor** e un modulo **IoTEdgeModule1**. Il modulo **tempSensor** genera dati simulati per il modulo **IoTEdgeModule1**, mentre il codice predefinito nel modulo **IoTEdgeModule1** invia direttamente i messaggi ricevuti all'hub IoT di Azure tramite pipe.

Il progetto **IoTEdgeModule1** è un'applicazione console .NET Core 2.1. Contiene i file Docker richiesti per l'esecuzione del dispositivo IoT Edge con un contenitore Windows o un contenitore Linux. Il file **module.json** descrive i metadati di un modulo. **program.cs** è il codice del modulo effettivo, che usa Azure IoT SDK per dispositivi come dipendenza.

## <a name="develop-your-module"></a>Sviluppare il modulo

Il codice del modulo C# predefinito fornito con la soluzione si trova in **IoTEdgeModule1** > **Program.cs**. Il modulo e il file deployment.template.json sono impostati in modo che sia possibile compilare la soluzione, inviarla al registro del contenitore e distribuirla in un dispositivo per avviare il test senza toccare alcun codice. Il modulo viene compilato per accettare l'input da un'origine (in questo caso, il modulo **tempSensor** che simula i dati) e collegarlo all'hub IoT di Azure tramite pipe.

Quando si è pronti per personalizzare il modello C# con il proprio codice, usare gli [SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md) per compilare i moduli che rispondano alle esigenze chiave delle soluzioni IoT quali sicurezza, gestione dei dispositivi e affidabilità.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inizializzare iotedgehubdev con la stringa di connessione del dispositivo IoT Edge

1. Copiare la stringa di connessione di un dispositivo IoT Edge qualsiasi da **Stringa di connessione primaria** in Visual Studio Cloud Explorer. Assicurarsi di non copiare la stringa di connessione di un dispositivo non Edge, dal momento che l'icona di un dispositivo IoT Edge è diversa da quella di un dispositivo non Edge.

   ![Copiare la stringa di connessione del dispositivo Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Fare clic con il pulsante destro del mouse sul progetto **AzureIoTEdgeApp1** e quindi fare clic su **Set Edge Device Connection String** (Imposta stringa di connessione dispositivo Edge) per aprire la finestra di configurazione di Azure IoT Edge.

   ![Apertura della finestra di impostazione della stringa di connessione del dispositivo Edge](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Immettere la stringa di connessione del primo passaggio e quindi selezionare **OK**.

> [!NOTE]
> È necessario seguire questa procedura solo una volta nel computer di sviluppo, poiché i risultati vengono applicati automaticamente a tutte le soluzioni Azure IoT Edge successive. Questa procedura può essere eseguita nuovamente se è necessario usare una stringa di connessione diversa.

## <a name="build-and-debug-single-c-module"></a>Compilare ed eseguire il debug di un modulo C# singolo

In genere, è consigliabile testare ed eseguire il debug di ogni modulo prima di eseguirlo all'interno di un'intera soluzione con più moduli.

1. Fare clic con il pulsante destro del mouse su **IoTEdgeModule1** e selezionare **Imposta come progetto di avvio** dal menu di scelta rapida.

   ![Impostare il progetto di avvio](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Premere **F5** o fare clic sul pulsante riportato di seguito per eseguire il modulo; potrebbero occorrere circa 10&ndash;20 secondi alla prima esecuzione.

   ![Eseguire il modulo](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Si noterà che viene avviata un'app console .NET Core se il modulo è stato inizializzato correttamente.

   ![Modulo in esecuzione](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Impostare un punto di interruzione nella funzione `PipeMessage()` in **Program.cs** e quindi testarlo inviando un messaggio tramite l'esecuzione del comando seguente in una shell **Git Bash** o **WSL Bash** (non è possibile eseguire il comando `curl` in PowerShell o nel prompt dei comandi).

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Eseguire il debug del modulo singolo](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Il punto di interruzione deve essere attivato. È possibile controllare le variabili nella finestra **Variabili locali** di Visual Studio.

   > [!TIP]
   > È anche possibile usare [PostMan](https://www.getpostman.com/) o altri strumenti API per inviare messaggi invece di `curl`.

1. Premere **CTRL+F5** oppure fare clic sul pulsante Arresta per arrestare il debug.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Compilare ed eseguire il debug di una soluzione IoT Edge con più moduli

Dopo aver completato lo sviluppo di un modulo singolo, è possibile eseguire ed effettuare il debug di un'intera soluzione con più moduli.

1. Aggiungere un secondo modulo C# alla soluzione facendo clic con il pulsante destro del mouse su **AzureIoTEdgeApp1** e selezionando **Aggiungi** > **Nuovo modulo IoT Edge**. Il nome predefinito del secondo modulo è **IoTEdgeModule2** e si comporterà come un altro modulo di pipe.

1. Aprire il file `deployment.template.json`. Si noterà che **IoTEdgeModule2** è stato aggiunto nella sezione **modules**. Sostituire la sezione **routes** con il contenuto seguente. Se i nomi dei moduli sono stati personalizzati, assicurarsi di aggiornare questi nomi in modo che corrispondano.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. Fare clic con il pulsante destro del mouse su **AzureIoTEdgeApp1** e selezionare **Imposta come progetto di avvio** dal menu di scelta rapida.

1. Creare i punti di interruzione e quindi premere **F5** per eseguire ed effettuare il debug di più moduli contemporaneamente. Si dovrebbero vedere più finestre dell'app console .NET Core, ognuna delle quali rappresenta un modulo C# diverso.

   ![Eseguire il debug di più moduli](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Premere **CTRL+F5** oppure selezionare il pulsante Arresta per arrestare il debug.

## <a name="build-and-push-images"></a>Compilare le immagini ed eseguirne il push

1. Assicurarsi che **AzureIoTEdgeApp1** sia il progetto di avvio. Selezionare **Debug** o **Versione** come configurazione per la compilazione delle immagini del modulo.

    > [!NOTE]
    > Quando si sceglie **Debug**, Visual Studio usa `Dockerfile.debug` per compilare le immagini Docker. Ciò include VSDBG, ovvero il debugger della riga di comando di .NET Core, nell'immagine del contenitore durante la compilazione. Per i moduli IoT Edge per l'ambiente di produzione, è consigliabile usare la configurazione **Versione**, che usa `Dockerfile` senza VSDBG.

1. Se si usa un registro privato come Registro Azure Container, usare il comando Docker seguente per l'accesso. Se si usa un registro locale, è possibile [eseguire un registro locale](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Se si usa un registro privato come Registro Azure Container, è necessario aggiungere le informazioni di accesso del registro alle impostazioni di runtime presenti nel file `deployment.template.json`. Sostituire i segnaposto con il nome utente amministratore, la password e il nome del registro di Registro Azure Container effettivi.

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

1. Fare clic con il pulsante destro del mouse su **AzureIoTEdgeApp1** e selezionare **Build and Push Edge Solution** (Compila ed esegui il push della soluzione Edge) per compilare ed eseguire il push dell'immagine Docker per ogni modulo.

   ![Compilare le immagini ed eseguirne il push](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Nell'articolo della guida introduttiva usato per configurare il dispositivo IoT Edge è stato distribuito un modulo usando il portale di Azure. È anche possibile distribuire i moduli con Cloud Explorer per Visual Studio. Il manifesto della distribuzione, il file `deployment.json`, è già disponibile per questo scenario; è sufficiente selezionare un dispositivo che riceverà la distribuzione.

1. Aprire **Cloud Explorer** facendo clic su **Visualizza** > **Cloud Explorer**. Assicurarsi di avere eseguito l'accesso a Visual Studio 2017.

1. In **Cloud Explorer** espandere la sottoscrizione, trovare l'hub IoT di Azure e il dispositivo Azure IoT Edge da distribuire.

1. Fare clic con il pulsante destro del mouse sul dispositivo IoT Edge per creare una distribuzione. È necessario selezionare il file manifesto della distribuzione in `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Non si deve selezionare `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Fare clic sul pulsante Aggiorna per visualizzare i nuovi moduli in esecuzione insieme al modulo **TempSensor** e a **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Visualizzare i dati generati

1. Per monitorare il messaggio D2C per un dispositivo specifico, selezionare il dispositivo nell'elenco e quindi fare clic su **Start Monitoring D2C Messages** (Avvia il monitoraggio dei messaggi D2C) nella finestra **Azione**.

1. Per arrestare il monitoraggio dei dati, selezionare il dispositivo nell'elenco e quindi selezionare **Stop Monitoring D2C Messages** (Arresta il monitoraggio dei messaggi D2C) nella finestra **Azione**.

## <a name="next-steps"></a>Passaggi successivi

Per sviluppare moduli personalizzati per i dispositivi IoT Edge, [comprendere e usare gli SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md).
