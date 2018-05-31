---
title: Modulo Python per Azure IoT Edge | Microsoft Docs
description: Creare un modulo per IoT Edge con codice Python e distribuirlo in un dispositivo perimetrale
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3c46df85f95377f5740526542ac1baf5a8fd77c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177836"
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Sviluppare e distribuire un modulo Python per IoT Edge in un dispositivo simulato - Anteprima

È possibile usare i moduli di IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore. Verrà utilizzato il dispositivo IoT Edge simulato che è stato creato nelle esercitazioni Distribuire Azure IoT Edge in un dispositivo simulato in [Windows][lnk-tutorial1-win] e [Linux][lnk-tutorial1-lin]. In questa esercitazione si apprenderà come:    

> [!div class="checklist"]
> * Usare Visual Studio Code per creare un modulo Python per IoT Edge
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla nel registro 
> * Distribuire il modulo nel dispositivo IoT Edge
> * Visualizzare i dati generati


Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo quando la temperatura è superiore a una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud. 

> [!IMPORTANT]
> Attualmente il modulo Python può essere eseguito solo in contenitori Linux amd64, non in contenitori Windows o basati su ARM. 

## <a name="prerequisites"></a>Prerequisiti

* Il dispositivo Azure IoT Edge creato nella guida introduttiva o nella prima esercitazione.
* La chiave primaria della stringa di connessione del dispositivo IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Estensione di Python per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) nello stesso computer con Visual Studio Code. La Community Edition (CE) è sufficiente per questa esercitazione. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) per l'installazione di pacchetti Python (generalmente incluso con l'installazione di Python).

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
La procedura seguente illustra come creare un modulo Python per IoT Edge tramite Visual Studio Code e l'estensione Azure IoT Edge.
1. In Visual Studio Code selezionare **Visualizza** > **Terminale integrato** per aprire il terminale integrato di Visual Studio Code.
2. Nel terminale integrato immettere il comando seguente per installare (o aggiornare) **cookiecutter** (è consigliabile farlo in un ambiente virtuale o come installazione utente come illustrato di seguito):

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Creare un progetto per il nuovo modulo. Il comando seguente crea la cartella del progetto, **FilterModule**, con il repository del contenitore. Il parametro di `image_repository` deve essere nel formato `<your container registry name>.azurecr.io/filtermodule` se si usa il Registro contenitori di Azure. Nella cartella di lavoro corrente immettere il comando seguente:

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. Selezionare  **File** > **Apri cartella**.
5. Passare alla cartella **FilterModule** e fare clic su **Seleziona cartella** per aprire il progetto in VS Code.
6. Nello strumento di esplorazione di VS Code fare clic su **main.py** per aprirlo.
7. Nella parte superiore dello spazio dei nomi **FilterModule** importare la raccolta `json`:

    ```python
    import json
    ```

8. Aggiungere `TEMPERATURE_THRESHOLD`, `RECEIVE_CALLBACKS` e `TWIN_CALLBACKS` nei contatori globali. La soglia della temperatura imposta il valore che la temperatura misurata deve superare per inviare i dati all'hub IoT.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = RECEIVE_CALLBACKS = 0
    ```

9. Aggiornare la funzione `receive_message_callback` con il contenuto seguente.

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print("    Data: <<<{}>>> & Size={:d}".format(message_text, size))
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print("    Properties: {}".format(key_value_pair))
        RECEIVE_CALLBACKS += 1
        print("    Total calls received: {:d}".format(RECEIVE_CALLBACKS))
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature {} exceeds threshold {}".format(data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. Aggiungere una nuova funzione `device_twin_callback`. Questa funzione verrà richiamata quando le proprietà desiderate verranno aggiornate.

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print("\nTwin callback called with:\nupdateStatus = {}\npayload = {}\ncontext = {}".format(update_state, payload, user_context))
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print("Total calls confirmed: {:d}\n".format(TWIN_CALLBACKS))
    ```

