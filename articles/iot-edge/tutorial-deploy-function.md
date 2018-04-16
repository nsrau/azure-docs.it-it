---
title: Distribuire Funzioni di Azure con Azure IoT Edge | Microsoft Docs
description: Distribuire Funzione di Azure come modulo in un dispositivo perimetrale
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: f1c6b5cd07752c6b29234a365b3298d76b639b3a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Distribuire Funzioni di Azure come modulo di IoT Edge: anteprima
È possibile usare Funzioni di Azure per distribuire il codice che implementa la logica di business direttamente nei dispositivi IoT Edge. Questa esercitazione illustra in modo dettagliato la creazione e la distribuzione di una funzione di Azure che filtra i dati dei sensori nel dispositivo IoT Edge simulato, creato nelle esercitazioni sulla distribuzione di Azure IoT Edge in un dispositivo simulato in [Windows][lnk-tutorial1-win] o [Linux][lnk-tutorial1-lin]. In questa esercitazione si apprenderà come:     

> [!div class="checklist"]
> * Usare Visual Studio Code per creare una funzione di Azure
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla nel registro 
> * Distribuire il modulo nel dispositivo IoT Edge
> * Visualizzare i dati generati


La funzione di Azure creata in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo e invia messaggi upstream all'hub IoT di Azure solo quando la temperatura è superiore a una soglia specificata. 

## <a name="prerequisites"></a>Prerequisiti

* Il dispositivo Azure IoT Edge creato nella guida introduttiva o nell'esercitazione precedente.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Docker](https://docs.docker.com/engine/installation/). Per questa esercitazione è sufficiente la Community Edition (CE) per la piattaforma usata. 
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
2. Per aprire il terminale integrato di Visual Studio Code, selezionare **Visualizza** > **Terminale integrato**.
3. Per installare o aggiornare il modello **AzureIoTEdgeFunction** in dotnet, eseguire il comando seguente nel terminale integrato:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Creare un progetto per il nuovo modulo. Il comando seguente crea la cartella del progetto, **FilterFunction**, con il repository del contenitore. Se si usa Registro contenitori di Azure, il secondo parametro deve essere nel formato `<your container registry name>.azurecr.io`. Nella cartella di lavoro corrente immettere il comando seguente:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction -r <your container registry address>/filterfunction
    ```

3. Selezionare **File** > **Apri cartella**, quindi passare alla cartella **FilterFunction** e aprire il progetto in Visual Studio Code.
4. Nello strumento di esplorazione di Visual Studio Code espandere la cartella **EdgeHubTrigger-Csharp** e quindi aprire il file **run.csx**.
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
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
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

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Creare un'immagine Docker e pubblicarla nel registro

1. Accedere a Docker immettendo il comando seguente nel terminale integrato di Visual Studio Code: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Per trovare il nome utente, la password e il server di accesso da usare in questo comando, passare al [portale di Azure] (https://portal.azure.com). Da **Tutte le risorse** fare clic sul riquadro per il Registro contenitori di Azure per aprirne le proprietà, quindi fare clic su **Chiavi di accesso**. Copiare i valori nei campi **Nome utente**, **Password** e **Server di accesso**. 

2. Aprire **module.json**. È facoltativamente possibile aggiornare `"version"` ad esempio a **"1.0"**. Viene mostrato anche il nome del repository immesso nel parametro `-r` di `dotnet new aziotedgefunction`.

3. Salvare il file **module.json**.

4. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file **module.json** e quindi scegliere **Build and Push IoT Edge module Docker image** (Compilazione e push dell'immagine Docker del modulo per IoT Edge). Nella casella a discesa popup nella parte superiore della finestra di Visual Studio Code selezionare la piattaforma del contenitore, **amd64** per un contenitore Linux o **windows-amd64** per un contenitore Windows. Visual Studio Code inserisce quindi in contenitori i codici funzione e ne esegue il push nel registro contenitori specificato.

5. È possibile ottenere l'indirizzo completo dell'immagine del contenitore con tag nel terminale integrato di Visual Studio Code. Per altre informazioni sulla definizione di compilazione e push, è possibile fare riferimento al file `module.json`.

## <a name="add-registry-credentials-to-your-edge-device"></a>Aggiungere le credenziali del registro al dispositivo perimetrale
Aggiungere le credenziali per il registro al runtime di Edge nel computer in cui si esegue il dispositivo perimetrale, in modo da consentire al runtime l'accesso per il pull del contenitore. 

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
2. Passare a **IoT Edge (preview)** (IoT Edge - anteprima) e selezionare il dispositivo IoT Edge.
1. Selezionare **Set Modules** (Configura i moduli). 
2. Se il modulo **tempSensor** è già stato distribuito in questo dispositivo, è possibile che sia inserito automaticamente. In caso contrario, seguire questi passaggi per aggiungerlo:
    1. Selezionare **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `tempSensor`.
    3. Nel campo **URI immagine** immettere `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Lasciare invariate le altre impostazioni e fare clic su **Salva**.
