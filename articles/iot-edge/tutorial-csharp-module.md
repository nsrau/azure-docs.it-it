---
title: 'Esercitazione: creare un modulo C# personalizzato - Azure IoT Edge | Microsoft Docs'
description: Questa esercitazione illustra come creare un modulo per IoT Edge con codice C# e distribuirlo in un dispositivo perimetrale.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: f4afd83e31cf724e734b4e86045f8404e65753c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58088027"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Esercitazione: Sviluppare un modulo C# per IoT Edge e distribuirlo in un dispositivo simulato

È possibile usare i moduli di Azure IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore. Verrà usato il dispositivo IoT Edge simulato che è stato creato nelle guide introduttive relative alla distribuzione di Azure IoT Edge in un dispositivo simulato in [Windows](quickstart.md) e [Linux](quickstart-linux.md). In questa esercitazione si apprenderà come:    

> [!div class="checklist"]
> * Usare Visual Studio Code per creare un modulo di IoT Edge basato su .NET Core 2.1 SDK.
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla nel registro.
> * Distribuire il modulo nel dispositivo IoT Edge.
> * Visualizzare i dati generati.

>[!NOTE]
>È anche possibile usare [Visual Studio 2017 per sviluppare, distribuire ed eseguire il debug di moduli di IoT Edge](how-to-visual-studio-develop-csharp-module.md).

Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo quando la temperatura è superiore a una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

* È possibile usare il computer per lo sviluppo o una macchina virtuale come dispositivo perimetrale seguendo la procedura illustrata nella guida introduttiva per dispositivi [Linux](quickstart-linux.md) o [Windows](quickstart.md).

Risorse cloud:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure. 

Risorse per lo sviluppo:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Strumenti di Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per Visual Studio Code. 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/)


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
La procedura seguente consente di creare un progetto di modulo di IoT Edge basato su .NET Core 2.0 SDK tramite Visual Studio Code e gli strumenti di Azure IoT.

### <a name="create-a-new-solution"></a>Creare una nuova soluzione

Creare un modello di soluzione C# che è possibile personalizzare con il proprio codice. 

1. In Visual Studio Code selezionare **Visualizza** > **Riquadro comandi** per aprire il riquadro comandi di VS Code. 

2. Nel riquadro comandi immettere ed eseguire il comando **Azure: Accedere** e seguire le istruzioni per accedere all'account Azure. Se è stato già effettuato l'accesso, è possibile ignorare questo passaggio.

3. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: Nuova soluzione IoT Edge**. Seguire i prompt nel riquadro comandi per creare la soluzione.

   | Campo | Valore |
   | ----- | ----- |
   | Selezionare la cartella | Nel computer di sviluppo scegliere la posizione in cui Visual Studio Code dovrà creare i file della soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione oppure accettare quello predefinito **EdgeSolution**. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **C# Module** (Modulo C#). |
   | Provide a module name (Specificare un nome per il modulo) | Assegnare al modulo il nome **CSharpModule**. |
   | Provide Docker image repository for the module (Specificare il repository di immagini Docker per il modulo) | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore viene preinserita in base al nome specificato nell'ultimo passaggio. Sostituire **localhost:5000** con il valore del server di accesso in Registro Azure Container. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure. <br><br>Il repository di immagini finale sarà simile a \<nome registro\>.azurecr.io/csharpmodule. |
 
   ![Specificare il repository di immagini Docker](./media/tutorial-csharp-module/repository.png)

La finestra di VS Code carica l'area di lavoro della soluzione IoT Edge. L'area di lavoro della soluzione contiene cinque componenti di livello superiore. La cartella **modules** contiene il codice C# per il modulo e i Dockerfile per creare il modulo come un'immagine del contenitore. Il file **\.env** archivia le credenziali del registro contenitori. Il file **deployment.template.json** contiene le informazioni che il runtime IoT Edge usa per distribuire i moduli in un dispositivo. Il file **deployment.debug.template.json** contiene invece la versione di debug dei moduli. In questa esercitazione non verranno modificati né la cartella **\.vscode** né il file **\.gitignore**.

Se non è stato specificato un registro contenitori durante la creazione della soluzione, ma è stato accettato il valore predefinito localhost:5000, non sarà presente il file con estensione \. env. 

