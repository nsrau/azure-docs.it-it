---
title: 'Esercitazione: Sviluppare un modulo C per Linux - Azure IoT Edge| Microsoft Docs'
description: Questa esercitazione illustra come creare un modulo per IoT Edge con codice C e distribuirlo in un dispositivo Linux che esegui IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 63169423e757f3e1e73a95a1523d74c8fc59b2b2
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835117"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Esercitazione: Sviluppare un modulo IoT Edge in C per dispositivi Linux

Usare Visual Studio Code per sviluppare codice C e distribuirlo in un dispositivo Linux che esegue Azure IoT Edge. 

È possibile usare i moduli di IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare Visual Studio Code per creare un modulo IoT Edge in C
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla nel registro contenitori
> * Distribuire il modulo nel dispositivo IoT Edge
> * Visualizzare i dati generati

Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo quando la temperatura è superiore a una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Ambito della soluzione

Questa esercitazione illustra come sviluppare un modulo in **C** usando **Visual Studio Code** e come distribuirlo in un **dispositivo Linux**. Se si sviluppano moduli per dispositivi Windows, vedere invece [Sviluppare un modulo IoT Edge in C per dispositivi Windows](tutorial-c-module-windows.md).

Usare la tabella seguente per informazioni sulle opzioni disponibili per lo sviluppo e la distribuzione di moduli C in Linux: 

| C | Visual Studio Code | Visual Studio | 
| - | ------------------ | ------------- |
| **Linux AMD64** | ![Usare Visual Studio Code per i moduli C su Linux AMD64](./media/tutorial-c-module/green-check.png) | ![Usare Visual Studio per i moduli C su Linux AMD64](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Usare Visual Studio Code per i moduli C su Linux ARM32](./media/tutorial-c-module/green-check.png) | ![Usare Visual Studio per i moduli C su Linux ARM32](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione è necessario aver completato l'esercitazione precedente per configurare l'ambiente di sviluppo per i contenitori Linux: [Sviluppare moduli IoT Edge per i dispositivi Linux](tutorial-develop-for-linux.md). Completando tale esercitazione, saranno soddisfatti i prerequisiti seguenti: 

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Un [dispositivo Linux che esegue Azure IoT Edge](quickstart-linux.md)
* Un registro contenitori, ad esempio [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurato con [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurato per eseguire i contenitori Linux.

Per sviluppare un modulo IoT Edge in C, installare i prerequisiti aggiuntivi seguenti nel computer di sviluppo: 

* [Estensione C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) per Visual Studio Code.

## <a name="create-a-module-project"></a>Creare un progetto di modulo

La procedura seguente consente di creare un progetto di modulo IoT Edge per C tramite Visual Studio Code e l'estensione Azure IoT Tools. Dopo aver creato il modello di progetto, aggiungere nuovo codice in modo che il modulo filtri i messaggi in base alle relative proprietà segnalate. 

### <a name="create-a-new-project"></a>Creare un nuovo progetto

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

### <a name="add-your-registry-credentials"></a>Aggiungere le credenziali del registro

Il file dell'ambiente archivia le credenziali per il registro contenitori e le condivide con il runtime IoT Edge. Queste credenziali sono necessarie al runtime per eseguire il pull delle immagini private nel dispositivo IoT Edge.

1. Nello strumento di esplorazione di Visual Studio Code aprire il file con estensione env.
2. Aggiornare i campi con i valori di **nome utente** e **password** copiati dal registro contenitori di Azure.
3. Salvare questo file.

### <a name="select-your-target-architecture"></a>Selezionare l'architettura di destinazione

Attualmente, Visual Studio Code può sviluppare moduli C per dispositivi Linux AMD64 e Linux ARM32v7. È necessario selezionare l'architettura di destinazione per ogni soluzione, perché il contenitore viene creato ed eseguito in modo diverso in base al tipo di architettura. L'impostazione predefinita è Linux AMD64. 

1. Aprire il riquadro comandi e cercare **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Azure IoT Edge: Imposta la piattaforma di destinazione predefinita per la soluzione Edge) oppure selezionare l'icona del collegamento sulla barra laterale nella parte inferiore della finestra. 

2. Nel riquadro comandi selezionare l'architettura di destinazione nell'elenco di opzioni. Per questa esercitazione si usa una macchina virtuale Ubuntu come dispositivo IoT Edge, quindi si manterrà il valore predefinito **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aggiornare il modulo con il codice personalizzato

Il codice del modulo predefinito riceve i messaggi in una coda di input e li passa attraverso una coda di output. Si aggiungerà ora del codice in modo che il modulo elabori i messaggi nel dispositivo Edge prima di inoltrarli all'hub IoT. Aggiornare il modulo in modo che analizzi i dati della temperatura in ogni messaggio e invii solo il messaggio all'hub IoT se la temperatura supera una determinata soglia. 

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

1. Trovare la funzione `CreateMessageInstance` in main.c. Sostituire l'istruzione if-else interna con il codice seguente che aggiunge alcune righe di funzionalità: 

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

1. Sostituire l'intera funzione `InputQueue1Callback` con il codice seguente. Questa funzione implementa i filtri effettivi per i messaggi. Quando viene ricevuto un messaggio, verifica se la temperatura segnalata supera la soglia. In caso affermativo, inoltra il messaggio tramite la relativa coda di output. In caso negativo, ignora il messaggio. 

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

1. Trovare la funzione `SetupCallbacksForModule`. Sostituire la funzione con il codice seguente che aggiunge un'istruzione **else if** per verificare se il modulo gemello è stato aggiornato.

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

1. Nello strumento di esplorazione di VS Code aprire il file **deployment.template.json** nell'area di lavoro della soluzione IoT Edge. 

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

## <a name="build-and-push-your-module"></a>Creare il modulo ed eseguirne il push

Nella sezione precedente è stata creata una soluzione IoT Edge ed è stato aggiunto a CModule il codice per filtrare i messaggi in cui la temperatura del computer segnalata è compresa nei limiti accettabili. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori.

1. Aprire il terminale di VS Code selezionando **Visualizza** > **Terminale**.

1. Accedere a Docker immettendo il comando seguente nel terminale. Accedere con il nome utente, la password e il server di accesso del registro contenitori di Azure. È possibile recuperare questi valori dalla sezione **Chiavi di accesso** del registro nel portale di Azure.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Potrebbe venire visualizzato un avviso di sicurezza in cui si consiglia l'uso di `--password-stdin`. Sebbene si tratti di una procedura consigliata per gli scenari di produzione, esula dell'ambito di questa esercitazione. Per altri dettagli, vedere le informazioni di riferimento sull'[accesso a docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

2. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file **deployment.template.json** e scegliere **Build and Push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge).

   Il comando di creazione e push avvia tre operazioni. Prima di tutto, crea una nuova cartella nella soluzione denominata **config** che contiene il manifesto completo della distribuzione, basato sulle informazioni del modello di distribuzione e di altri file della soluzione. In secondo luogo, esegue `docker build` per creare l'immagine del contenitore in base al documento dockerfile appropriato per l'architettura di destinazione. Infine, esegue `docker push` per eseguire il push del repository di immagini nel registro contenitori.

## <a name="deploy-modules-to-device"></a>Distribuire i moduli nel dispositivo

Usare Esplora risorse di Visual Studio Code e l'estensione Azure IoT Tools per distribuire il progetto di modulo nel dispositivo IoT Edge. Il manifesto della distribuzione, il file **deployment.json**, è già disponibile per questo scenario nella cartella config. Ora è sufficiente selezionare un dispositivo che riceverà la distribuzione.

Assicurarsi che il dispositivo IoT Edge sia in esecuzione. 

1. In Esplora risorse di Visual Studio Code espandere la sezione **Azure IoT Hub Devices** (Dispositivi dell'hub IoT di Azure).

2. Fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge, quindi selezionare **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo).

3. Selezionare il file **deployment.json** nella cartella **config** e quindi fare clic su **Select Edge Deployment Manifest** (Seleziona il manifesto della distribuzione di Edge). Non usare il file deployment.template.json.

4. Fare clic sul pulsante Aggiorna. Dovrebbe essere visualizzato il nuovo **CModule** in esecuzione insieme al modulo **TempSensor** e a **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Visualizzare i dati generati

Dopo aver applicato il manifesto della distribuzione al dispositivo IoT Edge, il runtime di IoT Edge nel dispositivo raccoglie le informazioni della nuova distribuzione e si avvia all'interno di questa. Tutti i moduli in esecuzione nel dispositivo che non sono inclusi nel manifesto della distribuzione vengono arrestati. Tutti i moduli mancanti dal dispositivo vengono avviati.

È possibile visualizzare lo stato del dispositivo IoT Edge tramite la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure) della finestra di esplorazione di Visual Studio Code. Espandere i dettagli del dispositivo per visualizzare un elenco dei moduli distribuiti e in esecuzione.

