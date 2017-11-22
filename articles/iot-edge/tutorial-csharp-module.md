---
title: Modulo C# per Azure IoT Edge | Microsoft Docs
description: Creare un modulo per IoT Edge con codice C# e distribuirlo in un dispositivo perimetrale
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb7674d8c292e7d571a94ac4625b0858a90704b3
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Sviluppare e distribuire un modulo C# per IoT Edge in un dispositivo simulato - Anteprima

È possibile usare i moduli di IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra in modo dettagliato la creazione e la distribuzione di un modulo di IoT Edge che filtra i dati dei sensori nel dispositivo di IoT Edge simulato creato nelle esercitazioni sulla distribuzione di Azure IoT Edge in un dispositivo simulato su [Windows][lnk-tutorial1-win] o [Linux][lnk-tutorial1-lin]. In questa esercitazione si apprenderà come:    

> [!div class="checklist"]
> * Usare Visual Studio Code per creare un modulo di IoT Edge basato su .NET Core 2.0
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla nel registro 
> * Distribuire il modulo nel dispositivo di IoT Edge
> * Visualizzare i dati generati


Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo e invia messaggi upstream solo quando la temperatura è superiore a una soglia specificata. 

## <a name="prerequisites"></a>Prerequisiti

* Il dispositivo Azure IoT Edge creato nella guida introduttiva o nell'esercitazione precedente.
* La stringa di connessione per l'hub IoT a cui si connette il dispositivo di IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). È possibile installare l'estensione dal pannello Estensioni in Visual Studio Code.
* [Estensione C# per Visual Studio Code (con tecnologia OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). È possibile installare l'estensione dal pannello Estensioni in Visual Studio Code.
* Estensione Azure IoT Edge per Visual Studio Code
* [Docker](https://docs.docker.com/engine/installation/). La Community Edition (CE) per la piattaforma è sufficiente per questa esercitazione. Assicurarsi di installarla nel computer che esegue Visual Studio Code.
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="choose-or-sign-up-for-a-docker-registry"></a>Scegliere o iscriversi a un registro Docker
In questa esercitazione viene usata l'estensione Azure IoT Edge per Visual Studio Code per creare un modulo e un'[immagine Docker](https://docs.docker.com/glossary/?term=image). Eseguire quindi il push di tale immagine Docker in un [repository di Docker](https://docs.docker.com/glossary/?term=repository) ospitato in un [registro Docker](https://docs.docker.com/glossary/?term=registry). Distribuire infine l'immagine Docker in un pacchetto come [contenitore di Docker](https://docs.docker.com/glossary/?term=container) dal registro nel dispositivo di IoT Edge.  

È possibile usare qualsiasi registro compatibile con Docker per questa esercitazione. Due servizi molto diffusi per il registro Docker disponibili sul cloud sono il **Registro contenitori di Azure** e **Docker Hub**:

- Il [Registro contenitori di Azure](https://docs.microsoft.com/en-us/azure/container-registry/) è disponibile con una [sottoscrizione a pagamento](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Per questa esercitazione è sufficiente la sottoscrizione **Basic**. 

- [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) offre un repository privato gratuito se ci si iscrive per ottenere un ID Docker (gratuito). 
    1. Per iscriversi e ottenere un ID Docker, seguire le istruzioni disponibili in [Register for a Docker ID](https://docs.docker.com/docker-id/#register-for-a-docker-id) (Effettuare la registrazione per ottenere un ID Docker) nel sito Docker. 

    2. Per creare un repository di Docker privato, seguire le istruzioni disponibili in [Creating a new repository on Docker Hub](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) (Creazione di un nuovo repository in Docker Hub) nel sito Docker.

In questa esercitazione, dove appropriato, verranno forniti i comandi per il Registro contenitori di Azure e per Docker Hub.

## <a name="create-an-iot-edge-module-project"></a>Creare un progetto di modulo di IoT Edge
La procedura seguente illustra come creare un modulo di IoT Edge basato su .NET Core 2.0 tramite Visual Studio Code e l'estensione Azure IoT Edge.
1. Aprire Visual Studio Code.
2. Usare il comando di menu **Visualizza | Terminale integrato** per aprire il terminale integrato di Visual Studio Code.
3. Nel terminale integrato immettere il comando seguente per installare o aggiornare il modello **AzureIoTEdgeModule** in dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Nel terminale integrato immettere il comando seguente per creare un progetto per il nuovo modulo:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```

    >[!NOTE]
    > Questo comando crea la cartella del progetto, **FilterModule**, nella cartella di lavoro corrente. Se si vuole creare la cartella in un'altra posizione, cambiare le directory prima di eseguire il comando.
 
3. Usare il comando di menu **File | Apri cartella**, passare alla cartella **FilterModule** e fare clic su **Seleziona cartella** per aprire il progetto in Visual Studio Code.
4. Nello strumento di esplorazione di Visual Studio Code fare clic su **Program.cs** per aprirlo.
5. Aggiungere il campo seguente alla classe **Program**.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Aggiungere le classi seguenti alla classe **Program**. Queste classi definiscono lo schema previsto per il corpo dei messaggi in arrivo.

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

1. Nel metodo **Init** il codice crea e configura un oggetto **DeviceClient**. Questo oggetto consente al modulo di connettersi al runtime locale di Azure IoT Edge per inviare e ricevere messaggi. Il parametro della stringa di connessione usato nel metodo **Init** viene fornito al modulo dal runtime di IoT Edge nella variabile di ambiente **EdgeHubConnectionString**. Dopo la creazione di **DeviceClient**, il codice registra un callback per la ricezione di messaggi dall'hub di IoT Edge tramite l'endpoint **input1**. Sostituire il metodo usato come callback per la gestione dei messaggi con un nuovo metodo e associare un callback per gli aggiornamenti desiderati alle proprietà nel modulo gemello. Per apportare questa modifica, sostituire l'ultima riga del metodo **Init** con il codice seguente:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

1. Aggiungere il metodo seguente alla classe **Program** per aggiornare il campo **temperatureThreshold** in base alle proprietà desiderate inviate del servizio back-end tramite il modulo gemello. Tutti i moduli hanno il rispettivo modulo gemello. Un modulo gemello consente a un servizio back-end di configurare il codice in esecuzione all'interno di un modulo.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"].exists())
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

1. Sostituire il metodo **PipeMessage** con il metodo seguente. Questo metodo viene chiamato ogni volta che l'hub di IoT Edge invia un messaggio al modulo. Filtra i messaggi in base al valore della temperatura disponibile nel corpo del messaggio e alla soglia relativa alla temperatura configurata tramite il modulo gemello.

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

11. Compilare il progetto. Usare il comando di menu **Visualizza | Finestra di esplorazione** per aprire lo strumento di esplorazione di Visual Studio Code. Nello strumento di esplorazione fare clic con il pulsante destro del mouse sul file **FilterModule.csproj** e scegliere **Build IoT Edge module** (Compila il modulo di IoT Edge) per compilare il modulo ed esportare il file binario e le rispettive dipendenze in una cartella da cui viene creata l'immagine di Docker nel passaggio successivo.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Creare un'immagine Docker e pubblicarla nel registro

1. Compilare l'immagine Docker.
    1. Nello strumento di esplorazione di Visual Studio Code fare clic sulla cartella **Docker** per aprirla. Selezionare quindi la cartella per la piattaforma del contenitore, ovvero **linux-x64** o **windows-nano**. 
    2. Fare clic con il pulsante destro del mouse sul file **Dockerfile** e scegliere **Build IoT Edge module Docker image** (Compila l'immagine Docker per il modulo di IoT Edge). 
    3. Nella casella **Selezione cartella** passare o immettere `./bin/Debug/netcoreapp2.0/publish`. Fare clic su **Select Folder as EXE_DIR** (Selezionare la cartella EXE_DIR).
    4. Nella casella di testo popup nella parte superiore della finestra di Visual Studio Code immettere il nome dell'immagine. Ad esempio:`<docker registry address>/filtermodule:latest`, dove *docker registry address* è l'ID Docker se si usa Docker Hub o è simile a `<your registry name>.azurecr.io`, se si usa il Registro contenitori di Azure.
 
4. Accedere a Docker. Nel terminale integrato immettere il comando seguente: 

    - Docker Hub (immettere le credenziali quando richiesto):
        
        ```csh/sh
        docker login
        ```

    - Per il Registro contenitori di Azure:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Per trovare il nome utente, la password e il server di accesso da usare in questo comando, passare al [portale di Azure] (https://portal.azure.com). Da **Tutte le risorse** fare clic sul riquadro per il Registro contenitori di Azure per aprire le rispettive proprietà, quindi fare clic su **Chiavi di accesso**. Copiare i valori nei campi **Nome utente**, **Password** e **Server di accesso**. Il server di accesso deve avere il formato seguente: `<your registry name>.azurecr.io`.

3. Eseguire il push dell'immagine nel repository di Docker. Usare il comando di menu **Visualizza | Riquadro comandi | Edge: Push IoT Edge module Docker image** (Edge: Esegui il push dell'immagine Docker per il modulo di IoT Edge) e immettere il nome dell'immagine nella casella di testo popup nella parte superiore della finestra di Visual Studio Code. Usare lo stesso nome di immagine usato nel Passaggio 1.c.

## <a name="add-registry-credentials-to-edge-runtime-on-your-edge-device"></a>Aggiungere le credenziali del registro al runtime di Edge nel dispositivo perimetrale
Aggiungere le credenziali per il registro al runtime di Edge nel computer in cui si esegue il dispositivo perimetrale, in modo da consentire al runtime l'accesso per il pull del contenitore. 

- Per Windows, eseguire il comando seguente:
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- Per Linux, eseguire il comando seguente:
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>Eseguire la soluzione

1. Nel [portale di Azure](https://portal.azure.com) passare all'hub IoT.
2. Passare a **IoT Edge (anteprima)** e selezionare il dispositivo di IoT Edge.
3. Selezionare **Set Modules** (Configura i moduli). 
2. Aggiungere il modulo **tempSensor**. Questo passaggio è necessario solo se non è stato distribuito in precedenza il modulo **tempSensor** nel dispositivo di IoT Edge.
    1. Selezionare **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `tempSensor`.
    3. Nel campo **URI immagine** immettere `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Lasciare invariate le altre impostazioni e fare clic su **Salva**.
9. Aggiungere il valore **filtermodule**.
    1. Selezionare di nuovo **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `filtermodule`.
    3. Nel campo **Immagine** immettere l'indirizzo dell'immagine, ad esempio `<docker registry address>/filtermodule:latest`.
    4. Selezionare la casella **Edit module twin** (Modifica il modulo gemello).
    5. Sostituire il codice JSON nella casella di testo per il modulo gemello con il codice JSON seguente: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Fare clic su **Salva**.
12. Fare clic su **Avanti**.
13. Nel passaggio **Specify Routes** (Specificare le route) copiare il codice JSON seguente nella casella di testo. I moduli pubblicano tutti i messaggi nel runtime di Edge. Le regole dichiarative nel runtime definiscono la destinazione del flusso di messaggi. In questa esercitazione sono necessarie due route. La prima route trasporta i messaggi dal sensore relativo alla temperatura al modulo di filtro tramite l'endpoint "input1", ovvero l'endpoint configurato con il gestore **FilterMessages**. La seconda route trasporta i messaggi dal modulo di filtro all'hub IoT. In questa route `upstream` è una destinazione speciale che indica all'hub di Edge di inviare messaggi all'hub IoT. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Fare clic su **Avanti**.
5. Nel passaggio **Review Template** (Verifica il modello) fare clic su **Submit** (Invia). 
6. Tornare alla pagina dei dettagli del dispositivo di IoT Edge e fare clic su **Aggiorna**. Dovrebbe essere visualizzato il nuovo **filtermodule** in esecuzione insieme al modulo **tempSensor** e al **runtime di IoT Edge**. 

## <a name="view-generated-data"></a>Visualizzare i dati generati

Per monitorare i messaggi da dispositivo a cloud inviati dal dispositivo di IoT Edge all'hub IoT:
1. Configurare l'estensione Azure IoT Toolkit con la stringa di connessione per l'hub IoT: 
    1. Usare il comando di menu **Visualizza | Finestra di esplorazione** per aprire lo strumento di esplorazione di Visual Studio Code. 
    3. Nello strumento di esplorazione fare clic su **IOT HUB DEVICES** (DISPOSITIVI DELL'HUB IOT) e quindi fare clic su **...**. Fare clic su **Set IoT Hub Connection String** (Configura la stringa di connessione dell'hub IoT) e immettere la stringa di connessione per l'hub IoT a cui si connette il dispositivo di IoT Edge nella finestra popup. 

        Per trovare la stringa di connessione, fare clic sul riquadro dell'hub IoT nel portale di Azure e quindi fare clic su **Criteri di accesso condivisi**. In **Criteri di accesso condivisi** fare clic sul criterio **iothubowner** e copiare la stringa di connessione dell'hub IoT nella finestra **iothubowner**.   

1. Per monitorare i dati in arrivo nell'hub IoT, usare il comando di menu **Visualizza | Riquadro comandi | IoT: Start monitoring D2C message** (IoT: Avvia il monitoraggio del messaggio D2C). 
2. Per interrompere il monitoraggio dei dati, usare il comando di menu **Visualizza | Riquadro comandi | IoT: Stop monitoring D2C message** (IoT: Interrompi il monitoraggio del messaggio D2C). 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge contenente il codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. È possibile continuare con una delle esercitazioni seguenti per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Distribuire Funzione di Azure come modulo](tutorial-deploy-function.md)
> [Distribuire Analisi di flusso di Azure come modulo](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
