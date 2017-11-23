---
title: Distribuire Funzioni di Azure con Azure IoT Edge | Microsoft Docs
description: Distribuire Funzione di Azure come modulo in un dispositivo perimetrale
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 34ed5083b952c42d4ed119b6986db965eb9eb67a
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
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
* [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). È possibile installare l'estensione dal pannello Estensioni in Visual Studio Code.
* [Estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). È possibile installare l'estensione dal pannello Estensioni in Visual Studio Code.
* [Docker](https://docs.docker.com/engine/installation/). La Community Edition (CE) per la piattaforma è sufficiente per questa esercitazione. 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="set-up-a-docker-registry"></a>Configurare un registro Docker
In questa esercitazione viene usata l'estensione Azure IoT Edge per Visual Studio Code per creare Funzioni di Azure e compilare un'[immagine Docker](https://docs.docker.com/glossary/?term=image) con essa. Eseguire quindi il push di tale immagine Docker in un [repository di Docker](https://docs.docker.com/glossary/?term=repository) ospitato in un [registro Docker](https://docs.docker.com/glossary/?term=registry). Distribuire infine l'immagine Docker in un pacchetto come [contenitore di Docker](https://docs.docker.com/glossary/?term=container) dal registro nel dispositivo di IoT Edge.  

È possibile usare qualsiasi registro compatibile con Docker per questa esercitazione. Due servizi molto diffusi per il registro Docker disponibili sul cloud sono il **Registro contenitori di Azure** e **Hub Docker**:

- Il [Registro contenitori di Azure](https://docs.microsoft.com/en-us/azure/container-registry/) è disponibile con una [sottoscrizione a pagamento](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Per questa esercitazione è sufficiente la sottoscrizione **Basic**. 

- [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) offre un repository privato gratuito se ci si iscrive per ottenere un ID Docker gratuito. 
    1. Per iscriversi e ottenere un ID Docker, seguire le istruzioni disponibili in [Register for a Docker ID](https://docs.docker.com/docker-id/#register-for-a-docker-id) (Effettuare la registrazione per ottenere un ID Docker) nel sito Docker. 

    2. Per creare un repository di Docker privato, seguire le istruzioni disponibili in [Creating a new repository on Docker Hub](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) (Creazione di un nuovo repository in Hub Docker) nel sito Docker.

In questa esercitazione, dove appropriato, verranno indicati i comandi per il Registro contenitori di Azure e per Hub Docker.

## <a name="create-a-function-project"></a>Creare un progetto per le funzioni
La procedura seguente illustra come creare una funzione di IoT Edge tramite Visual Studio Code e l'estensione Azure IoT Edge.
1. Aprire Visual Studio Code.
2. Usare il comando di menu **Visualizza | Terminale integrato** per aprire il terminale integrato di Visual Studio Code.
3. Nel terminale integrato immettere il comando seguente per installare o aggiornare il modello **AzureIoTEdgeFunction** in dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Nel terminale integrato immettere il comando seguente per creare un progetto per il nuovo modulo:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

    >[!NOTE]
    > Questo comando crea la cartella del progetto, **FilterFunction**, nella cartella di lavoro corrente. Se si vuole creare la cartella in un'altra posizione, cambiare le directory prima di eseguire il comando.

3. Usare il comando di menu **File | Apri cartella**, passare alla cartella **FilterFunction** e fare clic su **Seleziona cartella** per aprire il progetto in Visual Studio Code.
4. Nello strumento di esplorazione di Visual Studio Code scegliere la cartella **EdgeHubTrigger-Csharp**, quindi fare clic sul file **run.csx** per aprirlo.
5. Aggiungere l'istruzione seguente dopo l'istruzione `#r "Microsoft.Azure.Devices.Client"`:

    ```csharp
    #r "Newtonsoft.Json"
    ```

5. Aggiungere l'istruzione using seguente dopo l'istruzione `using` esistente:

    ```csharp
    using Newtonsoft.Json;
    ```

1. Aggiungere le classi seguenti. Queste classi definiscono lo schema previsto per il corpo dei messaggi in arrivo.

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

1. Sostituire il corpo del metodo **Run** con il codice seguente. Filtra i messaggi in base al valore della temperatura nel corpo del messaggio e alla soglia relativa alla temperatura.

    ```csharp
    const int temperatureThreshold = 25;

    byte[] messageBytes = messageReceived.GetBytes();
    var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

    if (!string.IsNullOrEmpty(messageString))
    {
        // Get the body of the message and deserialize it
        var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);
        
        if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
        {
            // We will send the message to the output as the temperature value is greater than the threashold
            var filteredMessage = new Message(messageBytes);
            // We need to copy the properties of the original message into the new Message object
            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
            {
                filteredMessage.Properties.Add(prop.Key, prop.Value);
            }
            // We are adding a new property to the message to indicate it is an alert
            filteredMessage.Properties.Add("MessageType", "Alert");
            // Send the message        
            await output.AddAsync(filteredMessage);
            log.Info("Received and transfered a message with temperature above the threshold");
        }
    }
    ```

11. Salvare il file.

## <a name="publish-a-docker-image"></a>Pubblicare un'immagine Docker

1. Compilare l'immagine Docker.
    1. Nello strumento di esplorazione di Visual Studio Code fare clic sulla cartella **Docker** per aprirla. Selezionare quindi la cartella per la piattaforma del contenitore, ovvero **linux-x64** o **windows-nano**. 
    2. Fare clic con il pulsante destro del mouse sul file **Dockerfile** e scegliere **Build IoT Edge module Docker image** (Compila l'immagine Docker per il modulo di IoT Edge). 
    3. Nella casella di testo **Seleziona cartella** passare alla cartella **Docker/linux-x64** e fare clic su **Select Folder as EXE_DIR** (Seleziona cartella come EXE_DIR). 
    4. Nella casella di testo popup nella parte superiore della finestra di Visual Studio Code immettere il nome dell'immagine. Ad esempio `<docker registry address>/filterfunction:latest`, dove *docker registry address* è l'ID Docker se si usa Hub Dockero è simile a `<your registry name>.azurecr.io`, se si usa il Registro contenitori di Azure.
 
4. Accedere a Docker. Nel terminale integrato immettere il comando seguente: 

    - Hub Docker, immettere le credenziali quando richiesto:
        
        ```csh/sh
        docker login
        ```

    - Registro contenitori di Azure:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Per trovare il nome utente, la password e il server di accesso da usare in questo comando, passare al [portale di Azure] (https://portal.azure.com). Da **Tutte le risorse** fare clic sul riquadro per il Registro contenitori di Azure per aprirne le proprietà, quindi fare clic su **Chiavi di accesso**. Copiare i valori nei campi **Nome utente**, **Password** e **Server di accesso**. Il server di accesso deve avere il formato seguente: `<your registry name>.azurecr.io`.

3. Eseguire il push dell'immagine nel repository di Docker. Usare il comando di menu **Visualizza | Riquadro comandi... | Edge: Push IoT Edge module Docker image** (Edge: esegui il push dell'immagine Docker per il modulo di IoT Edge) e immettere il nome dell'immagine nella casella di testo popup nella parte superiore della finestra di Visual Studio Code. Usare lo stesso nome di immagine usato nel passaggio 1.c.

## <a name="add-registry-credentials-to-your-edge-device"></a>Aggiungere le credenziali del registro al dispositivo di Edge
Aggiungere le credenziali per il registro al runtime di Edge nel computer in cui si esegue il dispositivo perimetrale di Edge, in modo da consentire al runtime l'accesso per il pull del contenitore. 

- Per Windows, eseguire il comando seguente:
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- Per Linux, eseguire il comando seguente:
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>Eseguire la soluzione

1. Nel **portale di Azure** passare all'hub IoT.
2. Passare a **IoT Edge (preview)** (IoT Edge - anteprima) e selezionare il dispositivo di IoT Edge.
1. Selezionare **Set Modules** (Configura i moduli). 
2. Aggiungere il modulo **tempSensor**. Questo passaggio è necessario solo se il modulo **tempSensor** non è stato distribuito in precedenza nel dispositivo di IoT Edge.
    1. Selezionare **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `tempSensor`.
    3. Nel campo **URI immagine** immettere `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Lasciare invariate le altre impostazioni e fare clic su **Salva**.
1. Aggiungere il modulo **filterfunction**.
    1. Selezionare di nuovo **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `filterfunction`.
    3. Nel campo **Immagine** immettere l'indirizzo dell'immagine, ad esempio `<docker registry address>/filterfunction:latest`.
    74. Fare clic su **Salva**.
2. Fare clic su **Avanti**.
3. Nel passaggio **Specify Routes** (Specifica route) copiare il codice JSON seguente nella casella di testo. I moduli pubblicano tutti i messaggi nel runtime di Edge. Le regole dichiarative nel runtime definiscono la destinazione del flusso di messaggi. In questa esercitazione sono necessarie due route. La prima route trasporta i messaggi dal sensore della temperatura al modulo di filtro tramite l'endpoint "input1", ovvero l'endpoint configurato con il gestore **FilterMessages**. La seconda route trasporta i messaggi dal modulo del filtro all'hub IoT. In questa route `upstream` è una destinazione speciale che indica all'hub di Edge di inviare messaggi all'hub IoT. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Fare clic su **Avanti**.
5. Nel passaggio **Review Template** (Verifica il modello) fare clic su **Invia**. 
6. Tornare alla pagina dei dettagli del dispositivo di IoT Edge e fare clic su **Aggiorna**. Dovrebbe essere visualizzato il nuovo **filterfunction** in esecuzione insieme al modulo **tempSensor** e al **runtime di IoT Edge**. 

## <a name="view-generated-data"></a>Visualizzare i dati generati

Per monitorare i messaggi da dispositivo a cloud inviati dal dispositivo di IoT Edge all'hub IoT:
1. Configurare l'estensione Azure IoT Toolkit con la stringa di connessione per l'hub IoT: 
    1. Usare il comando di menu **Visualizza | Esplora risorse** per aprire lo strumento di esplorazione di Visual Studio Code. 
    3. Nello strumento di esplorazione fare clic su **IOT HUB DEVICES** (DISPOSITIVI DELL'HUB IOT) e quindi fare clic su **...**. Fare clic su **Set IoT Hub Connection String** (Configura la stringa di connessione dell'hub IoT) e immettere la stringa di connessione per l'hub IoT a cui si connette il dispositivo di IoT Edge nella finestra popup. 

        Per trovare la stringa di connessione, fare clic sul riquadro dell'hub IoT nel portale di Azure e quindi fare clic su **Criteri di accesso condiviso**. In **Criteri di accesso condiviso** fare clic sul criterio **iothubowner** e copiare la stringa di connessione dell'hub IoT nella finestra **iothubowner**.   

1. Per monitorare i dati in arrivo nell'hub IoT, usare il comando di menu **Visualizza | Riquadro comandi... | IoT: Start monitoring D2C message** (IoT: avvia il monitoraggio del messaggio D2C). 
2. Per interrompere il monitoraggio dei dati, usare il comando di menu **Visualizza | Riquadro comandi... | IoT: Stop monitoring D2C message** (IoT: interrompi il monitoraggio del messaggio D2C). 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato Funzioni di Azure contenente il codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. Per continuare a esplorare Azure IoT Edge, imparare a usare un dispositivo IoT come gateway. 

> [!div class="nextstepaction"]
> [Creare un dispositivo gateway di IoT Edge](how-to-create-gateway-device.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