1. Aggiungere il modulo **filterFunction**.
    1. Selezionare di nuovo **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `filterFunction`.
    3. Nel campo **URI immagine** immettere l'indirizzo dell'immagine, ad esempio `<your container registry address>/filterfunction:0.0.1-amd64`. Nella sezione precedente è possibile trovare l'indirizzo completo dell'immagine.
    74. Fare clic su **Save**.
2. Fare clic su **Avanti**.
3. Nel passaggio **Specify Routes** (Specifica route) copiare il codice JSON seguente nella casella di testo. La prima route trasporta i messaggi dal sensore della temperatura al modulo del filtro tramite l'endpoint "input1", La seconda route trasporta i messaggi dal modulo del filtro all'hub IoT. In questa route `$upstream` è una destinazione speciale che indica all'hub di Edge di inviare messaggi all'hub IoT. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Fare clic su **Avanti**.
5. Nel passaggio **Review Template** (Verifica il modello) fare clic su **Invia**. 
6. Tornare alla pagina dei dettagli del dispositivo IoT Edge e fare clic su **Aggiorna**. Dovrebbe essere visualizzato il nuovo **filterfunction** in esecuzione insieme al modulo **tempSensor** e al **runtime di IoT Edge**. 

## <a name="view-generated-data"></a>Visualizzare i dati generati

Per monitorare i messaggi da dispositivo a cloud inviati dal dispositivo IoT Edge all'hub IoT:
1. Configurare l'estensione Azure IoT Toolkit con la stringa di connessione per l'hub IoT: 
    1. Nel portale di Azure passare all'hub IoT e selezionare **Criteri di accesso condivisi**. 
    2. Selezionare **iothubowner** e copiare il valore di **Stringa di connessione - chiave primaria**.
    3. Nello strumento di esplorazione di Visual Studio Code fare clic su **IOT HUB DEVICES** (DISPOSITIVI DELL'HUB IOT) e quindi fare clic su **...** 
    4. Fare clic su **Set IoT Hub Connection String** (Configura la stringa di connessione dell'hub IoT) e immettere la stringa di connessione dell'hub IoT nella finestra popup. 

2. Per monitorare i dati in arrivo nell'hub IoT, selezionare **Visualizza** > **Riquadro comandi** e cercare **IoT: Start monitoring D2C message** (IoT: Avvia il monitoraggio del messaggio D2C). 
3. Per interrompere il monitoraggio dei dati, usare il comando **IoT: Stop monitoring D2C message** (IoT: Interrompi il monitoraggio del messaggio D2C) nel riquadro comandi. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una funzione di Azure contenente il codice per filtrare i dati non elaborati generati dal dispositivo IoT Edge. Per continuare a esplorare Azure IoT Edge, imparare a usare un dispositivo IoT come gateway. 

> [!div class="nextstepaction"]
> [Creare un dispositivo gateway di IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
