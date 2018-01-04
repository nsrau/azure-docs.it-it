---
title: Usare codice di Visual Studio per sviluppare un modulo in c# con bordo IoT di Azure | Documenti Microsoft
description: Sviluppare e distribuire un modulo in c# con bordo IoT di Azure in Visual Studio Code senza cambio di contesto
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 269f77e5015175e45e0078926ef06699811889a4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="use-visual-studio-code-to-develop-c-module-with-azure-iot-edge"></a>Usare codice di Visual Studio per sviluppare c# modulo Azure IoT Edge
In questo articolo vengono fornite istruzioni dettagliate per l'utilizzo di [codice di Visual Studio](https://code.visualstudio.com/) come lo strumento di sviluppo principali per sviluppare e distribuire i moduli di IoT Edge. 

## <a name="prerequisites"></a>Prerequisiti
In questa esercitazione si presuppone che si utilizza un computer o macchina virtuale che esegue Windows o Linux come computer di sviluppo. Il dispositivo perimetrale IoT può essere un altro dispositivo fisico oppure è possibile simulare il dispositivo perimetrale IoT nel computer di sviluppo.

Assicurarsi di avere completato le esercitazioni seguenti prima di iniziare questa Guida.
- Distribuire Azure IoT Edge su un dispositivo simulato in [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) o [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Sviluppare e distribuire un modulo di c# IoT bordo per il dispositivo simulato](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

Di seguito è riportato un elenco di controllo che visualizza gli elementi, che è necessario dopo aver completato le esercitazioni precedenti.

- [Visual Studio Code](https://code.visualstudio.com/). 
- [Estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7](https://www.python.org/downloads/)
- [Script del controllo IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Modello AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Un hub IoT attivo con almeno un dispositivo IoT Edge.

È inoltre consigliabile per installare [supporto Docker per il codice di Visual Studio](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) per gestire meglio le immagini dei moduli e i contenitori.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Distribuire un modulo di Azure IoT Edge nel codice di Visual Studio

### <a name="list-your-iot-hub-devices"></a>Elenco dei dispositivi di hub IoT
Esistono due modi per elencare i dispositivi di hub IoT nel codice di Visual Studio. È possibile scegliere di continuare in entrambi i casi.

#### <a name="sign-in-your-azure-account-in-vscode-and-choose-your-iot-hub"></a>L'accesso all'account Azure in VSCode e scegliere l'hub IoT
1. Nel riquadro comandi (F1 o Ctrl + MAIUSC + P), digitare e selezionare **Azure: Accedi**. Quindi fare clic su  **copia* & Open** nella finestra a comparsa. Incollare il codice di (Ctrl + V) nel browser e fare clic sul pulsante Continua. Quindi accedere con l'account di Azure. È possibile visualizzare le informazioni dell'account nella barra di stato di Visual Studio Code.
2. Nel riquadro comandi (F1 o Ctrl + MAIUSC + P), digitare e selezionare **IoT: selezionare l'IoT Hub**. È innanzitutto necessario selezionare la sottoscrizione in cui l'hub IoT è creato nell'esercitazione precedente. Scegliere l'hub IoT che contiene il dispositivo perimetrale IoT.


#### <a name="set-iot-hub-connection-string"></a>Impostare la stringa di connessione hub IoT
1. Nel riquadro comandi (F1 o Ctrl + MAIUSC + P), digitare e selezionare **IoT: stringa di connessione Hub IoT impostare**. Assicurarsi incollare la stringa di connessione in base ai criteri **iothubowner** (è possibile trovarlo in Criteri di accesso condiviso dell'hub di IoT nel portale di Azure).
 

È possibile visualizzare l'elenco dei dispositivi in IoT Hub dispositivi Esplora nella barra laterale sinistra.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Avviare il runtime IoT Edge e distribuire un modulo
Installare e avviare il runtime di Azure IoT Edge nel dispositivo. E distribuire un modulo sensore simulati che invierà i dati di telemetria per l'IoT Hub.
1. Nel riquadro comandi, selezionare **bordo: il programma di installazione Edge** e scegliere il contorno IoT ID dispositivo. O l'ID dispositivo perimetrale nell'elenco dei dispositivi e scegliere **bordo installazione**.
2. Nel riquadro comandi, selezionare **Edge: Edge Start** per avviare il runtime di bordo. È possibile visualizzare l'output corrispondente nel terminal integrata.
3. Verificare lo stato di runtime del bordo in Esplora risorse di Docker. Verde che significa che è in esecuzione. Il runtime di IoT Edge è stato avviato correttamente.
4. Ora è in esecuzione il runtime di bordo, ovvero il PC simula un dispositivo periferico. Passaggio successivo consiste nel simulare un sensorthing che continua a inviare messaggi al dispositivo di bordo. Nel riquadro comandi, digitare e selezionare **bordo: il file di configurazione di generare Edge**. Selezionare una cartella per creare questo file. Nel file deployment.json generato, sostituire la riga "<registry>/<image>:<tag>" con `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
5. Selezionare **Edge: creare una distribuzione per il dispositivo perimetrale** e scegliere l'ID dispositivo perimetrale per creare una nuova distribuzione. Oppure l'ID del dispositivo nell'elenco dei dispositivi bordo destro e selezionare **creare distribuzione per il dispositivo perimetrale**. 
6. Verrà visualizzato il contorno IoT avviati in Esplora risorse di Docker con il sensore simulato. Fare clic sul contenitore in soluzioni di Docker. È possibile controllare i registri di docker per ogni modulo.
7. L'ID dispositivo bordo destro ed è possibile monitorare i messaggi D2C in Visual Studio Code.
8. Per arrestare il runtime IoT bordo e il modulo sensore, è possibile digitare e selezionare **Edge: arrestare Edge** nel riquadro comandi.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Sviluppare e distribuire un modulo di c# in Visual Studio Code
Nell'esercitazione [sviluppare un modulo c#](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module), aggiornare, compilazione e pubblicare l'immagine del modulo nel codice di Visual Studio e quindi visitare il portale Azure per distribuire un modulo in c#. In questa sezione viene illustrato come utilizzare il codice di Visual Studio per distribuire e monitorare il modulo di c#.

### <a name="start-a-local-docker-registry"></a>Avviare un registro di sistema locale docker
È possibile usare qualsiasi registro compatibile con Docker per questa esercitazione. Due servizi molto diffusi per il registro Docker disponibili sul cloud sono il [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) e [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Questa sezione viene usato un [Registro di sistema locale Docker](https://docs.docker.com/registry/deploying/), che è più semplice per scopi di test durante lo sviluppo delle primo.
In Visual Studio Code **terminal integrata**(Ctrl + '), esecuzione seguenti comandi per avviare un registro di sistema locale.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Esempio precedente mostra le configurazioni del Registro di sistema solo appropriate per il test. Un registro di sistema di ambiente di produzione deve essere protetto da TLS e dovrebbe utilizzare un meccanismo di controllo di accesso. Si consiglia di usare [Registro di sistema di Azure contenitore](https://docs.microsoft.com/azure/container-registry/) o [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) per distribuire i moduli di IoT bordo di ambiente di produzione.

### <a name="create-an-iot-edge-module-project"></a>Creare un progetto di modulo di IoT Edge
La procedura seguente illustra come creare un modulo di IoT Edge basato su .NET Core 2.0 tramite Visual Studio Code e l'estensione Azure IoT Edge. Se è stata completata in questa sezione nell'esercitazione precedente, è possibile ignorare in modo sicuro in questa sezione.
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
5. Nello strumento di esplorazione di Visual Studio Code fare clic su **Program.cs** per aprirlo.
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


### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Creare un'immagine Docker e pubblicarla nel registro

1. Nello strumento di esplorazione di Visual Studio Code espandere la cartella **Docker**. Espandere quindi la cartella per la piattaforma del contenitore, ovvero **linux-x64** o **windows-nano**.
2. Fare clic con il pulsante destro del mouse sul file **Dockerfile** e scegliere **Build IoT Edge module Docker image** (Compila l'immagine Docker per il modulo di IoT Edge). 
3. Nella finestra **Selezione cartella** passare o immettere `./bin/Debug/netcoreapp2.0/publish`. Fare clic su **Select Folder as EXE_DIR** (Selezionare la cartella EXE_DIR).
4. Nella casella di testo popup nella parte superiore della finestra di Visual Studio Code immettere il nome dell'immagine. Ad esempio: `<your container registry address>/filtermodule:latest`. Se si distribuisce nel Registro di sistema locale, deve essere `localhost:5000/filtermodule:latest`.
5. Eseguire il push dell'immagine nel repository di Docker. Utilizzare il **Edge: immagine di Docker modulo Push IoT Edge** comandi e immettere l'URL dell'immagine nella casella di testo popup nella parte superiore della finestra del codice di Visual Studio. Utilizzare lo stesso URL di immagini utilizzato nel precedente passaggio.

### <a name="deploy-your-iot-edge-modules"></a>Distribuire i moduli di IoT Edge

1. Aprire il `deployment.json` file, sostituire **moduli** sezione con contenuto:
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

2. Sostituire il **route** sezione con contenuto:
    ```json
    {
        "routes": {
            "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        }
    }
    ```
   > [!NOTE]
   > Le regole dichiarative nel runtime definiscono la destinazione del flusso di messaggi. In questa esercitazione sono necessarie due route. La prima route trasporta i messaggi del sensore di temperatura del modulo di filtro tramite l'endpoint "input1", che è l'endpoint configurato con il gestore FilterMessages. La seconda route trasporta i messaggi dal modulo del filtro all'hub IoT. In questa route, upstream è una destinazione speciale che indica l'Hub di Edge di inviare messaggi all'IoT Hub.

3. Salvare il file.
4. Nel riquadro comandi, selezionare **Edge: creare una distribuzione per il dispositivo perimetrale**. Selezionare quindi l'ID del dispositivo IoT Edge per creare una distribuzione. O l'ID del dispositivo nell'elenco dei dispositivi e scegliere **creare distribuzione per il dispositivo perimetrale**.
5. Selezionare il `deployment.json` è aggiornato. Nella finestra di output, è possibile visualizzare gli output corrispondenti per la distribuzione.
6. Avviare il runtime di Edge nel riquadro comandi. **Edge: Bordo iniziale**
7. È possibile visualizzare il bordo di IoT runtime avviati in Esplora risorse di Docker con il modulo di filtro e un sensore simulato.
8. L'ID dispositivo bordo destro ed è possibile monitorare i messaggi D2C in Visual Studio Code.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, un modulo IoT bordo creato e distribuito dispositivo IoT Edge in Visual Studio Code. È possibile continuare a una delle esercitazioni seguenti per ulteriori informazioni sugli altri scenari durante lo sviluppo di IoT Edge di Azure in Visual Studio Code.

> [!div class="nextstepaction"]
> [Eseguire il debug modulo c# in Visual Studio Code](how-to-vscode-debug-csharp-module.md)
