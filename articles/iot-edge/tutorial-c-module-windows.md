---
title: 'Esercitazione: Sviluppare moduli C per Windows - Azure IoT Edge | Microsoft Docs'
description: Questa esercitazione illustra come creare un modulo per IoT Edge con codice C e distribuirlo in un dispositivo Windows che esegui IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 49f853341edab7c7dc92f72472b81f7fb22c0ad8
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808764"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Esercitazione: Sviluppare un modulo IoT Edge in C per dispositivi Windows

Usare Visual Studio per sviluppare codice C e distribuirlo in un dispositivo Windows che esegue Azure IoT Edge. 

È possibile usare i moduli di Azure IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore. In questa esercitazione si apprenderà come:    

> [!div class="checklist"]
> * Usare Visual Studio per creare un modulo IoT Edge basato sull'SDK per C.
> * Usare Visual Studio e Docker per creare un'immagine Docker e pubblicarla nel registro.
> * Distribuire il modulo nel dispositivo IoT Edge.
> * Visualizzare i dati generati.

Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo quando la temperatura è superiore a una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Ambito della soluzione

Questa esercitazione illustra come sviluppare un modulo in **C** usando **Visual Studio 2019** e come distribuirlo in un **dispositivo Windows**. Se si sviluppano moduli per dispositivi Linux, vedere invece [Sviluppare un modulo IoT Edge in C per dispositivi Linux](tutorial-c-module.md). 

Usare la tabella seguente per informazioni sulle opzioni disponibili per lo sviluppo e la distribuzione di moduli C per dispositivi Windows: 

| C | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Sviluppare moduli C per WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione è necessario aver completato l'esercitazione precedente per configurare l'ambiente di sviluppo per i contenitori Windows: [Sviluppare moduli IoT Edge per dispositivi Windows](tutorial-develop-for-windows.md). Dopo aver completato questa esercitazione, saranno soddisfatti i prerequisiti seguenti: 

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Un [dispositivo Windows che esegue Azure IoT Edge](quickstart.md).
* Un registro contenitori, ad esempio [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) configurato con l'estensione [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* [Docker CE](https://docs.docker.com/install/) configurato per eseguire i contenitori Windows.
* Azure IoT SDK per C. 

> [!TIP]
> Se si usa Visual Studio 2017 (versione 15.7 o successiva), scaricare e installare [Azure IoT Edge Tools (anteprima)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) per Visual Studio 2017 da Visual Studio Marketplace.

## <a name="create-a-module-project"></a>Creare un progetto di modulo

La procedura seguente consente di creare un progetto di modulo IoT Edge basato sull'SDK per C usando Visual Studio e l'estensione Azure IoT Edge Tools. Dopo aver creato il modello di progetto, aggiungere nuovo codice in modo che il modulo filtri i messaggi in base alle relative proprietà segnalate. 

### <a name="create-a-new-project"></a>Creare un nuovo progetto

Creare un modello di soluzione C che è possibile personalizzare con il proprio codice.

1. Avviare Visual Studio 2019 e selezionare **Crea nuovo progetto**.

2. Nella finestra per il nuovo progetto cercare **IoT Edge** e scegliere il progetto **Azure IoT Edge (Windows amd64)** . Fare clic su **Avanti**. 

   ![Creare un nuovo progetto Azure IoT Edge](./media/tutorial-c-module-windows/new-project.png)

3. Nella finestra Configura il nuovo progetto assegnare al progetto e alla soluzione un nuovo nome descrittivo, ad esempio **CTutorialApp**. Fare clic su **Crea** per creare il progetto. 

   ![Configurare un nuovo progetto Azure IoT Edge](./media/tutorial-c-module-windows/configure-project.png)

