---
title: 'Esercitazione: Sviluppare moduli C# per Windows con Azure IoT Edge'
description: Questa esercitazione illustra come creare un modulo per IoT Edge con codice C# e distribuirlo in un dispositivo IoT Edge di Windows.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 041efc62b32e8d8c0c477d9d5715882fd7899cd9
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701936"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Esercitazione: Sviluppare un modulo IoT Edge in C# per dispositivi Windows

Usare Visual Studio per sviluppare codice C# e distribuirlo in un dispositivo Windows che esegue Azure IoT Edge. 

È possibile usare i moduli di Azure IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore. In questa esercitazione si apprenderà come:    

> [!div class="checklist"]
> * Usare Visual Studio per creare un modulo IoT Edge basato su C# SDK.
> * Usare Visual Studio e Docker per creare un'immagine Docker e pubblicarla nel registro.
> * Distribuire il modulo nel dispositivo IoT Edge.
> * Visualizzare i dati generati.

Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo quando la temperatura è superiore a una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Ambito della soluzione

Questa esercitazione illustra come sviluppare un modulo in **C#** usando **Visual Studio 2019** e come distribuirlo in un **dispositivo Windows**. Se si sviluppano moduli per dispositivi Linux, vedere invece [Sviluppare un modulo IoT Edge in C# per dispositivi Linux](tutorial-csharp-module.md). 

Usare la tabella seguente per informazioni sulle opzioni disponibili per lo sviluppo e la distribuzione di moduli C# per dispositivi Windows: 

| C# | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Sviluppo di Windows AMD64** | ![Sviluppare moduli C# per WinAMD64 in Visual Studio Code](./media/tutorial-c-module/green-check.png) | ![Sviluppare moduli C# per WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Debug di Windows AMD64** |   | ![Eseguire il debug di moduli C# per WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione è necessario aver completato l'esercitazione precedente per configurare l'ambiente di sviluppo, [Sviluppare un modulo IoT Edge per un dispositivo Windows](tutorial-develop-for-windows.md). Dopo aver completato questa esercitazione, saranno già stati soddisfatti i prerequisiti seguenti: 

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Un [dispositivo Windows che esegue Azure IoT Edge](quickstart.md).
* Un registro contenitori, ad esempio [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) configurato con l'estensione [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) configurato per eseguire i contenitori Windows.

> [!TIP]
> Se si usa Visual Studio 2017 (versione 15.7 o successiva), scaricare e installare [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) per Visual Studio 2017 da Visual Studio Marketplace

## <a name="create-a-module-project"></a>Creare un progetto di modulo

La procedura seguente consente di creare un progetto di modulo IoT Edge tramite Visual Studio Code e l'estensione Azure IoT Edge Tools. Dopo aver creato il modello di progetto, aggiungere nuovo codice in modo che il modulo filtri i messaggi in base alle relative proprietà segnalate. 

### <a name="create-a-new-project"></a>Creare un nuovo progetto

Azure IoT Edge Tools offre modelli di progetto per tutti i linguaggi dei moduli IoT Edge supportati in Visual Studio. Questi modelli includono il codice e tutti i file necessari per distribuire un modulo funzionante per testare IoT Edge oppure offrono un punto di partenza per personalizzare il modello con la propria logica di business. 

1. Avviare Visual Studio 2019 e selezionare **Crea nuovo progetto**.

2. Nella finestra per il nuovo progetto cercare **IoT Edge** e scegliere il progetto **Azure IoT Edge (Windows amd64)** . Fare clic su **Avanti**. 

   ![Creare un nuovo progetto Azure IoT Edge](./media/tutorial-csharp-module-windows/new-project.png)

3. Nella finestra Configura il nuovo progetto assegnare al progetto e alla soluzione un nuovo nome descrittivo, ad esempio **CSharpTutorialApp**. Fare clic su **Crea** per creare il progetto. 

   ![Configurare un nuovo progetto Azure IoT Edge](./media/tutorial-csharp-module-windows/configure-project.png)