<!--
   ![C# solution workspace](./media/tutorial-csharp-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Aggiungere le credenziali del registro

Il file dell'ambiente archivia le credenziali per il registro contenitori e le condivide con il runtime IoT Edge. Queste credenziali sono necessarie al runtime per eseguire il pull delle immagini private nel dispositivo IoT Edge. 

1. Nello strumento di esplorazione di Visual Studio Code aprire il file con estensione env. 
2. Aggiornare i campi con i valori di **nome utente** e **password** copiati dal registro contenitori di Azure. 
3. Salvare questo file. 

### <a name="update-the-module-with-custom-code"></a>Aggiornare il modulo con il codice personalizzato

1. Nello strumento di esplorazione di VS Code aprire **modules** > **CSharpModule** > **Program.cs**.

2. Nella parte superiore dello spazio dei nomi **CSharpModule** aggiungere tre istruzioni **using** per i tipi che verranno usati in un secondo momento:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Aggiungere la variabile **temperatureThreshold** alla classe **Program**. Questa variabile imposta il valore che la temperatura misurata deve superare per inviare i dati all'hub IoT. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Aggiungere le classi **MessageBody**, **Machine** e **Ambient** alla classe **Program**. Queste classi definiscono lo schema previsto per il corpo dei messaggi in arrivo.

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

5. Nel metodo **Init** il codice crea e configura un oggetto **ModuleClient**. Questo oggetto consente al modulo di connettersi al runtime locale di Azure IoT Edge per inviare e ricevere messaggi. La stringa di connessione usata nel metodo **Init** viene fornita al modulo dal runtime di IoT Edge. Dopo la creazione di **ModuleClient**, il codice legge il valore **temperatureThreshold** dalle proprietà desiderate del modulo gemello. Il codice registra un callback per la ricezione di messaggi da un hub IoT Edge tramite l'endpoint**input1**. Sostituire il metodo **SetInputMessageHandlerAsync** con un nuovo metodo e aggiungere un metodo **SetDesiredPropertyUpdateCallbackAsync** per gli aggiornamenti alle proprietà desiderate. Per apportare questa modifica, sostituire l'ultima riga del metodo **Init** con il codice seguente:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Aggiungere il metodo **onDesiredPropertiesUpdate** alla classe **Program**. Questo metodo riceve gli aggiornamenti della proprieta desiderata dal modulo gemello e aggiorna la variabile **temperatureThreshold**. Tutti i moduli hanno un modulo gemello che consente di configurare il codice in esecuzione all'interno di un modulo direttamente dal cloud.

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

7. Sostituire il metodo **PipeMessage** con il metodo **FilterMessages**. Questo metodo viene chiamato ogni volta che l'hub di IoT Edge invia un messaggio al modulo. Filtra i messaggi con un valore della temperatura inferiore alla soglia relativa alla temperatura configurata tramite il modulo gemello. Aggiunge inoltre la proprietà **MessageType** al messaggio con il valore impostato su **Avviso**. 

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
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await moduleClient.SendEventAsync("output1", filteredMessage);
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

9. Nello strumento di esplorazione di VS Code aprire il file **deployment.template.json** nell'area di lavoro della soluzione IoT Edge. Questo file indica all'agente di IoT Edge quali moduli distribuire, in questo caso **tempSensor** e **CSharpModule**, e indica all'hub di IoT Edge come indirizzare i messaggi tra i moduli. L'estensione di Visual Studio Code popola automaticamente la maggior parte delle informazioni necessarie nel modello di distribuzione, ma occorre assicurarsi che le informazioni siano appropriate per la soluzione specifica: 

   1. La piattaforma predefinita del dispositivo IoT Edge è impostata su **amd64** nella barra di stato di VS Code, di conseguenza **CSharpModule** è impostato sulla versione amd64 Linux dell'immagine. Cambiare la piattaforma predefinita nella barra di stato da **amd64** a **arm32v7** o **windows-amd64** se corrisponde all'architettura del dispositivo IoT Edge. 

      ![Aggiornare la piattaforma di immagini del modulo](./media/tutorial-csharp-module/image-platform.png)

   2. Verificare che il modello abbia il nome modulo corretto, non il nome **SampleModule** predefinito che è stato modificato durante la creazione della soluzione IoT Edge.

   3. La sezione **registryCredentials** archivia le credenziali del registro Docker, in modo che l'agente di IoT Edge possa eseguire il pull dell'immagine del modulo. Le coppie effettive di username e password vengono archiviate nel file ENV, che viene ignorato da Git. Aggiungere le credenziali al file con estensione env, se non è già stato fatto.  

   4. Per altre informazioni sui manifesti della distribuzione, vedere [Informazioni su come distribuire moduli e definire route in IoT Edge](module-composition.md).

10. Aggiungere il modulo gemello **CSharpModule** al manifesto della distribuzione. Inserire il contenuto JSON seguente alla fine della sezione **modulesContent** dopo il modulo gemello **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Aggiungere il modulo gemello al modello di distribuzione](./media/tutorial-csharp-module/module-twin.png)