1. In Esplora risorse di Visual Studio Code fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge e scegliere **Avvia monitoraggio messaggio D2C**.

2. Visualizzare i messaggi in arrivo nell'hub IoT. L'arrivo dei messaggi potrebbe richiedere del tempo, perché il dispositivo IoT Edge deve ricevere la nuova distribuzione e avviare tutti i moduli. Quindi, a causa delle modifiche apportate, il codice CModule aspetta che la temperatura del computer raggiunga i 25 gradi prima di inviare messaggi. Ai messaggi che indicano il raggiungimento della soglia di temperatura viene inoltre aggiunto il tipo di messaggio **Alert**. 

   ![Visualizzare i messaggi in arrivo nell'hub IoT](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Modificare il modulo gemello

Si è usato il modulo gemello CModule nel manifesto della distribuzione per impostare la soglia di temperatura su 25 gradi. È possibile usare il modulo gemello per cambiare la funzionalità senza dover aggiornare il codice del modulo.

1. In Visual Studio Code espandere i dettagli relativi al dispositivo IoT Edge per vedere i moduli in esecuzione. 

2. Fare clic con il pulsante destro del mouse su **CModule** e scegliere **Modifica modulo gemello**. 

3. Trovare **TemperatureThreshold** nelle proprietà desiderate. Sostituire il valore con una nuova temperatura più alta di 5-10 gradi rispetto all'ultima temperatura segnalata. 

4. Salvare il file del modulo gemello.

5. Fare clic con il pulsante destro del mouse in qualsiasi punto del riquadro di modifica del modulo gemello e scegliere **Update module twin** (Aggiorna modulo gemello). 

5. Monitorare i messaggi da dispositivo a cloud in ingresso. L'arrivo dei messaggi dovrebbe interrompersi finché non viene raggiunta la nuova soglia di temperatura. 

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test.

In caso contrario, è possibile eliminare le configurazioni locali e le risorse di Azure usate in questo articolo per evitare addebiti.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge contenente il codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. Quando si è pronti a creare moduli personalizzati, si può passare agli argomenti su come [sviluppare moduli IoT Edge personalizzati](module-development.md) o [sviluppare moduli con Visual Studio Code](how-to-vs-code-develop-module.md). Continuare con le esercitazioni successive per informazioni sul modo in cui Azure IoT Edge semplifica la distribuzione di servizi cloud di Azure per elaborare e analizzare i dati nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Funzioni](tutorial-deploy-function.md)
> [Analisi di flusso](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Servizio Visione personalizzata](tutorial-deploy-custom-vision.md)

