---
title: Distribuire Funzioni di Azure con Azure IoT Edge | Microsoft Docs
description: Distribuire Funzione di Azure come modulo in un dispositivo perimetrale
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1dfe46d307a076ae02362c4bba292602001ed915
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2017
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Distribuire Funzioni di Azure come modulo di IoT Edge: anteprima
È possibile usare Funzioni di Azure per distribuire il codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra in modo dettagliato la creazione e la distribuzione di Funzioni di Azure che filtra i dati dei sensori nel dispositivo di IoT Edge simulato creato nelle esercitazioni sulla distribuzione di Azure IoT Edge in un dispositivo simulato su [Windows][lnk-tutorial1-win] o [Linux][lnk-tutorial1-lin]. In questa esercitazione si apprenderà come:     

> [!div class="checklist"]
> * Usare Visual Studio Code per creare una funzione di Azure
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla nel registro 
> * Distribuire il modulo nel dispositivo di IoT Edge
> * Visualizzare i dati generati


Funzioni di Azure creata in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo e invia messaggi upstream all'hub IoT di Azure solo quando la temperatura è superiore a una soglia specificata. 

## <a name="prerequisites"></a>Prerequisiti

* Il dispositivo Azure IoT Edge creato nella guida introduttiva o nell'esercitazione precedente.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Docker](https://docs.docker.com/engine/installation/). La Community Edition (CE) per la piattaforma è sufficiente per questa esercitazione. 
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

## <a name="create-a-function-project"></a>Creare un progetto per le funzioni
La procedura seguente illustra come creare una funzione di IoT Edge tramite Visual Studio Code e l'estensione Azure IoT Edge.
1. Aprire Visual Studio Code.
2. Per aprire i servizi terminal integrato di Visual Studio Code, selezionare **vista** > **integrata Terminal**.
3. Per installare (o aggiornare) la **AzureIoTEdgeFunction** modello dotnet, eseguire il comando seguente in terminal integrato:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Creare un progetto per il nuovo modulo. Il comando seguente crea la cartella del progetto, **FilterFunction**, nella cartella di lavoro corrente:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

3. Selezionare **File** > **Apri cartella**, quindi selezionare il **FilterFunction** cartella e aprire il progetto in Visual Studio Code.
4. In Esplora il codice di Visual Studio, espandere il **EdgeHubTrigger Csharp** cartella, quindi aprire il **run.csx** file.
5. Sostituire il contenuto del file con il codice seguente:

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

11. Salvare il file.

## <a name="publish-a-docker-image"></a>Pubblicare un'immagine Docker

1. Compilare l'immagine Docker.
    1. Nello strumento di esplorazione di Visual Studio Code espandere la cartella **Docker**. Espandere quindi la cartella per la piattaforma del contenitore, ovvero **linux-x64** o **windows-nano**. 
    2. Fare clic con il pulsante destro del mouse sul file **Dockerfile** e scegliere **Build IoT Edge module Docker image** (Compila l'immagine Docker per il modulo di IoT Edge). 
    3. Passare il **FilterFunction** cartella del progetto e fare clic su **Seleziona cartella come EXE_DIR**. 
    4. Nella casella di testo popup nella parte superiore della finestra di Visual Studio Code immettere il nome dell'immagine. Ad esempio: `<your container registry address>/filterfunction:latest`. L'indirizzo del registro di sistema del contenitore è lo stesso del server di accesso copiato dal registro di sistema. Deve essere nel formato `<your container registry name>.azurecr.io`.
 
4. Accedere a Docker. In terminal integrato, immettere il comando seguente: 

   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Per trovare il nome utente, la password e il server di accesso da usare in questo comando, passare al [portale di Azure] (https://portal.azure.com). Da **Tutte le risorse** fare clic sul riquadro per il Registro contenitori di Azure per aprirne le proprietà, quindi fare clic su **Chiavi di accesso**. Copiare i valori nei campi **Nome utente**, **Password** e **Server di accesso**. 

3. Eseguire il push dell'immagine nel repository di Docker. Selezionare **vista** > **comando tavolozza...**  quindi cercare **Edge: immagine di Docker modulo Push IoT Edge**.
4. Nella casella di testo popup, immettere il nome dell'immagine stessa utilizzata nel passaggio 1.d.

## <a name="add-registry-credentials-to-your-edge-device"></a>Aggiungere le credenziali del registro al dispositivo di Edge
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

1. Nel **portale di Azure** passare all'hub IoT.
2. Passare a **IoT Edge (preview)** (IoT Edge - anteprima) e selezionare il dispositivo di IoT Edge.
1. Selezionare **Set Modules** (Configura i moduli). 
2. Se è già stato distribuito il **tempSensor** modulo al dispositivo, può essere popolato automaticamente. In caso contrario, attenersi alla procedura seguente per aggiungerlo:
    1. Selezionare **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `tempSensor`.
    3. Nel campo **URI immagine** immettere `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Lasciare invariate le altre impostazioni e fare clic su **Salva**.
1. Aggiungere il **filterFunction** modulo.
    1. Selezionare di nuovo **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `filterFunction`.
    3. Nel campo **Immagine** immettere l'indirizzo dell'immagine, ad esempio `<docker registry address>/filterfunction:latest`.
    74. Fare clic su **Save**.
2. Fare clic su **Avanti**.
3. Nel passaggio **Specify Routes** (Specifica route) copiare il codice JSON seguente nella casella di testo. La prima route trasporta i messaggi del sensore di temperatura del modulo di filtro tramite l'endpoint "input1". La seconda route trasporta i messaggi dal modulo del filtro all'hub IoT. In questa route `$upstream` è una destinazione speciale che indica all'hub di Edge di inviare messaggi all'hub IoT. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Fare clic su **Avanti**.
5. Nel passaggio **Review Template** (Verifica il modello) fare clic su **Submit** (Invia). 
6. Tornare alla pagina dei dettagli del dispositivo di IoT Edge e fare clic su **Aggiorna**. Dovrebbe essere visualizzato il nuovo **filterfunction** in esecuzione insieme al modulo **tempSensor** e al **runtime di IoT Edge**. 

## <a name="view-generated-data"></a>Visualizzare i dati generati

Per monitorare i messaggi da dispositivo a cloud inviati dal dispositivo di IoT Edge all'hub IoT:
1. Configurare l'estensione Azure IoT Toolkit con la stringa di connessione per l'hub IoT: 
    1. Nel portale di Azure, individuare l'hub IoT e selezionare **criteri di accesso condiviso**. 
    2. Selezionare **iothubowner** quindi copiare il valore di **chiave primaria di stringa di connessione**.
    1. In Esplora il codice di Visual Studio, fare clic su **IOT HUB dispositivi** e quindi fare clic su **...** . 
    1. Selezionare **stringa di connessione Hub IoT impostare** e immettere la stringa di connessione Iot Hub, nella finestra popup. 

1. Per monitorare i dati in arrivo presso l'hub IoT, selezionare il **vista** > **riquadro comandi...**  e cercare **IoT: avviare il monitoraggio di messaggio D2C**. 
2. Per arrestare il monitoraggio dei dati, usare il **IoT: interrompere il monitoraggio di messaggio D2C** comando nel riquadro comandi. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato Funzioni di Azure contenente il codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. Per continuare a esplorare Azure IoT Edge, imparare a usare un dispositivo IoT come gateway. 

> [!div class="nextstepaction"]
> [Creare un dispositivo gateway di IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
