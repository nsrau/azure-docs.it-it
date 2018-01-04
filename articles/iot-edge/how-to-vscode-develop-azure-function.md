---
title: Usare codice di Visual Studio per sviluppare e distribuire le funzioni di Azure al bordo IoT di Azure | Documenti Microsoft
description: Sviluppare e distribuire un funzioni di Azure in c# con bordo IoT di Azure in Visual Studio Code senza cambio di contesto
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9637986d10a0e89568b2f79ede3d7b7468bb99a7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>Usare codice di Visual Studio per sviluppare e distribuire le funzioni di Azure al bordo IoT di Azure

In questo articolo vengono fornite istruzioni dettagliate per l'utilizzo di [codice di Visual Studio](https://code.visualstudio.com/) come lo strumento di sviluppo principali per sviluppare e distribuire le funzioni di Azure IoT lato. 

## <a name="prerequisites"></a>Prerequisiti
In questa esercitazione si presuppone che si utilizza un computer o macchina virtuale che esegue Windows o Linux come computer di sviluppo. Il dispositivo perimetrale IoT può essere un altro dispositivo fisico oppure è possibile simulare il dispositivo perimetrale IoT nel computer di sviluppo.

Assicurarsi di avere completato le esercitazioni seguenti prima di iniziare questa Guida.
- Distribuire Azure IoT Edge su un dispositivo simulato in [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) o [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Distribuire Funzioni di Azure](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

Di seguito è riportato un elenco di controllo che visualizza gli elementi, che è necessario dopo aver completato le esercitazioni precedenti.

- [Visual Studio Code](https://code.visualstudio.com/). 
- [Estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7](https://www.python.org/downloads/)
- [Script del controllo IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Modello AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- Un hub IoT attivo con almeno un dispositivo IoT Edge.

È inoltre consigliabile per installare [supporto Docker per il codice di Visual Studio](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) per gestire meglio le immagini dei moduli e i contenitori.

> [!NOTE]
> Attualmente, le funzioni di Azure IoT lato supporta solo c#.

## <a name="deploy-azure-iot-functions-in-vs-code"></a>Distribuire Azure IoT funzioni nel codice di Visual Studio
Nell'esercitazione [distribuire Azure funzioni](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function), aggiornare, compilazione e pubblicare le immagini di modulo di funzione nel codice di Visual Studio e quindi visitare il portale Azure per distribuire le funzioni di Azure. In questa sezione viene illustrato come utilizzare il codice di Visual Studio per distribuire e monitorare le funzioni di Azure.

### <a name="start-a-local-docker-registry"></a>Avviare un registro di sistema locale docker
È possibile usare qualsiasi registro compatibile con Docker per questa esercitazione. Due servizi molto diffusi per il registro Docker disponibili sul cloud sono il [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) e [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Questa sezione viene usato un [Registro di sistema locale Docker](https://docs.docker.com/registry/deploying/), che è più semplice per scopi di test durante lo sviluppo delle primo.
In Visual Studio Code **terminal integrata**(Ctrl + '), esecuzione seguenti comandi per avviare un registro di sistema locale.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Esempio precedente mostra le configurazioni del Registro di sistema solo appropriate per il test. Un registro di sistema di ambiente di produzione deve essere protetto da TLS e dovrebbe utilizzare un meccanismo di controllo di accesso. Si consiglia di usare [Registro di sistema di Azure contenitore](https://docs.microsoft.com/azure/container-registry/) o [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) per distribuire i moduli di IoT bordo di ambiente di produzione.

### <a name="create-a-function-project"></a>Creare un progetto per le funzioni
La procedura seguente illustra come creare un modulo di IoT Edge basato su .NET Core 2.0 tramite Visual Studio Code e l'estensione Azure IoT Edge. Se è stata completata in questa sezione nell'esercitazione precedente, è possibile ignorare in modo sicuro in questa sezione.

1. In Visual Studio Code selezionare **Visualizza** > **Terminale integrato** per aprire il terminale integrato di Visual Studio Code.
2. Per installare (o aggiornare) la **AzureIoTEdgeFunction** modello dotnet, eseguire il comando seguente in terminal integrato:

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. Creare un progetto per il nuovo modulo. Il comando seguente crea la cartella del progetto, **FilterFunction**, nella cartella di lavoro corrente:

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. Selezionare **File > Apri cartella**, quindi selezionare il **FilterFunction** cartella e aprire il progetto in Visual Studio Code.
5. Individuare il **FilterFunction** cartella e fare clic su **selezionare la cartella** per aprire il progetto in Visual Studio Code.
6. In Esplora il codice di Visual Studio, espandere il **EdgeHubTrigger Csharp** cartella, quindi aprire il **run.csx** file.
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
3. Passare il **FilterFunction** cartella del progetto e fare clic su **Seleziona cartella come EXE_DIR**. 
4. Nella casella di testo popup nella parte superiore della finestra di Visual Studio Code immettere il nome dell'immagine. Ad esempio: `<your container registry address>/filterfunction:latest`. Se si distribuisce nel Registro di sistema locale, deve essere `localhost:5000/filterfunction:latest`.
5. Eseguire il push dell'immagine nel repository di Docker. Utilizzare il **Edge: immagine di Docker modulo Push IoT Edge** comandi e immettere l'URL dell'immagine nella casella di testo popup nella parte superiore della finestra del codice di Visual Studio. Utilizzare lo stesso URL di immagini utilizzato nel precedente passaggio.

### <a name="deploy-your-function-to-iot-edge"></a>Distribuire la funzione al bordo IoT

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

2. Sostituire il **route** sezione con contenuto:
   ```json
   {
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   }
   ```
   > [!NOTE]
   > Le regole dichiarative nel runtime definiscono la destinazione del flusso di messaggi. In questa esercitazione sono necessarie due route. La prima route trasporta i messaggi del sensore di temperatura alla funzione di filtro tramite l'endpoint "input1", che è l'endpoint configurato con il gestore FilterMessages. La seconda route trasporta i messaggi dalla funzione di filtro all'IoT Hub. In questa route, upstream è una destinazione speciale che indica l'Hub di Edge di inviare messaggi all'IoT Hub.

3. Salvare il file.
4. Nel riquadro comandi, selezionare **Edge: creare una distribuzione per il dispositivo perimetrale**. Selezionare quindi l'ID del dispositivo IoT Edge per creare una distribuzione. O l'ID del dispositivo nell'elenco dei dispositivi e scegliere **creare distribuzione per il dispositivo perimetrale**.
5. Selezionare il `deployment.json` è aggiornato. Nella finestra di output, è possibile visualizzare gli output corrispondenti per la distribuzione.
6. Avviare il runtime di Edge nel riquadro comandi. **Edge: Bordo iniziale**
7. È possibile visualizzare il runtime IoT Edge avviarne l'esecuzione in Esplora Docker con la funzione simulata sensore e filtro.
8. L'ID dispositivo bordo destro ed è possibile monitorare i messaggi D2C in Visual Studio Code.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è creata una funzione di Azure IoT lato e distribuito al dispositivo IoT Edge in Visual Studio Code. È possibile continuare a una delle esercitazioni seguenti per ulteriori informazioni sugli altri scenari durante lo sviluppo di IoT Edge di Azure in Visual Studio Code.

> [!div class="nextstepaction"]
> [Eseguire il debug di funzioni di Azure in Visual Studio Code](how-to-vscode-debug-azure-function.md)