4. Nella finestra relativa a modulo e applicazione IoT Edge configurare il progetto con i valori seguenti: 

   | Campo | Valore |
   | ----- | ----- |
   | Selezionare un modello: | Selezionare **C Module** (Modulo C). | 
   | Module project name (Nome progetto modulo) | Assegnare al modulo il nome **CModule**. | 
   | Docker image repository (Repository immagini Docker) | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore è prepopolata in base al valore del nome del progetto di modulo. Sostituire **localhost:5000** con il valore del server di accesso in Registro Azure Container. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure. <br><br> Il repository di immagini finale sarà simile a \<nome registro\>.azurecr.io/cmodule. |

   ![Configurare il progetto per il dispositivo di destinazione, il tipo di modulo e il registro contenitori](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Selezionare **OK** per applicare le modifiche. 

### <a name="add-your-registry-credentials"></a>Aggiungere le credenziali del registro

Il manifesto della distribuzione condivide le credenziali per il registro contenitori con il runtime IoT Edge. Queste credenziali sono necessarie al runtime per eseguire il pull delle immagini private nel dispositivo IoT Edge. Usare le credenziali della sezione **Chiavi di accesso** del registro contenitori di Azure. 

1. In Esplora soluzioni di Visual Studio aprire il file **deployment.template.json**. 

2. Trovare la proprietà **registryCredentials** nelle proprietà desiderate di $edgeAgent. 

3. Aggiornare la proprietà con le credenziali, usando questo formato: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Salvare il file deployment.template.json. 

### <a name="update-the-module-with-custom-code"></a>Aggiornare il modulo con il codice personalizzato

Il codice del modulo predefinito riceve i messaggi in una coda di input e li passa attraverso una coda di output. Si aggiungerà ora del codice in modo che il modulo elabori i messaggi nel dispositivo Edge prima di inoltrarli all'hub IoT. Aggiornare il modulo in modo che analizzi i dati della temperatura in ogni messaggio e invii solo il messaggio all'hub IoT se la temperatura supera una determinata soglia. 


1. I dati del sensore in questo scenario sono disponibili in formato JSON. Per filtrare i messaggi in formato JSON, importare una libreria JSON per C. Questa esercitazione usa Parson.

   1. Scaricare il [repository Parson di GitHub](https://github.com/kgabis/parson). Copiare i file **parson.c** e **parson.h** nel progetto **CModule**.

   2. In Visual Studio aprire il file **CMakeLists.txt** dalla cartella del progetto CModule. All'inizio del file importare i file Parson come libreria denominata **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Aggiungere **my_parson** all'elenco di librerie nella sezione **target_link_libraries** del file CMakeLists.txt.

   4. Salvare il file **CMakeLists.txt**.

   5. Aprire **CModule** > **main.c**. Alla fine dell'elenco di istruzioni include aggiungerne una nuova per includere `parson.h` per il supporto JSON:

      ```c
      #include "parson.h"
      ```

2.  Nel file **main.c** aggiungere una variabile globale denominata `temperatureThreshold` accanto alla variabile messagesReceivedByInput1Queue. Questa variabile imposta il valore che la temperatura misurata deve superare per inviare i dati all'hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Trovare la funzione `CreateMessageInstance` in main.c. Sostituire l'istruzione if-else interna con il codice seguente che aggiunge alcune righe di funzionalità: 

   ```c
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
   ```

   Le nuove righe di codice nell'istruzione else aggiungono una nuova proprietà al messaggio, che lo etichetta come avviso. Questo codice etichetta tutti i messaggi come avvisi, perché verrà aggiunta una funzionalità che invia i messaggi all'hub IoT solo se segnalano temperature elevate. 

4. Trovare la funzione `InputQueue1Callback` e sostituirla interamente con il codice seguente. Questa funzione implementa i filtri effettivi per i messaggi. Quando viene ricevuto un messaggio, verifica se la temperatura segnalata supera la soglia. In caso affermativo, inoltra il messaggio tramite la relativa coda di output. In caso negativo, ignora il messaggio. 

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

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds threshold, send to output1
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
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. Aggiungere una funzione `moduleTwinCallback`. Questo metodo riceve gli aggiornamenti della proprieta desiderata dal modulo gemello e aggiorna la variabile **temperatureThreshold**. Tutti i moduli hanno un modulo gemello che consente di configurare il codice in esecuzione all'interno di un modulo direttamente dal cloud.

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

6. Trovare la funzione `SetupCallbacksForModule`. Sostituire la funzione con il codice seguente che aggiunge un'istruzione **else if** per verificare se il modulo gemello è stato aggiornato. 

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

7. Salvare il file main.c.

8. Aprire il file **deployment.template.json**. 

9. Aggiungere il modulo gemello CModule al manifesto della distribuzione. Inserire il contenuto JSON seguente alla fine della sezione `moduleContent`, dopo il modulo gemello `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Aggiungere CModule gemello al modello di distribuzione](./media/tutorial-c-module-windows/module-twin.png)

1. Salvare il file **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Creare il modulo ed eseguirne il push

Nella sezione precedente è stata creata una soluzione IoT Edge ed è stato aggiunto codice a **CModule** per filtrare i messaggi in cui la temperatura segnalata del computer è inferiore alla soglia accettabile. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori. 

1. Usare il comando seguente per accedere a Docker nel computer di sviluppo. Accedere con il nome utente, la password e il server di accesso del registro contenitori di Azure. È possibile recuperare questi valori dalla sezione **Chiavi di accesso** del registro nel portale di Azure.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Potrebbe venire visualizzato un avviso di sicurezza in cui si consiglia l'uso di `--password-stdin`. Sebbene si tratti di una procedura consigliata per gli scenari di produzione, esula dell'ambito di questa esercitazione. Per altri dettagli, vedere le informazioni di riferimento sull'[accesso a docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

2. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto da creare. Il nome predefinito è **AzureIotEdgeApp1** e poiché si sta creando un modulo Windows l'estensione dovrà essere **Windows.Amd64**. 

3. Selezionare **Build and push IoT Edge modules** (Compila moduli IoT Edge ed esegui il push). 

   Il comando di creazione e push avvia tre operazioni. Prima di tutto, crea una nuova cartella nella soluzione denominata **config** che contiene il manifesto completo della distribuzione, basato sulle informazioni del modello di distribuzione e di altri file della soluzione. In secondo luogo, esegue `docker build` per creare l'immagine del contenitore in base al documento dockerfile appropriato per l'architettura di destinazione. Infine, esegue `docker push` per eseguire il push del repository di immagini nel registro contenitori. 

## <a name="deploy-modules-to-device"></a>Distribuire i moduli nel dispositivo

Usare Cloud Explorer di Visual Studio e l'estensione Azure IoT Edge Tools per distribuire il progetto di modulo nel dispositivo IoT Edge. Il manifesto della distribuzione, il file **deployment.json**, è già disponibile per questo scenario nella cartella config. Ora è sufficiente selezionare un dispositivo che riceverà la distribuzione.

Assicurarsi che il dispositivo IoT Edge sia in esecuzione. 

1. In Cloud Explorer di Visual Studio espandere le risorse per visualizzare l'elenco di dispositivi IoT. 

2. Fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge che dovrà ricevere la distribuzione. 

3. Scegliere **Crea distribuzione**.

4. In Esplora file selezionare il file **deployment.windows-amd64** nella cartella di configurazione della soluzione. 

5. Aggiornare Cloud Explorer per visualizzare i moduli distribuiti elencati sotto il dispositivo. 


## <a name="view-generated-data"></a>Visualizzare i dati generati

Dopo aver applicato il manifesto della distribuzione al dispositivo IoT Edge, il runtime di IoT Edge nel dispositivo raccoglie le informazioni della nuova distribuzione e si avvia all'interno di questa. Tutti i moduli in esecuzione nel dispositivo che non sono inclusi nel manifesto della distribuzione vengono arrestati. Tutti i moduli mancanti dal dispositivo vengono avviati. 

È possibile usare l'estensione IoT Edge Tools per visualizzare i messaggi che arrivano nell'hub IoT. 

1. In Cloud Explorer di Visual Studio selezionare il nome del dispositivo IoT Edge. 

2. Nell'elenco **Azioni** selezionare **Start Monitoring Built-in Event Endpoint** (Avvia monitoraggio endpoint eventi predefinito). 

3. Visualizzare i messaggi in arrivo nell'hub IoT. L'arrivo dei messaggi potrebbe richiedere del tempo, perché il dispositivo IoT Edge deve ricevere la nuova distribuzione e avviare tutti i moduli. Quindi, a causa delle modifiche apportate, il codice CModule aspetta che la temperatura del computer raggiunga i 25 gradi prima di inviare messaggi. Ai messaggi che indicano il raggiungimento della soglia di temperatura viene inoltre aggiunto il tipo di messaggio **Alert**. 

   ![Visualizzare i messaggi in arrivo nell'hub IoT](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Modificare il modulo gemello

Il modulo gemello CModule è stato usato per impostare la soglia della temperatura su 25 gradi. È possibile usare il modulo gemello per cambiare la funzionalità senza dover aggiornare il codice del modulo.

1. In Visual Studio aprire il file **deployment.windows-amd64.json**. (Non il file deployment.template). Se il manifesto della distribuzione non viene visualizzato nel file config di Esplora soluzioni, selezionare l'icona **Mostra tutti i file** sulla barra degli strumenti.

2. Trovare il modulo gemello CModule e impostare il valore del parametro **temperatureThreshold** su una nuova temperatura da 5 a 10 gradi più alta rispetto all'ultima segnalata. 

3. Salvare il file **deployment.windows-amd64.json**.

4. Seguire di nuovo i passaggi di distribuzione per applicare il manifesto della distribuzione aggiornato al dispositivo. 

5. Monitorare i messaggi da dispositivo a cloud in ingresso. L'arrivo dei messaggi dovrebbe interrompersi finché non viene raggiunta la nuova soglia di temperatura. 

## <a name="clean-up-resources"></a>Pulire le risorse 

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test. 

In caso contrario, è possibile eliminare le configurazioni locali e le risorse di Azure usate in questo articolo per evitare addebiti. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge con codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. Quando si è pronti a creare moduli personalizzati, si può passare agli argomenti su come [sviluppare moduli IoT Edge personalizzati](module-development.md) o [sviluppare moduli con Visual Studio](how-to-visual-studio-develop-module.md). Continuare con le esercitazioni successive per informazioni sul modo in cui Azure IoT Edge semplifica la distribuzione di servizi cloud di Azure per elaborare e analizzare i dati nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Funzioni](tutorial-deploy-function.md)
> [Analisi di flusso](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Servizio Visione personalizzata](tutorial-deploy-custom-vision.md)
