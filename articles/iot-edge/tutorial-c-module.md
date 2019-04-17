---
title: 'Esercitazione: creare un modulo C personalizzato - Azure IoT Edge | Microsoft Docs'
description: Questa esercitazione illustra come creare un modulo per IoT Edge con codice C e distribuirlo in un dispositivo perimetrale.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: eeaff4769dba5b6e6951665d09cd12d13f22af07
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273713"
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

* è possibile usare una macchina virtuale di Azure come dispositivo IoT Edge seguendo la procedura illustrata nell'argomento di avvio rapido per dispositivi [Linux](quickstart-linux.md) o [Windows](quickstart.md). 

   >[!TIP]
   >Questa esercitazione usa Visual Studio Code per sviluppare un modulo C con i contenitori Linux. Per sviluppare in C per i contenitori Windows, è necessario usare Visual Studio 2017. Per altre informazioni, vedere [Usare Visual Studio 2017 per sviluppare ed eseguire il debug di moduli per Azure IoT Edge](how-to-visual-studio-develop-module.md).

Risorse cloud:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.

Risorse per lo sviluppo:

* [Visual Studio Code](https://code.visualstudio.com/).
* [Estensione C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) per Visual Studio Code.
* [Strumenti di Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per Visual Studio Code.
* [Docker CE](https://docs.docker.com/install/).

## <a name="create-a-container-registry"></a>Creare un registro contenitori

In questa esercitazione vengono usati gli strumenti di Azure IoT per Visual Studio Code per creare un modulo e un'**immagine del contenitore** dai file. Eseguire quindi il push dell'immagine in un **registro** che archivia e gestisce le immagini. Distribuire infine l'immagine dal registro nel dispositivo IoT Edge.

È possibile usare qualsiasi registro compatibile con Docker per inserire le immagini dei contenitori. Due servizi di registro Docker molto diffusi sono [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/) e [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Questa esercitazione usa il Registro Azure Container.

Se non è ancora disponibile alcun registro contenitori, seguire questa procedura per crearne uno nuovo in Azure:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Contenitori** > **Registro Container**.

2. Specificare i valori seguenti per creare il registro contenitori:

   | Campo | Valore |
   | ----- | ----- |
   | Nome registro | Specificare un nome univoco. |
   | Sottoscrizione | Selezionare una sottoscrizione nell'elenco a discesa. |
   | Gruppo di risorse | È consigliabile usare lo stesso gruppo di risorse per tutte le risorse di test create durante le esercitazioni e le guide introduttive di IoT Edge. Ad esempio, **IoTEdgeResources**. |
   | Località | Scegliere una località vicina. |
   | Utente amministratore | Impostare su **Abilita**. |
   | SKU | Selezionare **Basic**. |

5. Selezionare **Create**.

6. Dopo aver creato il registro contenitori, passare al registro e quindi selezionare **Chiavi di accesso**.

7. Copiare i valori nei campi **Server di accesso**, **Nome utente** e **Password**. Usare questi valori più avanti nell'esercitazione per fornire l'accesso al registro contenitori.

## <a name="create-an-iot-edge-module-project"></a>Creare un progetto di modulo IoT Edge
La procedura seguente illustra come creare un progetto di modulo di IoT Edge basato su .NET core 2.0 tramite Visual Studio Code e gli strumenti di Azure IoT.

### <a name="create-a-new-solution"></a>Creare una nuova soluzione

Creare un modello di soluzione C che è possibile personalizzare con il proprio codice.

1. Selezionare **Visualizza** > **Riquadro comandi** per aprire il riquadro comandi di VS Code.

2. Nel riquadro comandi digitare ed eseguire il comando **Azure: Accedere** e seguire le istruzioni per accedere all'account Azure. Se è stato già effettuato l'accesso, è possibile ignorare questo passaggio.

3. Nel riquadro comandi digitare ed eseguire il comando **Azure IoT Edge: Nuova soluzione IoT Edge**. Seguire i prompt nel riquadro comandi per creare la soluzione.

   | Campo | Valore |
   | ----- | ----- |
   | Selezionare la cartella | Nel computer di sviluppo scegliere la posizione in cui Visual Studio Code dovrà creare i file della soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione oppure accettare quello predefinito **EdgeSolution**. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **C Module** (Modulo C). |
   | Provide a module name (Specificare un nome per il modulo) | Assegnare al modulo il nome **CModule**. |
   | Provide Docker image repository for the module (Specificare il repository di immagini Docker per il modulo) | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore viene preinserita in base al nome specificato nell'ultimo passaggio. Sostituire **localhost:5000** con il valore del server di accesso in Registro Azure Container. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure. <br><br> Il repository di immagini finale sarà simile a \<nome registro\>.azurecr.io/cmodule. |
 
   ![Specificare il repository di immagini Docker](./media/tutorial-c-module/repository.png)

La finestra di VS Code carica l'area di lavoro della soluzione IoT Edge con i cinque componenti di livello superiore. La cartella **modules** contiene il codice C per il modulo e i Dockerfile per creare il modulo come un'immagine del contenitore. Il file **\.env** archivia le credenziali del registro contenitori. Il file **deployment.template.json** contiene le informazioni che il runtime IoT Edge usa per distribuire i moduli in un dispositivo. Il file **deployment.debug.template.json** contiene invece la versione di debug dei moduli. In questa esercitazione non verranno modificati né la cartella **\.vscode** né il file **\.gitignore**.

Se non è stato specificato un registro contenitori durante la creazione della soluzione, ma è stato accettato il valore predefinito localhost:5000, non sarà presente il file con estensione \. env.

<!--
   ![C solution workspace](./media/tutorial-c-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Aggiungere le credenziali del registro

Il file dell'ambiente archivia le credenziali per il registro contenitori e le condivide con il runtime IoT Edge. Queste credenziali sono necessarie al runtime per eseguire il pull delle immagini private nel dispositivo IoT Edge.

1. Nello strumento di esplorazione di Visual Studio Code aprire il file con estensione env.
2. Aggiornare i campi con i valori di **nome utente** e **password** copiati dal registro contenitori di Azure.
3. Salvare questo file.

### <a name="update-the-module-with-custom-code"></a>Aggiornare il modulo con il codice personalizzato

Aggiungere il codice al modulo C che gli consente di controllare se la temperatura del computer indicata ha superato una soglia sicura. Se la temperatura è troppo alta, il modulo aggiunge un parametro di avviso al messaggio prima di inviare i dati all'hub IoT. 

1. I dati del sensore in questo scenario sono disponibili in formato JSON. Per filtrare i messaggi in formato JSON, importare una libreria JSON per C. Questa esercitazione usa Parson.

   1. Scaricare il [repository Parson di GitHub](https://github.com/kgabis/parson). Copiare i file **parson.c** e **parson.h** nella cartella **CModule**.

   2. Aprire **modules** > **CModule** > **CMakeLists.txt**. All'inizio del file importare i file Parson come libreria denominata **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Aggiungere **my_parson** all'elenco di librerie nella funzione **target_link_libraries** di CMakeLists.txt.

   4. Salvare il file **CMakeLists.txt**.

   5. Aprire **modules** > **CModule** > **main.c**. Alla fine dell'elenco di istruzioni include aggiungerne una nuova per includere `parson.h` per il supporto JSON:

      ```c
      #include "parson.h"
      ```

1. Nel file **main.c** aggiungere una variabile globale denominata `temperatureThreshold` dopo la sezione include. Questa variabile imposta il valore che la temperatura misurata deve superare per inviare i dati all'hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Sostituire l'intera funzione `CreateMessageInstance` con il codice seguente. Questa funzione alloca un contesto per il callback.

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

1. Sostituire l'intera funzione `InputQueue1Callback` con il codice seguente. Questa funzione implementa i filtri effettivi per i messaggi.

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

1. Aggiungere una funzione `moduleTwinCallback`. Questo metodo riceve gli aggiornamenti della proprieta desiderata dal modulo gemello e aggiorna la variabile **temperatureThreshold**. Tutti i moduli hanno un modulo gemello che consente di configurare il codice in esecuzione all'interno di un modulo direttamente dal cloud.

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

1. Sostituire la funzione `SetupCallbacksForModule` con il codice seguente.

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

1. Salvare il file main.c.

1. Nello strumento di esplorazione di VS Code aprire il file **deployment.template.json** nell'area di lavoro della soluzione IoT Edge. Questo file indica all'agente di IoT Edge quali moduli distribuire, in questo caso **tempSensor** e **CModule**, e indica all'hub di IoT Edge come indirizzare i messaggi tra i moduli. L'estensione di Visual Studio Code popola automaticamente la maggior parte delle informazioni necessarie nel modello di distribuzione, ma occorre assicurarsi che le informazioni siano appropriate per la soluzione specifica: 

   1. La piattaforma predefinita del dispositivo IoT Edge è impostata su **amd64** nella barra di stato di VS Code. **CModule** è quindi impostato sulla versione Linux amd64 dell'immagine. Cambiare la piattaforma predefinita nella barra di stato da **amd64** a **arm32v7** se corrisponde all'architettura del dispositivo IoT Edge. 

      ![Aggiornare la piattaforma di immagini del modulo](./media/tutorial-c-module/image-platform.png)

   2. Verificare che il modello abbia il nome modulo corretto, non il nome **SampleModule** predefinito che è stato modificato durante la creazione della soluzione IoT Edge.

   3. La sezione **registryCredentials** archivia le credenziali del registro Docker, in modo che l'agente di IoT Edge possa eseguire il pull dell'immagine del modulo. Le coppie effettive di username e password vengono archiviate nel file ENV, che viene ignorato da Git. Aggiungere le credenziali al file con estensione env, se non è già stato fatto.  

   4. Per altre informazioni sui manifesti della distribuzione, vedere [Informazioni su come distribuire moduli e definire route in IoT Edge](module-composition.md).

1. Aggiungere il modulo gemello CModule al manifesto della distribuzione. Inserire il contenuto JSON seguente alla fine della sezione `moduleContent`, dopo il modulo gemello `$edgeHub`:

   ```json
       "CModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Aggiungere CModule gemello al modello di distribuzione](./media/tutorial-c-module/module-twin.png)

1. Salvare il file **deployment.template.json**.

## <a name="build-and-push-your-solution"></a>Compilare ed eseguire il push della soluzione

Nella sezione precedente è stata creata una soluzione IoT Edge ed è stato aggiunto a CModule il codice per filtrare i messaggi in cui la temperatura del computer segnalata è compresa nei limiti accettabili. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori.

1. Aprire il terminale integrato di VS Code selezionando **Visualizza** > **Terminale**.

1. Accedere a Docker immettendo il comando seguente nel terminale integrato di Visual Studio Code. È necessario accedere con le credenziali di Registro Azure Container in modo che sia possibile eseguire il push dell'immagine del modulo nel registro.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Usare il nome utente, la password e il server di accesso copiati da Registro Azure Container nella prima sezione oppure recuperarli nuovamente dalla sezione **Chiavi di accesso** del registro nel portale di Azure.

2. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file **deployment.template.json** e scegliere **Build and Push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge).

Quando si comunica a Visual Studio Code di compilare la soluzione, prima di tutto viene generato un file `deployment.json` in una nuova cartella **config**. Le informazioni per il file deployment.json vengono raccolte dal file del modello aggiornato, dal file ENV usato per archiviare le credenziali del registro contenitori e dal file module.json nella cartella CModule.

Visual Studio Code esegue quindi due comandi nel terminale integrato: `docker build` e `docker push`. Questi due comandi compilano il codice, includono `CModule.dll` in un contenitore e ne eseguono il push nel registro contenitori specificato quando è stata inizializzata la soluzione.

È possibile visualizzare l'indirizzo completo dell'immagine del contenitore con tag nel terminale integrato di VS Code. L'indirizzo dell'immagine è costituito dalle informazioni presenti nel file `module.json`, nel formato **\<repository\>:\<versione\>-\<piattaforma\>**. Per questa esercitazione, il risultato sarà simile a **myregistry.azurecr.io/cmodule:0.0.1-amd64**.

>[!TIP]
>Se quando si prova a compilare ed eseguire il push del modulo viene visualizzato un errore, effettuare i controlli seguenti:
>* L'accesso a Docker in Visual Studio Code è stato eseguito con le credenziali del registro contenitori? Queste credenziali sono diverse rispetto a quelle usate per accedere al portale di Azure.
>* Il repository di contenitori è corretto? Aprire **modules** > **cmodule** > **module.json** e trovare il campo **repository**. Il repository di immagini sarà simile a **\<nomeregistro\>.azurecr.io/cmodule**. 
>* Si sta compilando lo stesso tipo di contenitori eseguito dal computer di sviluppo? L'impostazione predefinita in Visual Studio Code è costituita dai contenitori Linux amd64. Se il computer di sviluppo esegue contenitori Linux arm32v7, aggiornare la piattaforma sulla barra di stato blu nella parte inferiore della finestra di VS Code in modo che corrisponda alla piattaforma dei contenitori. I moduli C non possono essere compilati come contenitori Windows. 

## <a name="deploy-and-run-the-solution"></a>Distribuire ed eseguire la soluzione

Nell'articolo della guida introduttiva usato per configurare il dispositivo IoT Edge è stato distribuito un modulo usando il portale di Azure. È possibile distribuire i moduli anche usando l'estensione Azure hub IoT Toolkit (in precedenza estensione Azure IoT Toolkit) per Visual Studio Code. Il manifesto della distribuzione, il file **deployment.json**, è già disponibile per questo scenario. Ora è sufficiente selezionare un dispositivo che riceverà la distribuzione.

1. Nel riquadro comandi di VS Code eseguire **hub IoT di Azure: Selezionare l'hub IoT**.

2. Scegliere la sottoscrizione e l'hub IoT che contiene il dispositivo IoT Edge che si vuole configurare.

3. Nello strumento di esplorazione di VS Code espandere la sezione **Azure IoT Hub dispositivi** (Dispositivi dell'hub IoT di Azure).

4. Fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge, quindi selezionare **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo).

   ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/tutorial-c-module/create-deployment.png)

5. Selezionare il file **deployment.json** nella cartella **config** e quindi fare clic su **Select Edge Deployment Manifest** (Seleziona il manifesto della distribuzione di Edge). Non usare il file deployment.template.json.

6. Fare clic sul pulsante Aggiorna. Dovrebbe essere visualizzato il nuovo **CModule** in esecuzione insieme al modulo **TempSensor** e a **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Visualizzare i dati generati

Dopo aver applicato il manifesto della distribuzione al dispositivo IoT Edge, il runtime di IoT Edge nel dispositivo raccoglie le informazioni della nuova distribuzione e si avvia all'interno di questa. Tutti i moduli in esecuzione nel dispositivo che non sono inclusi nel manifesto della distribuzione vengono arrestati. Tutti i moduli mancanti dal dispositivo vengono avviati.

È possibile visualizzare lo stato del dispositivo IoT Edge tramite la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure) della finestra di esplorazione di Visual Studio Code. Espandere i dettagli del dispositivo per visualizzare un elenco dei moduli distribuiti e in esecuzione.

Nel dispositivo IoT Edge stesso è possibile visualizzare lo stato dei moduli della distribuzione usando il comando `iotedge list`. Devono essere presenti quattro moduli: i due moduli di runtime di IoT Edge, tempSensor e il modulo personalizzato creato in questa esercitazione. L'avvio di tutti i moduli può richiedere qualche minuto. Rieseguire quindi il comando se inizialmente non sono visualizzati tutti.

Per visualizzare i messaggi generati da un qualsiasi modulo, usare il comando `iotedge logs <module name>`.

È possibile visualizzare i messaggi man mano che arrivano nell'hub IoT tramite Visual Studio Code.

1. Per monitorare i dati in arrivo all'hub IoT, fare clic su **...** e selezionare **Start Monitoring D2C Messages** (Avvia il monitoraggio dei messaggi D2C).
2. Per monitorare il messaggio D2C per un dispositivo specifico, fare clic con il pulsante destro del mouse sul dispositivo nell'elenco e scegliere **Start Monitoring D2C Messages** (Avvia il monitoraggio dei messaggi D2C).
3. Per interrompere il monitoraggio dei dati, eseguire il comando **hub IoT di Azure: Interrompere il monitoraggio di messaggi D2C** nel riquadro comandi.
4. Per visualizzare o aggiornare il modulo gemello, fare clic con il pulsante destro del mouse sul modulo nell'elenco e scegliere **Edit module twin** (Modifica il modulo gemello). Per aggiornare il modulo gemello, salvare il file JSON gemello e fare clic con il pulsante destro del mouse sull'area degli editor e scegliere **Update Module Twin** (Aggiorna il modulo gemello).
5. Per visualizzare i log di Docker, è possibile installare [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) per VS Code e trovare i moduli in esecuzione in locale nello strumento di esplorazione di Docker. Dal menu di scelta rapida scegliere **Mostra log** per visualizzarli nel terminale integrato.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test.

In caso contrario, è possibile eliminare le risorse di Azure e le configurazioni locali create in questo articolo per evitare addebiti.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge contenente il codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. Quando si è pronti per compilare moduli personalizzati, per altre informazioni vedere [Develop a C module with Azure IoT Edge for Visual Studio Code](how-to-develop-c-module.md) (Sviluppare un modulo C con Azure IoT Edge per Visual Studio Code). È possibile continuare con le esercitazioni successive per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Archiviare dati sul perimetro con database di SQL Server](tutorial-store-data-sql-server.md)

