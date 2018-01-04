---
title: Modulo C# per Azure IoT Edge | Microsoft Docs
description: Creare un modulo per IoT Edge con codice C# e distribuirlo in un dispositivo perimetrale
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: bf57fa11c63930c594c63043ab4b695f586d9e1b
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Sviluppare e distribuire un modulo C# per IoT Edge in un dispositivo simulato - Anteprima

È possibile usare i moduli di IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore. Verrà utilizzato il dispositivo IoT Edge simulato che è stato creato nelle esercitazioni Distribuire Azure IoT Edge in un dispositivo simulato in [Windows][lnk-tutorial1-win] e [Linux][lnk-tutorial1-lin]. In questa esercitazione si apprenderà come:    

> [!div class="checklist"]
> * Usare Visual Studio Code per creare un modulo di IoT Edge basato su .NET Core 2.0
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla nel registro 
> * Distribuire il modulo nel dispositivo di IoT Edge
> * Visualizzare i dati generati


Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo quando la temperatura è superiore a una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud. 

## <a name="prerequisites"></a>Prerequisiti

* Il dispositivo Azure IoT Edge creato nella guida introduttiva o nella prima esercitazione.
* La chiave primaria della stringa di connessione del dispositivo IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/) nello stesso computer con Visual Studio Code. La Community Edition (CE) è sufficiente per questa esercitazione. 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Creare un registro di contenitori
In questa esercitazione viene usata l'estensione Azure IoT Edge per Visual Studio Code per creare un modulo e un'**immagine del contenitore** dai file. Eseguire quindi il push dell'immagine in un **registro** che archivia e gestisce le immagini. Distribuire infine l'immagine dal registro nel dispositivo di IoT Edge.  

