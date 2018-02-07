---
title: Usare Visual Studio Code per sviluppare e distribuire Funzioni di Azure con Azure IoT Edge | Microsoft Docs
description: Sviluppare e distribuire Funzioni di Azure in C# con Azure IoT Edge in Visual Studio Code senza cambio di contesto
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 219474a4577a76f5ceb9a9efaa3c349d633de047
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>Usare Visual Studio Code per sviluppare e distribuire Funzioni di Azure con Azure IoT Edge

Questo articolo fornisce istruzioni dettagliate per usare [Visual Studio Code](https://code.visualstudio.com/) come strumento di sviluppo principale per sviluppare e distribuire Funzioni di Azure in IoT Edge. 

## <a name="prerequisites"></a>Prerequisiti
Questa esercitazione presuppone che si usi un computer o una macchina virtuale Windows o Linux come computer di sviluppo. Il dispositivo IoT Edge può essere un altro dispositivo fisico oppure è possibile simulare il dispositivo IoT Edge nel computer di sviluppo.

Completare le esercitazioni seguenti prima di procedere con queste istruzioni.
- Distribuire Azure IoT Edge su un dispositivo simulato in [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) o [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Distribuire Funzioni di Azure](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

L'elenco di controllo seguente illustra gli elementi che è necessario avere a disposizione dopo aver completato le esercitazioni precedenti.

- [Visual Studio Code](https://code.visualstudio.com/). 
- [Estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7](https://www.python.org/downloads/)
- [Script di controllo IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Modello AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- Un hub IoT attivo con almeno un dispositivo IoT Edge.

È inoltre consigliabile installare il [supporto Docker per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) per gestire in modo più efficiente le immagini e i contenitori del modulo.

> [!NOTE]
> Attualmente, Funzioni di Azure in IoT Edge supporta solo il linguaggio C#.

## <a name="deploy-azure-iot-functions-in-vs-code"></a>Distribuire Funzioni di Azure IoT in Visual Studio Code
Nell'esercitazione [Distribuire Funzioni di Azure](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function) si aggiornano, creano e pubblicano le immagini del modulo funzione in Visual Studio Code e quindi si accede al portale di Azure per distribuire Funzioni di Azure. Questa sezione illustra come usare Visual Studio Code per distribuire e monitorare Funzioni di Azure.

### <a name="start-a-local-docker-registry"></a>Avviare un registro Docker locale
È possibile usare qualsiasi registro compatibile con Docker per questa esercitazione. Due servizi molto diffusi per il registro Docker disponibili sul cloud sono il [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) e [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). In questa sezione viene usato un [registro Docker locale](https://docs.docker.com/registry/deploying/), che è più semplice da usare a scopo di test durante la fase di sviluppo iniziale.
Nel **terminale integrato** di Visual Studio Code (CTRL+') eseguire i comandi seguenti per avviare un registro locale.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> L'esempio precedente mostra le configurazioni del registro appropriate solo per scenari di test. Un registro destinato alla produzione deve essere protetto da TLS e dovrebbe preferibilmente usare un meccanismo di controllo di accesso. Per distribuire moduli IoT Edge pronti per l'ambiente di produzione è consigliabile usare [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) o [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

### <a name="create-a-function-project"></a>Creare un progetto per le funzioni
La procedura seguente illustra come creare un modulo di IoT Edge basato su .NET Core 2.0 tramite Visual Studio Code e l'estensione Azure IoT Edge. Se si è completata questa sezione nell'esercitazione precedente, è possibile ignorarla.

1. In Visual Studio Code selezionare **Visualizza** > **Terminale integrato** per aprire il terminale integrato di Visual Studio Code.
2. Per installare o aggiornare il modello **AzureIoTEdgeFunction** in dotnet, eseguire il comando seguente nel terminale integrato:

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. Creare un progetto per il nuovo modulo. Il comando seguente crea la cartella di progetto **FilterFunction** nella cartella di lavoro corrente.

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. Selezionare **File > Apri cartella**, passare alla cartella **FilterFunction** e aprire il progetto in Visual Studio Code.
5. Passare alla cartella **FilterFunction** e fare clic su **Seleziona cartella** per aprire il progetto in Visual Studio Code.
6. Nello strumento di esplorazione di Visual Studio Code espandere la cartella **EdgeHubTrigger-Csharp** e quindi aprire il file **run.csx**.
7. Sostituire il contenuto del file con il codice seguente:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
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

8. Salvare il file.

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Creare un'immagine Docker e pubblicarla nel registro

1. Nello strumento di esplorazione di Visual Studio Code espandere la cartella **Docker**. Espandere quindi la cartella per la piattaforma del contenitore, ovvero **linux-x64** o **windows-nano**.
2. Fare clic con il pulsante destro del mouse sul file **Dockerfile** e scegliere **Build IoT Edge module Docker image** (Compila l'immagine Docker per il modulo di IoT Edge). 

    ![Compilare l'immagine Docker](./media/how-to-vscode-develop-csharp-function/build-docker-image.png)

3. Passare alla cartella di progetto **FilterFunction** e fare clic su **Select Folder as EXE_DIR** (Seleziona cartella come EXE_DIR). 
4. Nella casella di testo popup nella parte superiore della finestra di Visual Studio Code immettere il nome dell'immagine. Ad esempio: `<your container registry address>/filterfunction:latest`. Se si esegue la distribuzione nel registro locale, il nome deve essere `localhost:5000/filterfunction:latest`.
5. Eseguire il push dell'immagine nel repository di Docker. Usare il comando **Edge: Push IoT Edge module Docker image** (Edge: Esegui il push dell'immagine Docker per il modulo IoT Edge) e immettere l'URL dell'immagine nella casella di testo popup nella parte superiore della finestra di Visual Studio Code. Usare lo stesso URL di immagine del passaggio precedente.
    ![Push dell'immagine Docker](./media/how-to-vscode-develop-csharp-function/push-image.png)

### <a name="deploy-your-function-to-iot-edge"></a>Distribuire la funzione in IoT Edge

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
   "filterfunction": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "localhost:5000/filterfunction:latest",
         "createOptions": ""
      }
   }
   ```

2. Sostituire la sezione **routes** con il contenuto seguente:
   ```json
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   ```
   > [!NOTE]
   > Le regole dichiarative nel runtime definiscono la destinazione del flusso di messaggi. In questa esercitazione sono necessarie due route. La prima route trasporta i messaggi dal sensore della temperatura alla funzione di filtro tramite l'endpoint "input1", ovvero l'endpoint configurato con il gestore FilterMessages. La seconda route trasporta i messaggi dalla funzione di filtro all'hub IoT. In questa route, upstream è una destinazione speciale che indica all'hub Edge di inviare messaggi all'hub IoT.

3. Salvare questo file.
4. Nel riquadro comandi selezionare **Edge: Create deployment for Edge device** (Edge: Crea distribuzione per dispositivo Edge). Selezionare quindi l'ID del dispositivo IoT Edge per creare una distribuzione. In alternativa, fare clic con il pulsante destro del mouse sull'elenco dei dispositivi e scegliere **Create deployment for Edge device** (Crea distribuzione per dispositivo Edge).

    ![Creare una distribuzione](./media/how-to-vscode-develop-csharp-function/create-deployment.png)

5. Selezionare il file `deployment.json` aggiornato. Nella finestra di output è possibile visualizzare l'output corrispondente per la distribuzione.
6. Avviare il runtime di Edge nel riquadro comandi selezionando **Edge: Start Edge** (Edge: Avvia Edge).
7. Nello strumento di esplorazione di Docker è possibile vedere che il runtime di IoT Edge è ora in esecuzione con il sensore simulato e la funzione di filtro.

    ![Soluzione in esecuzione](./media/how-to-vscode-develop-csharp-function/solution-running.png)

8. Facendo clic con il pulsante destro del mouse sull'ID del dispositivo Edge è possibile monitorare i messaggi D2C in Visual Studio Code.

    ![Monitoraggio dei messaggi](./media/how-to-vscode-develop-csharp-function/monitor-d2c-messages.png)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una funzione di Azure in IoT Edge e successivamente è stata distribuita in un dispositivo IoT Edge in Visual Studio Code. Per informazioni su altri scenari durante lo sviluppo di Azure IoT Edge in Visual Studio Code, è possibile continuare con l'esercitazione seguente.

> [!div class="nextstepaction"]
> [Eseguire il debug di Funzioni di Azure in Visual Studio Code](how-to-vscode-debug-azure-function.md)
