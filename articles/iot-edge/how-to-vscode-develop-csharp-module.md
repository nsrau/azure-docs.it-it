---
title: Usare Visual Studio Code per sviluppare un modulo C# con Azure IoT Edge | Microsoft Docs
description: Sviluppare e distribuire un modulo C# con Azure IoT Edge in Visual Studio Code senza cambio di contesto
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cad28b4e6d4e46058641da19795cd71efdbd0c92
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="use-visual-studio-code-to-develop-c-module-with-azure-iot-edge"></a>Usare Visual Studio Code per sviluppare un modulo C# con Azure IoT Edge
Questo articolo fornisce istruzioni dettagliate per usare [Visual Studio Code](https://code.visualstudio.com/) come strumento principale per lo sviluppo e la distribuzione di moduli IoT Edge. 

## <a name="prerequisites"></a>Prerequisiti
Questa esercitazione presuppone che si usi un computer o una macchina virtuale Windows o Linux come computer di sviluppo. Il dispositivo IoT Edge può essere un altro dispositivo fisico oppure è possibile simulare il dispositivo IoT Edge nel computer di sviluppo.

Completare le esercitazioni seguenti prima di procedere con queste istruzioni.
- Distribuire Azure IoT Edge su un dispositivo simulato in [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) o [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Sviluppare e distribuire un modulo C# per IoT Edge in un dispositivo simulato](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

L'elenco di controllo seguente illustra gli elementi che è necessario avere a disposizione dopo aver completato le esercitazioni precedenti.

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- [Python 2.7](https://www.python.org/downloads/)
- [Script di controllo IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Modello AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Un hub IoT attivo con almeno un dispositivo IoT Edge

È inoltre consigliabile installare il [supporto Docker per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) per gestire in modo più efficiente le immagini e i contenitori del modulo.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Distribuire un modulo Azure IoT Edge in Visual Studio Code

### <a name="list-your-iot-hub-devices"></a>Elenco dei dispositivi dell'hub IoT
Per ottenere l'elenco dei dispositivi dell'hub IoT in Visual Studio Code sono disponibili due diverse procedure. Scegliere una delle due per continuare.

#### <a name="sign-in-your-azure-account-in-vscode-and-choose-your-iot-hub"></a>Accedere con l'account di Azure in Visual Studio Code e scegliere l'hub IoT
1. Nel riquadro comandi (F1 o CTRL+MAIUSC+P) digitare e selezionare **Azure: Sign in** (Azure: Accedi). Fare quindi clic su **Copy* & Open** (Copia e apri) nella finestra popop. Incollare il codice nel browser premendo CTRL+V e fare clic sul pulsante Continua. Accedere quindi con l'account di Azure. Le informazioni relative all'account sono visibili sulla barra di stato di Visual Studio Code.
2. Nel riquadro comandi (F1 o CTRL+MAIUSC+P) digitare e selezionare **IoT: Select IoT Hub** (IoT: Seleziona hub IoT). Selezionare innanzitutto la sottoscrizione in cui si è creato l'hub IoT nell'esercitazione precedente. Scegliere quindi l'hub IoT che contiene il dispositivo IoT Edge.

    ![Elenco dei dispositivi](./media/how-to-vscode-develop-csharp-module/device-list.png)

#### <a name="set-iot-hub-connection-string"></a>Impostare la stringa di connessione dell'hub IoT
Nel riquadro comandi (F1 o CTRL+MAIUSC+P) digitare e selezionare **IoT: Set IoT Hub Connection String** (IoT: Imposta stringa di connessione hub IoT). Assicurarsi di incollare la stringa di connessione sotto i criteri **iothubowner**, come specificato in Criteri di accesso condiviso per l'hub IoT nel portale di Azure.
 
È possibile visualizzare l'elenco dei dispositivi nell'apposito strumento di esplorazione per l'hub IoT sulla barra laterale sinistra.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Avviare il runtime di IoT Edge e distribuire un modulo
Installare e avviare il runtime di Azure IoT Edge nel dispositivo. Distribuire quindi un modulo di sensore simulato che invierà i dati di telemetria all'hub IoT.
1. Nel riquadro comandi selezionare **Edge: Setup Edge** (Edge: Configura Edge) e scegliere l'ID del dispositivo IoT Edge. In alternativa, fare clic con il pulsante destro del mouse sull'ID del dispositivo Edge nell'elenco dei dispositivi e scegliere **Setup Edge** (Configura Edge).

    ![Configurare il runtime di Edge](./media/how-to-vscode-develop-csharp-module/setup-edge.png)

2. Nel riquadro comandi selezionare **Edge: Start Edge** (Edge: Avvia Edge) per avviare il runtime di Edge. È possibile visualizzare l'output corrispondente nel terminale integrato.

    ![Avviare il runtime di Edge](./media/how-to-vscode-develop-csharp-module/start-edge.png)

3. Verificare lo stato del runtime di Edge nello strumento di esplorazione di Docker. Il colore verde significa che è in esecuzione. Il runtime di IoT Edge è stato avviato correttamente.

    ![Runtime di Edge in esecuzione](./media/how-to-vscode-develop-csharp-module/edge-runtime.png)

4. Il runtime di Edge è in esecuzione e quindi il PC sta ora simulando un dispositivo Edge. Il passaggio successivo consiste nel simulare un sensore che invia costantemente messaggi al dispositivo Edge. Nel riquadro comandi digitare e selezionare **Edge: Generate Edge configuration file** (Edge: Genera file di configurazione Edge). Selezionare quindi una cartella per creare questo file. Nel file deployment.json generato sostituire il contenuto `<registry>/<image>:<tag>` con `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` e salvare il file.

    ![Modulo del sensore](./media/how-to-vscode-develop-csharp-module/sensor-module.png)

5. Selezionare **Edge: Create deployment for Edge device** (Edge: Crea distribuzione per dispositivo Edge) e scegliere l'ID del dispositivo Edge per creare una nuova distribuzione. In alternativa, è possibile fare clic con il pulsante destro del mouse sull'ID del dispositivo Edge nell'elenco dei dispositivi e scegliere **Create deployment for Edge device** (Crea distribuzione per dispositivo Edge). 

6. Nello strumento di esplorazione di Docker si dovrebbe vedere il runtime di IoT Edge in esecuzione con il sensore simulato. Fare clic con il pulsante destro del mouse sul contenitore nello strumento di esplorazione di Docker. È possibile controllare i log di Docker per ogni modulo. È inoltre possibile visualizzare i moduli nell'elenco dei dispositivi.

    ![Elenco dei moduli](./media/how-to-vscode-develop-csharp-module/module-list.png)

7. Facendo clic con il pulsante destro del mouse sull'ID del dispositivo Edge è possibile monitorare i messaggi D2C in Visual Studio Code.
8. Per arrestare il runtime di IoT Edge e il modulo del sensore, è possibile digitare e selezionare **Edge: Stop Edge** (Edge: Arresta Edge) nel riquadro comandi.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Sviluppare e distribuire un modulo C# in Visual Studio Code
Nell'esercitazione [Sviluppare un modulo C#](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module) si aggiorna, crea e pubblica l'immagine del modulo C# in Visual Studio Code e quindi si accede al portale di Azure per distribuire il modulo. Questa sezione illustra come usare Visual Studio Code per distribuire e monitorare il modulo C#.

### <a name="start-a-local-docker-registry"></a>Avviare un registro Docker locale
È possibile usare qualsiasi registro compatibile con Docker per questa esercitazione. Due servizi molto diffusi per il registro Docker disponibili sul cloud sono il [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) e [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). In questa sezione viene usato un [registro Docker locale](https://docs.docker.com/registry/deploying/), più semplice da usare a scopo di test durante la fase di sviluppo iniziale.
Nel **terminale integrato** di Visual Studio Code (CTRL+`) eseguire i comandi seguenti per avviare un registro locale.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> L'esempio precedente mostra le configurazioni del registro appropriate per il test. Un registro pronto per la produzione deve essere protetto da TLS e dovrebbe usare preferibilmente un meccanismo di controllo di accesso. Per distribuire moduli IoT Edge pronti per l'ambiente di produzione è consigliabile usare [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) o [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

### <a name="create-an-iot-edge-module-project"></a>Creare un progetto di modulo di IoT Edge
La procedura seguente illustra come creare un modulo di IoT Edge basato su .NET Core 2.0 tramite Visual Studio Code e l'estensione Azure IoT Edge. Se si è completata questa sezione nell'esercitazione precedente, è possibile ignorarla.
1. In Visual Studio Code selezionare **Visualizza** > **Terminale integrato** per aprire il terminale integrato di Visual Studio Code.
3. Nel terminale integrato immettere il comando seguente per installare o aggiornare il modello **AzureIoTEdgeModule** in dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Creare un progetto per il nuovo modulo. Questo comando crea la cartella del progetto, **FilterModule**, nella cartella di lavoro corrente:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. Selezionare  **File** > **Apri cartella**.
4. Passare alla cartella **FilterModule** e fare clic su **Seleziona cartella** per aprire il progetto in Visual Studio Code.
5. Nello strumento di esplorazione di Visual Studio Code fare clic su **Program.cs** per aprirlo. Nella parte superiore di **program.cs** includere gli spazi dei nomi seguenti.
   ```csharp
   using Microsoft.Azure.Devices.Shared;
   using System.Collections.Generic;  
   using Newtonsoft.Json;
   ```

6. Aggiungere la variabile `temperatureThreshold` alla classe **Program**. Questa variabile imposta il valore che la temperatura misurata deve superare per inviare i dati all'hub IoT. 

   ```csharp
   static int temperatureThreshold { get; set; } = 25;
   ```

7. Aggiungere le classi `MessageBody`, `Machine` e `Ambient` alla classe **Program**. Queste classi definiscono lo schema previsto per il corpo dei messaggi in arrivo.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
    ```

8. Nel metodo **Init** il codice crea e configura un oggetto **DeviceClient**. Questo oggetto consente al modulo di connettersi al runtime locale di Azure IoT Edge per inviare e ricevere messaggi. La stringa di connessione usata nel metodo **Init** viene fornita al modulo dal runtime di IoT Edge. Dopo la creazione di **DeviceClient**, il codice registra un callback per la ricezione di messaggi dall'hub di IoT Edge tramite l'endpoint **input1**. Sostituire il metodo `SetInputMessageHandlerAsync` con un nuovo metodo e associare un metodo `SetDesiredPropertyUpdateCallbackAsync` per gli aggiornamenti desiderati alle proprietà. Per apportare questa modifica, sostituire l'ultima riga del metodo **Init** con il codice seguente:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Aggiungere il metodo `onDesiredPropertiesUpdate` alla classe **Program**. Questo metodo riceve gli aggiornamenti della proprieta desiderata dal modulo gemello e aggiorna la variabile **temperatureThreshold**. Tutti i moduli hanno un modulo gemello che consente di configurare il codice in esecuzione all'interno di un modulo direttamente dal cloud.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

10. Sostituire il metodo `PipeMessage` con il metodo `FilterMessages`. Questo metodo viene chiamato ogni volta che l'hub di IoT Edge invia un messaggio al modulo. Filtra i messaggi con un valore della temperatura inferiore alla soglia relativa alla temperatura configurata tramite il modulo gemello. Aggiunge inoltre la proprietà **MessageType** al messaggio con il valore impostato su **Avviso**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. Per compilare il progetto, fare clic con il pulsante destro del mouse sul file **FilterModule.csproj** nello strumento di esplorazioni e scegliere **Build IoT Edge module** (Compila il modulo di IoT Edge). Questo processo compila il modulo ed esporta il file binario e le relative dipendenze in una cartella usata per creare un'immagine Docker. 

    ![Compilare il modulo](./media/how-to-vscode-develop-csharp-module/build-module.png)

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Creare un'immagine Docker e pubblicarla nel registro

1. Nello strumento di esplorazione di Visual Studio Code espandere la cartella **Docker**. Espandere quindi la cartella per la piattaforma del contenitore, ovvero **linux-x64** o **windows-nano**.
2. Fare clic con il pulsante destro del mouse sul file **Dockerfile** e scegliere **Build IoT Edge module Docker image** (Compila l'immagine Docker per il modulo di IoT Edge). 

    ![Compilare l'immagine Docker](./media/how-to-vscode-develop-csharp-module/build-docker-image.png)

3. Nella finestra **Selezione cartella** passare o immettere `./bin/Debug/netcoreapp2.0/publish`. Fare clic su **Select Folder as EXE_DIR** (Selezionare la cartella EXE_DIR).
4. Nella casella di testo popup nella parte superiore della finestra di Visual Studio Code immettere il nome dell'immagine. Ad esempio: `<your container registry address>/filtermodule:latest`. Se si esegue la distribuzione nel registro locale, il nome dovrebbe essere `localhost:5000/filtermodule:latest`.
5. Eseguire il push dell'immagine nel repository di Docker. Usare il comando **Edge: Push IoT Edge module Docker image** (Edge: Esegui il push dell'immagine Docker per il modulo IoT Edge) e immettere l'URL dell'immagine nella casella di testo popup nella parte superiore della finestra di Visual Studio Code. Usare lo stesso URL di immagine del passaggio precedente. Controllare il log della console e verificare che sia stato eseguito il push dell'immagine.

    ![Eseguire il push dell'immagine Docker](./media/how-to-vscode-develop-csharp-module/push-image.png) ![Immagine Docker di cui è stato eseguito il push](./media/how-to-vscode-develop-csharp-module/pushed-image.png)

### <a name="deploy-your-iot-edge-modules"></a>Distribuire i moduli IoT Edge

1. Aprire il file `deployment.json` e sostituire la sezione **modules** con il contenuto seguente:
    ```json
    "tempSensor": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
            "createOptions": ""
        }
    },
    "filtermodule": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "localhost:5000/filtermodule:latest",
            "createOptions": ""
        }
    }
    ```

2. Sostituire la sezione **routes** con il contenuto seguente:
    ```json
    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
    ```
   > [!NOTE]
   > Le regole dichiarative nel runtime definiscono la destinazione del flusso di messaggi. In questa esercitazione sono necessarie due route. La prima route trasporta i messaggi dal sensore della temperatura al modulo del filtro tramite l'endpoint "input1", ovvero l'endpoint configurato con il gestore FilterMessages. La seconda route trasporta i messaggi dal modulo del filtro all'hub IoT. In questa route, upstream è una destinazione speciale che indica all'hub Edge di inviare messaggi all'hub IoT.

3. Salvare questo file.
4. Nel riquadro comandi selezionare **Edge: Create deployment for Edge device** (Edge: Crea distribuzione per dispositivo Edge). Selezionare quindi l'ID del dispositivo IoT Edge per creare una distribuzione. In alternativa, fare clic con il pulsante destro del mouse sull'elenco dei dispositivi e scegliere **Create deployment for Edge device** (Crea distribuzione per dispositivo Edge).

    ![Creare una distribuzione](./media/how-to-vscode-develop-csharp-module/create-deployment.png)

5. Selezionare il file `deployment.json` aggiornato. Nella finestra di output è possibile visualizzare l'output corrispondente per la distribuzione.

    ![Distribuzione completata](./media/how-to-vscode-develop-csharp-module/deployment-succeeded.png)

6. Avviare il runtime di Edge nel riquadro comandi selezionando **Edge: Start Edge** (Edge: Avvia Edge).
7. Nello strumento di esplorazione di Docker è possibile vedere il runtime di IoT Edge in esecuzione con il sensore simulato e il modulo del filtro.

    ![Soluzione IoT Edge in esecuzione](./media/how-to-vscode-develop-csharp-module/solution-running.png)

8. Facendo clic con il pulsante destro del mouse sull'ID del dispositivo Edge è possibile monitorare i messaggi D2C in Visual Studio Code.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è creato un modulo IoT Edge e quindi si è distribuito tale modulo al dispositivo IoT Edge in Visual Studio Code. Per informazioni su altri scenari durante lo sviluppo di Azure IoT Edge in Visual Studio Code, è possibile continuare con l'esercitazione seguente.

> [!div class="nextstepaction"]
> [Eseguire il debug del modulo C# in Visual Studio Code](how-to-vscode-debug-csharp-module.md)