È possibile usare qualsiasi registro compatibile con Docker per questa esercitazione. Due servizi molto diffusi per il registro Docker disponibili sul cloud sono il [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) e [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Questa esercitazione usa il registro contenitori di Azure. 

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Contenitori** > **Registro di sistema del contenitore di Azure**.
2. Assegnare un nome al registro di sistema, scegliere una sottoscrizione e un gruppo di risorse e impostare lo SKU su **Basic**. 
3. Selezionare **Create**.
4. Dopo aver creato il registro di sistema del contenitore, accedervi e selezionare **Chiavi di accesso**. 
5. Impostare **Utente amministratore** su **Abilita**.
6. Copiare i valori nei campi **Server di accesso**, **Nome utente** e **Password**. Questi valori verranno usati più avanti nell'esercitazione. 

## <a name="create-an-iot-edge-module-project"></a>Creare un progetto di modulo di IoT Edge
La procedura seguente illustra come creare un modulo di IoT Edge basato su .NET Core 2.0 tramite Visual Studio Code e l'estensione Azure IoT Edge.
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

   ![Aprire Program.cs][1]

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

   ![Compilare il modulo di IoT Edge][2]

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Creare un'immagine Docker e pubblicarla nel registro

1. Nello strumento di esplorazione di Visual Studio Code espandere la cartella **Docker**. Espandere quindi la cartella per la piattaforma del contenitore, ovvero **linux-x64** o **windows-nano**.

   ![Selezionare la piattaforma del contenitore Docker][3]

2. Fare clic con il pulsante destro del mouse sul file **Dockerfile** e scegliere **Build IoT Edge module Docker image** (Compila l'immagine Docker per il modulo di IoT Edge). 
3. Nella finestra **Selezione cartella** passare o immettere `./bin/Debug/netcoreapp2.0/publish`. Fare clic su **Select Folder as EXE_DIR** (Selezionare la cartella EXE_DIR).
4. Nella casella di testo popup nella parte superiore della finestra di Visual Studio Code immettere il nome dell'immagine. Ad esempio: `<your container registry address>/filtermodule:latest`. L'indirizzo del registro di sistema del contenitore è lo stesso del server di accesso copiato dal registro di sistema. Deve essere nel formato `<your container registry name>.azurecr.io`.
5. Accedere a Docker immettendo il comando seguente nel terminale integrato di Visual Studio Code: 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Usare il nome utente, la password e il server di accesso copiati quando è stato creato il registro di sistema del contenitore di Azure.

3. Eseguire il push dell'immagine nel repository di Docker. Selezionare **Visualizza** > **Riquadro comandi** e cercare il comando di menu **Edge: Push IoT Edge module Docker image**(Edge: esegui il push dell'immagine Docker per il modulo di IoT Edge). Nella casella di testo popup nella parte superiore della finestra di Visual Studio Code immettere il nome dell'immagine. Utilizzare lo stesso nome di immagine utilizzata nel passaggio 4.

## <a name="add-registry-credentials-to-edge-runtime"></a>Aggiungere le credenziali del registro al runtime di Edge
Aggiungere le credenziali per il registro al runtime di Edge nel computer in cui si esegue il dispositivo perimetrale di Edge, in modo da consentire al runtime l'accesso per il pull del contenitore. 

- Per Windows, eseguire il comando seguente:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Per Linux, eseguire il comando seguente:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Eseguire la soluzione

1. Nel [portale di Azure](https://portal.azure.com) passare all'hub IoT.
2. Passare a **IoT Edge (preview)** (IoT Edge - anteprima) e selezionare il dispositivo di IoT Edge.
3. Selezionare **Set Modules** (Configura i moduli). 
2. Verificare che il modulo **tempSensor** venga inserito automaticamente. In caso contrario, seguire questa procedura:
    1. Selezionare **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `tempSensor`.
    3. Nel campo **URI immagine** immettere `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Lasciare invariate le altre impostazioni e fare clic su **Salva**.
9. Aggiungere il modulo **filterModule** creato nella sezione precedente. 
    1. Selezionare **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `filterModule`.
    3. Nel campo **URI immagine** immettere l'indirizzo dell'immagine, ad esempio `<your container registry address>/filtermodule:latest`.
    4. Selezionare la casella **Abilita** per modificare il modulo gemello. 
    5. Sostituire il codice JSON nella casella di testo per il modulo gemello con il codice JSON seguente: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Fare clic su **Save**.
12. Fare clic su **Avanti**.
13. Nel passaggio **Specify Routes** (Specifica route) copiare il codice JSON seguente nella casella di testo. I moduli pubblicano tutti i messaggi nel runtime di Edge. Le regole dichiarative nel runtime definiscono la destinazione del flusso di messaggi. In questa esercitazione sono necessarie due route. La prima route trasporta i messaggi dal sensore della temperatura al modulo di filtro tramite l'endpoint "input1", ovvero l'endpoint configurato con il gestore **FilterMessages**. La seconda route trasporta i messaggi dal modulo del filtro all'hub IoT. In questa route `upstream` è una destinazione speciale che indica all'hub di Edge di inviare messaggi all'hub IoT. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Fare clic su **Avanti**.
5. Nel passaggio **Review Template** (Verifica il modello) fare clic su **Submit** (Invia). 
6. Tornare alla pagina dei dettagli del dispositivo di IoT Edge e fare clic su **Aggiorna**. Dovrebbe essere visualizzato il nuovo **filtermodule** in esecuzione insieme al modulo **tempSensor** e al **runtime di IoT Edge**. 

## <a name="view-generated-data"></a>Visualizzare i dati generati

Per monitorare i messaggi da dispositivo a cloud inviati dal dispositivo di IoT Edge all'hub IoT:
1. Configurare l'estensione Azure IoT Toolkit con la stringa di connessione per l'hub IoT: 
    1. Aprire lo strumento di esplorazione di Visual Studio Code selezionando **Visualizza** > **Explorer**. 
    3. Nello strumento di esplorazione fare clic su **IOT HUB DEVICES** (DISPOSITIVI DELL'HUB IOT) e quindi fare clic su **...**. Fare clic su **Set IoT Hub Connection String** (Configura la stringa di connessione dell'hub IoT) e immettere la stringa di connessione per l'hub IoT a cui si connette il dispositivo di IoT Edge nella finestra popup. 

        Per trovare la stringa di connessione, fare clic sul riquadro dell'hub IoT nel portale di Azure e quindi fare clic su **Criteri di accesso condiviso**. In **Criteri di accesso condiviso** fare clic sul criterio **iothubowner** e copiare la stringa di connessione dell'hub IoT nella finestra **iothubowner**.   

1. Per monitorare i dati in arrivo nell'hub IoT, usare **Visualizza** > **Riquadro comandi** e cercare il comando di menu **IoT: Start monitoring D2C message** (IoT: avvia il monitoraggio del messaggio D2C). 
2. Per interrompere il monitoraggio dei dati, usare il comando di menu **IoT: Stop monitoring D2C message** (IoT: interrompi il monitoraggio del messaggio D2C). 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge contenente il codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. È possibile continuare con una delle esercitazioni seguenti per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Distribuire Funzione di Azure come modulo](tutorial-deploy-function.md)
> [Distribuire Analisi di flusso di Azure come modulo](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