11. Salvare il file deployment.template.json.


## <a name="build-your-iot-edge-solution"></a>Compilare la soluzione IoT Edge

Nella sezione precedente è creata una soluzione IoT Edge ed è stato aggiunto a **CSharpModule** il codice per filtrare i messaggi in cui la temperatura del computer segnalata è inferiore alla soglia accettabile. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori. 

1. Accedere a Docker immettendo il comando seguente nel terminale integrato di Visual Studio Code. È quindi possibile eseguire il push dell'immagine del modulo nel registro contenitori di Azure.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Usare il nome utente, la password e il server di accesso copiati dal registro contenitori di Azure nella prima sezione. È anche possibile recuperare questi valori dalla sezione **Chiavi di accesso** del registro nel portale di Azure.

2. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file deployment.template.json e scegliere **Build and Push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge). 

Quando si comunica a Visual Studio Code di compilare la soluzione, prima di tutto con le informazioni del modello di distribuzione viene generato un file deployment.json in una nuova cartella denominata **config**. Vengono quindi eseguiti due comandi nel terminale integrato: `docker build` e `docker push`. Questi due comandi compilano il codice, includono CSharpModule.dll in contenitori e ne eseguono il push nel registro contenitori specificato quando è stata inizializzata la soluzione. 

È possibile visualizzare l'indirizzo completo dell'immagine del contenitore con tag nel terminale integrato di VS Code. L'indirizzo dell'immagine è costituito dalle informazioni presenti nel file module.json nel formato \<repository\>:\<versione\>-\<piattaforma\>. Per questa esercitazione, il risultato sarà simile a registryname.azurecr.io/csharpmodule:0.0.1-amd64.

>[!TIP]
>Se quando si prova a compilare ed eseguire il push del modulo viene visualizzato un errore, effettuare i controlli seguenti:
>* L'accesso a Docker in Visual Studio Code è stato eseguito con le credenziali del registro contenitori? Queste credenziali sono diverse rispetto a quelle usate per accedere al portale di Azure.
>* Il repository di contenitori è corretto? Aprire **modules** > **cmodule** > **module.json** e trovare il campo **repository**. Il repository di immagini sarà simile a **\<nomeregistro\>.azurecr.io/csharpmodule**. 
>* Si sta compilando lo stesso tipo di contenitori eseguito dal computer di sviluppo? L'impostazione predefinita in Visual Studio Code è costituita dai contenitori Linux amd64. Se il computer di sviluppo esegue contenitori Windows o Linux arm32v7, aggiornare la piattaforma sulla barra di stato blu nella parte inferiore della finestra di VS Code in modo che corrisponda alla piattaforma dei contenitori.

## <a name="deploy-and-run-the-solution"></a>Distribuire ed eseguire la soluzione

Nell'articolo della guida introduttiva usato per configurare il dispositivo IoT Edge è stato distribuito un modulo usando il portale di Azure. È possibile distribuire i moduli anche usando l'estensione Azure hub IoT Toolkit (in precedenza estensione Azure IoT Toolkit) per Visual Studio Code. Il manifesto della distribuzione, il file **deployment.json**, è già disponibile per questo scenario. Ora è sufficiente selezionare un dispositivo che riceverà la distribuzione.

