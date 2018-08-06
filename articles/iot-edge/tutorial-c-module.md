---
title: Esercitazione per C in Azure IoT Edge | Microsoft Docs
description: Questa esercitazione illustra come creare un modulo per IoT Edge con codice C e distribuirlo in un dispositivo perimetrale.
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 07/30/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c9d1931f1b78bb19f5e321a19baca45265ea7ab4
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413162"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Esercitazione: Sviluppare un modulo C per IoT Edge e distribuirlo in un dispositivo simulato

È possibile usare i moduli di IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore. In questa esercitazione si apprenderà come:    

> [!div class="checklist"]
> * Usare Visual Studio Code per creare un modulo IoT Edge in C
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla nel registro contenitori 
> * Distribuire il modulo nel dispositivo IoT Edge
> * Visualizzare i dati generati


Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo quando la temperatura è superiore a una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

* È possibile usare il computer per lo sviluppo o una macchina virtuale come dispositivo perimetrale seguendo la procedura illustrata nella guida introduttiva per dispositivi [Linux](quickstart-linux.md) o [Windows](quickstart.md).

Risorse cloud:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Standard in Azure. 

Risorse per lo sviluppo:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Estensione C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) per Visual Studio Code.
* [Estensione Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) per Visual Studio Code.
* [Docker CE](https://docs.docker.com/install/). 


## <a name="create-a-container-registry"></a>Creare un registro di contenitori
In questa esercitazione viene usata l'estensione Azure IoT Edge per Visual Studio Code per creare un modulo e un'**immagine del contenitore** dai file. Eseguire quindi il push dell'immagine in un **registro** che archivia e gestisce le immagini. Distribuire infine l'immagine dal registro nel dispositivo IoT Edge.  

È possibile usare qualsiasi registro compatibile con Docker per questa esercitazione. Due servizi molto diffusi per il registro Docker disponibili sul cloud sono il [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) e [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Questa esercitazione usa il Registro contenitori di Azure. 

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Contenitori** > **Registro contenitori di Azure**.
2. Assegnare un nome al registro, scegliere una sottoscrizione e un gruppo di risorse e impostare lo SKU su **Di base**. 
3. Selezionare **Crea**.
4. Dopo aver creato il registro contenitori, passare al registro e selezionare **Chiavi di accesso**. 
5. Impostare **Utente amministratore** su **Abilita**.
6. Copiare i valori nei campi **Server di accesso**, **Nome utente** e **Password**. Questi valori verranno usati più avanti nel corso dell'esercitazione, per pubblicare l'immagine Docker nel Registro di sistema e per aggiungere le credenziali del registro al runtime di Edge. 

## <a name="create-an-iot-edge-module-project"></a>Creare un progetto di modulo IoT Edge
La procedura seguente illustra come creare un progetto di modulo di IoT Edge basato su .NET Core 2.0 tramite Visual Studio Code e l'estensione Azure IoT Edge.
1. In Visual Studio Code selezionare **Visualizza** > **Terminale integrato** per aprire il terminale integrato di Visual Studio Code.
2. Selezionare **Visualizza** > **Riquadro comandi** per aprire il riquadro comandi di VS Code. 
3. Nel riquadro comandi digitare ed eseguire il comando **Azure: Sign in** (Azure: Accedi) e seguire le istruzioni per accedere all'account Azure. Se è stato già effettuato l'accesso, è possibile ignorare questo passaggio.
4. Nel riquadro comandi digitare ed eseguire il comando **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nuova soluzione IoT Edge). Nel riquadro comandi immettere le informazioni seguenti per creare la soluzione: 
   1. Selezionare la cartella in cui si vuole creare la soluzione. 
   2. Specificare un nome per la soluzione o accettare quello predefinito **EdgeSolution**.
   3. Scegliere **C Module** (Modulo C) come modello di modulo. 
   4. Assegnare al modulo il nome **CModule**. 
   5. Specificare il registro contenitori di Azure creato nella sezione precedente come repository di immagini per il primo modulo. Sostituire **localhost:5000** con il valore del server di accesso copiato. La stringa finale è simile a **\<nome registro\>.azurecr.io/cmodule**.
 
4. La finestra di VS Code carica l'area di lavoro della soluzione IoT Edge. Sono presenti una cartella **modules**, una cartella **.vscode**, un file modello del manifesto della distribuzione e un file **ENV**. Il codice del modulo predefinito viene implementato come modulo di pipe. 

5. Per filtrare i messaggi in formato JSON, è necessario importare una libreria JSON per C. È possibile scegliere qualsiasi libreria JSON o scrivere una libreria personalizzata per analizzare il codice JSON nel modulo C. La procedura seguente usa [Parson](https://github.com/kgabis/parson) come esempio.
   1. Scaricare **parson.c** e **parson.h** dal [repository Parson di Github](https://github.com/kgabis/parson). Copiare e incollare quindi questi due file nella cartella **CModule**.
   2. Aprire **modules** > **CModule** > **CMakeLists.txt**. Aggiungere le righe seguenti per importare la libreria parson come my_parson.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. In `target_link_libraries` in **CMakeLists.txt** aggiungere `my_parson`.
   4. Aprire **modules** > **CModule** > **main.c**. Nella parte finale della sezione include aggiungere il codice seguente per includere `parson.h` per il supporto per JSON:

      ```c
      #include "parson.h"
      ```

6. Aggiungere una variabile globale `temperatureThreshold` dopo la sezione include. Questa variabile imposta il valore che la temperatura misurata deve superare per inviare i dati all'hub IoT. 

    ```c
    static double temperatureThreshold = 25;
    ```

7. Sostituire l'intera funzione `CreateMessageInstance` con il codice seguente. Questa funzione alloca un contesto per il callback. 

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

            if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
            {
                free(messageInstance);
                messageInstance = NULL;
            }
            else
            {
                messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
                MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
                if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
                {
                    printf("ERROR: Map_AddOrUpdate Failed!\r\n");
                }
            }
        }

        return messageInstance;
    }
    ```

8. Sostituire l'intera funzione `InputQueue1Callback` con il codice seguente. Questa funzione implementa i filtri effettivi per i messaggi. 

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n", 
                messagesReceivedByInput1Queue, messageBody);

        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

9. Aggiungere una funzione `moduleTwinCallback`. Questo metodo riceve gli aggiornamenti della proprieta desiderata dal modulo gemello e aggiorna la variabile **temperatureThreshold**. Tutti i moduli hanno un modulo gemello che consente di configurare il codice in esecuzione all'interno di un modulo direttamente dal cloud.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n", 
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

10. Aggiungere un nuovo callback per `moduleTwinCallback` nella funzione `SetupCallbacksForModule`. Sostituire l'intera funzione `SetupCallbacksForModule` con il codice seguente.

    ```c
    static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
    {
        int ret;

        if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else
        {
            ret = 0;
        }

        return ret;
    }
    ```

11. Salvare questo file.

## <a name="build-your-iot-edge-solution"></a>Compilare la soluzione IoT Edge

Nella sezione precedente è creata una soluzione IoT Edge ed è stato aggiunto a CModule il codice per filtrare i messaggi in cui la temperatura del computer segnalata è inferiore alla soglia accettabile. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori. 

1. Accedere a Docker immettendo il comando seguente nel terminale integrato di Visual Studio Code, per poter eseguire il push dell'immagine del modulo nel record di controllo di accesso: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Usare il nome utente, la password e il server di accesso copiati dal registro contenitori di Azure nella prima sezione oppure recuperarli nuovamente dalla sezione **Chiavi di accesso** del registro nel portale di Azure.

2. Nello strumento di esplorazione di VS Code aprire il file **deployment.template.json** nell'area di lavoro della soluzione IoT Edge. Questo file comunica a `$edgeAgent` di distribuire due moduli: **tempSensor** e **CModule**. Il valore `CModule.image` è impostato su una versione di Linux amd64 dell'immagine. Per altre informazioni sui manifesti di distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).

3. Nel file **deployment.template.json** è presente una sezione **registryCredentials** in cui sono archiviate le credenziali del registro Docker. Le coppie effettive di username e password vengono archiviate nel file ENV ignorato da Git.

4. Aggiungere il modulo gemello CModule al manifesto della distribuzione. Inserire il contenuto JSON seguente alla fine della sezione `moduleContent`, dopo il modulo gemello `$edgeHub`: 
    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Salvare questo file.
5. Nello strumento di esplorazione di VS Code fare clic con il pulsante destro del mouse sul file **deployment.template.json** e scegliere **Build IoT Edge solution** (Compila soluzione IoT Edge). 

Quando si comunica a Visual Studio Code di compilare la soluzione, prima di tutto con le informazioni del modello di distribuzione viene generato un file `deployment.json` in una nuova cartella **config**. Vengono quindi eseguiti due comandi nel terminale integrato: `docker build` e `docker push`. Questi due comandi compilano il codice, includono `CModule.dll` in un contenitore e ne eseguono il push nel registro contenitori specificato quando è stata inizializzata la soluzione. 

È possibile visualizzare l'indirizzo completo dell'immagine del contenitore con tag nel terminale integrato di VS Code. L'indirizzo dell'immagine è costituito dalle informazioni presenti nel file `module.json`, nel formato **\<repository\>:\<versione\>-\<piattaforma\>**. Per questa esercitazione, il risultato sarà simile a **registryname.azurecr.io/cmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Distribuire ed eseguire la soluzione

1. Configurare l'estensione Azure IoT Toolkit con la stringa di connessione per l'hub IoT: 
    1. Aprire lo strumento di esplorazione di Visual Studio Code selezionando **Visualizza** > **Explorer**. 
    2. Nello strumento di esplorazione fare clic su **AZURE IOT HUB DEVICES** (DISPOSITIVI DELL'HUB IOT DI AZURE) e quindi fare clic su **...**. Fare clic su **Select IoT Hub** (Seleziona l'hub IoT). Seguire le istruzioni per accedere all'account Azure e scegliere l'hub IoT. 
       Si noti che è anche possibile eseguire la configurazione facendo clic su **Set IoT Hub Connection String** (Configura la stringa di connessione dell'hub IoT). Immettere la stringa di connessione per l'hub IoT a cui si connette il dispositivo IoT Edge nella finestra popup.

2. Fare clic con il pulsante destro del mouse sul dispositivo IoT Edge nell'apposito strumento di esplorazione per l'hub IoT di Azure, quindi scegliere **Create Deployment for IoT Edge device** (Crea la distribuzione per il dispositivo IoT Edge). Selezionare il file **deployment.json** nella cartella **config** e quindi fare clic su **Select Edge Deployment Manifest** (Seleziona il manifesto della distribuzione di Edge).

3. Fare clic sul pulsante Aggiorna. Dovrebbe essere visualizzato il nuovo **CModule** in esecuzione insieme al modulo **TempSensor** e a **$edgeAgent** e **$edgeHub**. 

## <a name="view-generated-data"></a>Visualizzare i dati generati

1. Per monitorare i dati in arrivo all'hub IoT, fare clic su **...** e selezionare **Start Monitoring D2C Messages** (Avvia il monitoraggio dei messaggi D2C).
2. Per monitorare il messaggio D2C per un dispositivo specifico, fare clic con il pulsante destro del mouse sul dispositivo nell'elenco e scegliere **Start Monitoring D2C Messages** (Avvia il monitoraggio dei messaggi D2C).
3. Per arrestare il monitoraggio dei dati, eseguire il comando **Azure IoT Hub: Stop monitoring D2C message** (Hub IoT di Azure: Interrompi il monitoraggio del messaggio D2C) nel riquadro comandi. 
4. Per visualizzare o aggiornare il modulo gemello, fare clic con il pulsante destro del mouse sul modulo nell'elenco e scegliere **Edit module twin** (Modifica il modulo gemello). Per aggiornare il modulo gemello, salvare il file JSON gemello e fare clic con il pulsante destro del mouse sull'area degli editor e scegliere **Update Module Twin** (Aggiorna il modulo gemello).
5. Per visualizzare i log di Docker, è possibile installare [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) per VS Code e trovare i moduli in esecuzione in locale nello strumento di esplorazione di Docker. Dal menu di scelta rapida scegliere **Mostra log** per visualizzarli nel terminale integrato.
 
## <a name="clean-up-resources"></a>Pulire le risorse 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Se si continua con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni già create e riutilizzarle.

In caso contrario, è possibile eliminare le configurazioni locali e le risorse di Azure create in questo articolo per evitare addebiti. 

> [!IMPORTANT]
> L'eliminazione delle risorse di Azure e del gruppo di risorse è irreversibile. Dopo l'eliminazione, il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se si è creato l'hub IoT all'interno di un gruppo di risorse esistente che contiene risorse che si vogliono conservare, eliminare solo la risorsa dell'hub IoT invece di eliminare il gruppo di risorse.
>

Per eliminare solo l'hub IoT, eseguire il comando seguente usando il nome dell'hub e il nome del gruppo di risorse:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Per eliminare l'intero gruppo di risorse per nome:

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.

2. Nella casella di testo **Filtra per nome...** immettere il nome del gruppo di risorse che contiene l'hub IoT. 

3. A destra del gruppo di risorse nell'elenco dei risultati fare clic su **...** quindi su **Elimina gruppo di risorse**.

4. Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere di nuovo il nome del gruppo di risorse per confermare e fare clic su **Elimina**. Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge contenente il codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. Quando si è pronti per compilare moduli personalizzati, per altre informazioni vedere [Develop a C module with Azure IoT Edge for Visual Studio Code](how-to-develop-c-module.md) (Sviluppare un modulo C con Azure IoT Edge per Visual Studio Code). È possibile continuare con le esercitazioni successive per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Archiviare dati sul perimetro con database di SQL Server](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