11. Nella classe `HubManager` aggiungere una nuova riga al metodo `__init__` per inizializzare la funzione `device_twin_callback` appena aggiunta.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. Salvare questo file.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Creare un'immagine Docker e pubblicarla nel registro

1. Accedere a Docker immettendo il comando seguente nel terminale integrato di Visual Studio Code: 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Usare il nome utente, la password e il server di accesso copiati quando è stato creato il registro di sistema del contenitore di Azure.

2. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file **module.json** e quindi scegliere **Build and Push IoT Edge module Docker image** (Compilazione e push dell'immagine Docker del modulo per IoT Edge). Nella casella a discesa popup nella parte superiore della finestra di VS Code selezionare la piattaforma del contenitore, ad esempio **amd64** per il contenitori di Linux. VS Code include nel contenitore `main.py` e le dipendenze richieste, quindi ne esegue il push nel registro contenitori specificato. La prima volta che si compila l'immagine potrebbero essere necessari alcuni minuti.

3. È possibile ottenere l'indirizzo completo dell'immagine del contenitore con tag nel terminale integrato di VS Code. Per altre informazioni sulla definizione di compilazione e push, è possibile fare riferimento al file `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Aggiungere le credenziali del registro al runtime di Edge
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

1. Nel [portale di Azure](https://portal.azure.com) passare all'hub IoT.
2. Passare a **IoT Edge (preview)** (IoT Edge - anteprima) e selezionare il dispositivo IoT Edge.
3. Selezionare **Set Modules** (Configura i moduli). 
2. Verificare che il modulo **tempSensor** venga inserito automaticamente. In caso contrario, seguire questa procedura:
    1. Selezionare **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `tempSensor`.
    3. Nel campo **URI immagine** immettere `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Lasciare invariate le altre impostazioni e fare clic su **Salva**.
9. Aggiungere il modulo **filterModule** creato nella sezione precedente. 
    1. Selezionare **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `filterModule`.
    3. Nel campo **URI immagine** immettere l'indirizzo dell'immagine, ad esempio `<your container registry address>/filtermodule:0.0.1-amd64`. Nella sezione precedente è possibile trovare l'indirizzo completo dell'immagine.
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
10. Fare clic su **Avanti**.
11. Nel passaggio **Specify Routes** (Specifica route) copiare il codice JSON seguente nella casella di testo. I moduli pubblicano tutti i messaggi nel runtime di Edge. Le regole dichiarative nel runtime definiscono la destinazione del flusso di messaggi. In questa esercitazione sono necessarie due route. La prima route trasporta i messaggi dal sensore della temperatura al modulo di filtro tramite l'endpoint "input1", ovvero l'endpoint configurato con il gestore **FilterMessages**. La seconda route trasporta i messaggi dal modulo del filtro all'hub IoT. In questa route `upstream` è una destinazione speciale che indica all'hub Edge di inviare messaggi all'hub IoT. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Fare clic su **Avanti**.
5. Nel passaggio **Review Template** (Verifica il modello) fare clic su **Invia**. 
6. Tornare alla pagina dei dettagli del dispositivo di IoT Edge e fare clic su **Aggiorna**. Dovrebbe essere visualizzato il nuovo **filtermodule** in esecuzione insieme al modulo **tempSensor** e al **runtime di IoT Edge**. 

## <a name="view-generated-data"></a>Visualizzare i dati generati

Per monitorare i messaggi da dispositivo a cloud inviati dal dispositivo IoT Edge all'hub IoT:
1. Configurare l'estensione Azure IoT Toolkit con la stringa di connessione per l'hub IoT: 
    1. Aprire lo strumento di esplorazione di Visual Studio Code selezionando **Visualizza** > **Explorer**. 
    3. Nello strumento di esplorazione fare clic su **IOT HUB DEVICES** (DISPOSITIVI DELL'HUB IOT) e quindi fare clic su **...**. Fare clic su **Set IoT Hub Connection String** (Configura la stringa di connessione dell'hub IoT) e immettere la stringa di connessione per l'hub IoT a cui si connette il dispositivo IoT Edge nella finestra popup. 

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