4. Nella finestra relativa a modulo e applicazione IoT Edge configurare il progetto con i valori seguenti: 

   | Campo | Valore |
   | ----- | ----- |
   | Selezionare un modello: | Selezionare **C# Module** (Modulo C#). | 
   | Module project name (Nome progetto modulo) | Assegnare al modulo il nome **CSharpModule**. | 
   | Docker image repository (Repository immagini Docker) | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore è prepopolata in base al valore del nome del progetto di modulo. Sostituire **localhost:5000** con il valore del server di accesso in Registro Azure Container. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure. <br><br> Il repository di immagini finale sarà simile a \<nome registro\>.azurecr.io/csharpmodule. |

   ![Configurare il progetto per il dispositivo di destinazione, il tipo di modulo e il registro contenitori](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Selezionare **Aggiungi** per creare il progetto. 

### <a name="add-your-registry-credentials"></a>Aggiungere le credenziali del registro

Il manifesto della distribuzione condivide le credenziali per il registro contenitori con il runtime IoT Edge. Queste credenziali sono necessarie al runtime per eseguire il pull delle immagini private nel dispositivo IoT Edge. Usare le credenziali della sezione **Chiavi di accesso** del Registro Azure Container. 

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

1. In Visual Studio aprire **CSharpModule** > **Program.cs**.

2. Nella parte superiore dello spazio dei nomi **CSharpModule** aggiungere tre istruzioni **using** per i tipi che verranno usati in un secondo momento:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Aggiungere la variabile **temperatureThreshold** alla classe **Program** dopo la variabile counter. La variabile temperatureThreshold imposta il valore che la temperatura misurata deve superare perché vengano inviati dati all'hub IoT. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Aggiungere le classi **MessageBody**, **Machine** e **Ambient** alla classe **Program** dopo le dichiarazioni di variabili. Queste classi definiscono lo schema previsto per il corpo dei messaggi in arrivo.

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

5. Trovare il metodo **Init**. Questo metodo crea e configura l'oggetto **ModuleClient**, che consente al modulo di connettersi al runtime locale di Azure IoT Edge per inviare e ricevere messaggi. Il codice registra anche un callback per la ricezione di messaggi da un hub IoT Edge tramite l'endpoint**input1**.

   Sostituire l'intero metodo Init con il codice seguente:
   
   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```
   
   Questo metodo Init aggiornato configura ancora la connessione al runtime di IoT Edge con ModuleClient, ma aggiunge anche nuove funzionalità. Legge le proprietà desiderate del modulo gemello per recuperare il valore di **temperatureThreshold**. Quindi crea un callback che resta in ascolto di futuri aggiornamenti per le proprietà desiderate del modulo gemello. Con questo callback, è possibile aggiornare in remoto la soglia della temperatura nel modulo gemello e le modifiche verranno incorporate nel modulo. 

   Il metodo Init aggiornato cambia anche il metodo **SetInputMessageHandlerAsync** esistente. Nell'esempio di codice i messaggi in arrivo in *input1* vengono elaborati con la funzione *PipeMessage*, ma è necessario usare invece la funzione *FilterMessages* che verrà creata nei passaggi seguenti. 

6. Aggiungere un nuovo metodo **onDesiredPropertiesUpdate** alla classe **Program**. Questo metodo riceve gli aggiornamenti della proprieta desiderata dal modulo gemello e aggiorna la variabile **temperatureThreshold**. Tutti i moduli hanno un modulo gemello che consente di configurare il codice in esecuzione all'interno di un modulo direttamente dal cloud.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. Rimuovere il metodo **PipeMessage** di esempio e sostituirlo con il nuovo metodo **FilterMessages**. Questo metodo viene chiamato ogni volta che l'hub di IoT Edge invia un messaggio al modulo. Filtra i messaggi con un valore della temperatura inferiore alla soglia relativa alla temperatura configurata tramite il modulo gemello. Aggiunge inoltre la proprietà **MessageType** al messaggio con il valore impostato su **Avviso**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using(var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Salvare il file Program.cs.

9. Aprire il file **deployment.template.json** nella soluzione IoT Edge. Questo file indica all'agente di IoT Edge quali moduli distribuire, in questo caso **SimulatedTemperatureSensor** e **CSharpModule**, e indica all'hub di IoT Edge come indirizzare i messaggi tra i moduli.

10. Aggiungere il modulo gemello **CSharpModule** al manifesto della distribuzione. Inserire il contenuto JSON seguente alla fine della sezione **modulesContent** dopo il modulo gemello **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Aggiungere il modulo gemello al modello di distribuzione](./media/tutorial-csharp-module-windows/module-twin.png)

11. Salvare il file deployment.template.json.


## <a name="build-and-push-your-module"></a>Creare il modulo ed eseguirne il push

Nella sezione precedente è creata una soluzione IoT Edge ed è stato aggiunto a **CSharpModule** il codice per filtrare i messaggi in cui la temperatura del computer segnalata è inferiore alla soglia accettabile. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori. 

1. Usare il comando seguente per accedere a Docker nel computer di sviluppo. Usare il nome utente, la password e il server di accesso del Registro Azure Container. È possibile recuperare questi valori dalla sezione **Chiavi di accesso** del registro nel portale di Azure.

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

3. Visualizzare i messaggi in arrivo nell'hub IoT. L'arrivo dei messaggi può richiedere del tempo, perché a causa delle modifiche apportate, il codice CSharpModule aspetta che la temperatura del computer raggiunta i 25 gradi prima di inviare messaggi. Ai messaggi che indicano il raggiungimento della soglia di temperatura viene inoltre aggiunto il tipo di messaggio **Alert**. 

   ![Visualizzare i messaggi in arrivo nell'hub IoT](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Modificare il modulo gemello

Il modulo gemello CSharpModule è stato usato per impostare la soglia della temperatura su 25 gradi. È possibile usare il modulo gemello per cambiare la funzionalità senza dover aggiornare il codice del modulo.

1. In Visual Studio aprire il file **deployment.windows-amd64.json**. (Non il file deployment.template). Se il manifesto della distribuzione non viene visualizzato nel file config di Esplora soluzioni, selezionare l'icona **Mostra tutti i file** sulla barra degli strumenti.

2. Trovare il modulo gemello CSharpModule e impostare il valore del parametro **temperatureThreshold** su una nuova temperatura da 5 a 10 gradi più alta rispetto all'ultima segnalata. 

3. Salvare il file **deployment.windows-amd64.json**.

4. Seguire di nuovo i passaggi di distribuzione per applicare il manifesto della distribuzione aggiornato al dispositivo. 

5. Monitorare i messaggi da dispositivo a cloud in ingresso. L'arrivo dei messaggi dovrebbe interrompersi finché non viene raggiunta la nuova soglia di temperatura. 

## <a name="clean-up-resources"></a>Pulire le risorse 

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test. 

In caso contrario, è possibile eliminare le configurazioni locali e le risorse di Azure usate in questo articolo per evitare addebiti. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge con codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. Quando si è pronti a creare moduli personalizzati, si può passare agli argomenti su come [sviluppare moduli IoT Edge personalizzati](module-development.md) o [sviluppare moduli con Visual Studio](how-to-visual-studio-develop-module.md). Per esempi di moduli di IoT Edge, incluso il modulo per le temperature simulate, vedere gli [esempi di moduli di IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules). 


Continuare con le esercitazioni successive per informazioni sul modo in cui Azure IoT Edge semplifica la distribuzione di servizi cloud di Azure per elaborare e analizzare i dati nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Funzioni](tutorial-deploy-function.md)
> [Analisi di flusso](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Servizio Visione personalizzata](tutorial-deploy-custom-vision.md)