1. Nel riquadro comandi di VS Code eseguire **hub IoT di Azure: Selezionare l'hub IoT**. 

2. Scegliere la sottoscrizione e l'hub IoT che contiene il dispositivo IoT Edge che si vuole configurare. 

3. Nello strumento di esplorazione di VS Code espandere la sezione **Azure IoT Hub dispositivi** (Dispositivi dell'hub IoT di Azure). 

4. Fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge, quindi selezionare **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo). 

   ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/tutorial-csharp-module/create-deployment.png)

5. Selezionare il file **deployment.json** nella cartella **config** e quindi fare clic su **Select Edge Deployment Manifest** (Seleziona il manifesto della distribuzione di Edge). Non usare il file deployment.template.json. 

6. Fare clic sul pulsante Aggiorna. Dovrebbe essere visualizzato il nuovo **CSharpModule** in esecuzione insieme al modulo **TempSensor** e a **$edgeAgent** e **$edgeHub**.  

## <a name="view-generated-data"></a>Visualizzare i dati generati

Dopo aver applicato il manifesto della distribuzione al dispositivo IoT Edge, il runtime di IoT Edge nel dispositivo raccoglie le informazioni della nuova distribuzione e si avvia all'interno di questa. Tutti i moduli in esecuzione nel dispositivo che non sono inclusi nel manifesto della distribuzione vengono arrestati. Tutti i moduli mancanti dal dispositivo vengono avviati. 

È possibile visualizzare lo stato del dispositivo IoT Edge tramite la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure) della finestra di esplorazione di Visual Studio Code. Espandere i dettagli del dispositivo per visualizzare un elenco dei moduli distribuiti e in esecuzione. 

Nel dispositivo IoT Edge stesso è possibile visualizzare lo stato dei moduli della distribuzione usando il comando `iotedge list`. Devono essere presenti quattro moduli: i due moduli di runtime di IoT Edge, tempSensor e il modulo personalizzato creato in questa esercitazione. L'avvio di tutti i moduli può richiedere qualche minuto. Rieseguire quindi il comando se inizialmente non sono visualizzati tutti. 

Per visualizzare i messaggi generati da un qualsiasi modulo, usare il comando `iotedge logs <module name>`. 

È possibile visualizzare i messaggi man mano che arrivano nell'hub IoT tramite Visual Studio Code. 

1. Per monitorare i dati che arrivano all'hub IoT, selezionare i puntini di sospensione (**...**) e quindi selezionare **Start Monitoring D2C Messages** (Avvia il monitoraggio dei messaggi D2C).
2. Per monitorare il messaggio D2C per un dispositivo specifico, fare clic con il pulsante destro del mouse sul dispositivo nell'elenco e scegliere **Start Monitoring D2C Messages** (Avvia il monitoraggio dei messaggi D2C).
3. Per interrompere il monitoraggio dei dati, eseguire il comando **hub IoT di Azure: Interrompere il monitoraggio di messaggi D2C** nel riquadro comandi. 
4. Per visualizzare o aggiornare il modulo gemello, fare clic con il pulsante destro del mouse sul modulo nell'elenco e scegliere **Edit module twin** (Modifica il modulo gemello). Per aggiornare il modulo gemello, salvare il file JSON gemello, fare clic con il pulsante destro del mouse sull'area degli editor e scegliere **Update Module Twin** (Aggiorna il modulo gemello).
5. Per visualizzare i log di Docker, installare [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) per Visual Studio Code. È possibile trovare i moduli in esecuzione in locale nello strumento di esplorazione di Docker. Dal menu di scelta rapida selezionare **Mostra log** per visualizzarli nel terminale integrato.
 
## <a name="clean-up-resources"></a>Pulire le risorse 

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test. 

In caso contrario, è possibile eliminare le risorse di Azure e le configurazioni locali create in questo articolo per evitare addebiti. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge con codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. Quando si è pronti per compilare moduli personalizzati, sono disponibili altre informazioni sullo [sviluppo di un modulo C# con Azure IoT Edge per Visual Studio Code](how-to-develop-csharp-module.md). È possibile continuare con le esercitazioni successive per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Archiviare dati sul perimetro con database di SQL Server](tutorial-store-data-sql-server.md)
