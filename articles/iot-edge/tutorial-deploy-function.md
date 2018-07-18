---
title: Distribuire Funzioni di Azure con Azure IoT Edge | Microsoft Docs
description: Distribuire Funzione di Azure come modulo in un dispositivo perimetrale
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0445817f9ff403156025e38a1e14a3892a9a292b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38667023"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules---preview"></a>Esercitazione: Distribuire Funzioni di Azure come moduli IoT Edge - anteprima

È possibile usare Funzioni di Azure per distribuire il codice che implementa la logica di business direttamente nei dispositivi IoT Edge. Questa esercitazione illustra in modo dettagliato la creazione e la distribuzione di una funzione di Azure che filtra i dati dei sensori nel dispositivo IoT Edge simulato, creato nelle esercitazioni sulla distribuzione di Azure IoT Edge in un dispositivo simulato in [Windows][lnk-tutorial1-win] o [Linux][lnk-tutorial1-lin]. In questa esercitazione si apprenderà come:     

> [!div class="checklist"]
> * Usare Visual Studio Code per creare una funzione di Azure
> * Usare VS Code e Docker per creare un'immagine Docker e pubblicarla in un registro contenitori 
> * Distribuire il modulo dal registro contenitori al dispositivo IoT Edge
> * Visualizzare i dati filtrati

>[!NOTE]
>I moduli delle funzioni di Azure in Azure IoT Edge sono in [anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pubblica. 

La funzione di Azure creata in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo e invia messaggi upstream all'hub IoT di Azure solo quando la temperatura è superiore a una soglia specificata. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

Per testare il modulo di Funzioni che si compila in questa esercitazione, è necessario un dispositivo IoT Edge. È possibile usare il dispositivo configurato nella guida introduttiva per [Linux](quickstart-linux.md) o [Windows](quickstart.md).

Nel computer di sviluppo sono necessari prerequisiti seguenti: 
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) per Visual Studio Code.
* [Estensione Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) per Visual Studio Code. 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) installato nel computer di sviluppo. 

## <a name="create-a-container-registry"></a>Creare un registro di contenitori
In questa esercitazione viene usata l'estensione Azure IoT Edge per Visual Studio Code per creare un modulo e un'**immagine del contenitore** dai file. Eseguire quindi il push dell'immagine in un **registro** che archivia e gestisce le immagini. Distribuire infine l'immagine dal registro nel dispositivo di IoT Edge.  

È possibile usare qualsiasi registro compatibile con Docker per questa esercitazione. Due servizi molto diffusi per il registro Docker disponibili sul cloud sono il [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) e [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Questa esercitazione usa il registro contenitori di Azure. 

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Contenitori** > **Registro contenitori**.

    ![Creare un registro contenitori](./media/tutorial-deploy-function/create-container-registry.png)

2. Assegnare un nome al registro e scegliere una sottoscrizione.
3. Per il gruppo di risorse, è consigliabile usare lo stesso nome di gruppo di risorse che contiene l'hub IoT. Tenendo tutte le risorse nello stesso gruppo, è possibile gestirle insieme. Ad esempio, con l'eliminazione del gruppo di risorse usato per i test vengono eliminate tutte le risorse di test contenute nel gruppo. 
4. Impostare lo SKU su **Basic** e impostare **Utente amministratore** su **Abilita**. 
5. Fare clic su **Crea**.
6. Dopo aver creato il registro di sistema del contenitore, accedervi e selezionare **Chiavi di accesso**. 
7. Copiare i valori nei campi **Server di accesso**, **Nome utente** e **Password**. Questi valori verranno usati più avanti nell'esercitazione. 

## <a name="create-a-function-project"></a>Creare un progetto per le funzioni
La procedura seguente illustra come creare una funzione di IoT Edge tramite Visual Studio Code e l'estensione Azure IoT Edge.

1. Aprire Visual Studio Code.
2. Aprire il terminale integrato di VS Code selezionando **Visualizza** > **Terminale integrato**. 
2. Aprire il riquadro comandi di VS Code selezionando **Visualizza** > **Riquadro comandi**.
3. Nel riquadro comandi digitare ed eseguire il comando **Azure: Sign in** (Azure: Accedi) seguire le istruzioni per accedere all'account Azure. Se è stato già effettuato l'accesso, è possibile ignorare questo passaggio.
3. Nel riquadro comandi digitare ed eseguire il comando **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nuova soluzione IoT Edge). Nel riquadro comandi fornire le informazioni seguenti per creare la soluzione: 

   1. Selezionare la cartella in cui si vuole creare la soluzione. 
   2. Specificare un nome per la soluzione o accettare quello predefinito **EdgeSolution**.
   3. Scegliere **Azure Functions - C#** (Funzioni di Azure - C#) come modello di modulo. 
   4. Assegnare al modulo il nome **CSharpFunction**. 
   5. Specificare il registro contenitori di Azure creato nella sezione precedente come repository di immagini per il primo modulo. Sostituire **localhost:5000** con il valore del server di accesso copiato. La stringa finale è simile a **\<nome registro\>.azurecr.io/csharpfunction**.

4. La finestra VS Code carica l'area di lavoro della soluzione IoT Edge. Sono presenti una cartella **.vscode**, una cartella **modules**, un file modello del manifesto della distribuzione e un file **ENV**. Aprire **modules** > **CSharpFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

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
        public Machine machine {get; set;}
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

6. Salvare il file.

## <a name="build-your-iot-edge-solution"></a>Compilare la soluzione IoT Edge

Nella sezione precedente è creata una soluzione IoT Edge ed è stato aggiunto a CSharpFunction il codice per filtrare i messaggi in cui la temperatura del computer segnalata è inferiore alla soglia accettabile. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori.

1. Accedere a Docker immettendo il comando seguente nel terminale integrato di Visual Studio Code, per poter eseguire il push dell'immagine del modulo nel record di controllo di accesso: 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Usare il nome utente e il server di accesso copiati prima dal registro contenitori di Azure. Verrà richiesto di specificare la password. Incollare la password nel prompt e premere **INVIO**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. Nello strumento di esplorazione di VS Code aprire il file **deployment.template.json** nell'area di lavoro della soluzione IoT Edge. Questo file indica al runtime IoT Edge quali moduli distribuire in un dispositivo. Per altre informazioni sui manifesti di distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).

3. Trovare la sezione **registryCredentials** nel manifesto della distribuzione. Aggiornare **username**, **password** e **address** con le credenziali del registro contenitori. Questa sezione fornisce il runtime IoT Edge per l'autorizzazione del dispositivo a eseguire il pull delle immagini del contenitore archiviate nel registro privato. Le coppie effettive di username e password vengono archiviate nel file ENV ignorato da Git.

5. Salvare questo file.

6. Nello strumento di esplorazione di VS Code fare clic con il pulsante destro del mouse sul file **deployment.template.json** e scegliere **Build IoT Edge solution** (Compila soluzione IoT Edge). 

Quando si comunica a Visual Studio Code di compilare la soluzione, prima di tutto con le informazioni del modello di distribuzione viene generato un file `deployment.json` in una nuova cartella **config**. Vengono quindi eseguiti due comandi nel terminale integrato: `docker build` e `docker push`. Questi due comandi compilano il codice, includono le funzioni in contenitori e ne eseguono il push nel registro contenitori specificato quando è stata inizializzata la soluzione. 

## <a name="view-your-container-image"></a>Visualizzare l'immagine del contenitore

Visual Studio Code genera un messaggio di conferma quando viene eseguito il push dell'immagine del contenitore nel registro contenitori. Per verificare personalmente se l'operazione ha esito positivo, è possibile visualizzare l'immagine nel registro. 

1. Nel portale di Azure passare al registro contenitori di Azure. 
2. Selezionare **Repository**.
3. **csharpfunction** dovrebbe essere elencata nei repository. Selezionare il repository per visualizzare altri dettagli.
4. Nella sezione **Tags** verrà visualizzato **0.0.1-amd64**. Questo tag riflette la versione e la piattaforma dell'immagine che è stata compilata. Questi valori sono impostati nel file **module.json** nella cartella CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Distribuire ed eseguire la soluzione

È possibile usare il portale di Azure per distribuire un modulo di Funzioni in un dispositivo IoT Edge come si è fatto nelle guide introduttive, ma è anche possibile distribuire e monitorare i moduli da Visual Studio Code. Le sezioni seguenti usano l'estensione Azure IoT Edge per VS Code elencata nei prerequisiti. Installarla ora se non è già stato fatto. 

1. Aprire il riquadro comandi di VS Code selezionando **Visualizza** > **Riquadro comandi**.

2. Cercare ed eseguire il comando **Azure: Sign in** (Azure: Accedi). Seguire le istruzioni per accedere all'account Azure. 

3. Nel riquadro comandi cercare ed eseguire il comando **Azure IoT Hub: Select IoT Hub** (Hub IoT di Azure: Seleziona l'hub IoT). 

4. Selezionare la sottoscrizione che contiene l'hub IoT, quindi selezionare l'hub IoT a cui si vuole accedere.

5. Nello strumento di esplorazione di VS Code espandere la sezione **Azure IoT Hub dispositivi** (Dispositivi dell'Hub IoT di Azure). 

6. Fare clic con il pulsante destro del mouse sul dispositivo IoT Edge, quindi scegliere **Create Deployment for IoT Edge device** (Crea la distribuzione per il dispositivo IoT Edge). 

7. Passare alla cartella della soluzione che contiene CSharpFunction. Aprire la cartella **config** e selezionare il file **deployment.json**. Fare clic su **Select Edge deployment manifest** (Seleziona il manifesto della distribuzione di Edge).

8. Aggiornare la sezione **Azure IoT Hub Devices** (Dispositivi dell'Hub IoT di Azure). Dovrebbe essere visualizzata la nuova **CSharpFunction** in esecuzione insieme al modulo **TempSensor** e a **$edgeAgent** e **$edgeHub**. 

   ![Visualizzare i moduli distribuiti in VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Visualizzare i dati generati

È possibile visualizzare tutti i messaggi in arrivo all'hub IoT eseguendo **Azure IoT Hub: Start Monitoring D2C Message** (Hub IoT di Azure: Avvia il monitoraggio del messaggio D2C) nel riquadro comandi.

È anche possibile applicare un filtro per visualizzare tutti i messaggi in arrivo all'hub IoT da un dispositivo specifico. Fare clic con il pulsante destro del mouse sul dispositivo nella sezione **Azure IoT Hub Devices** (Dispositivi dell'Hub IoT di Azure) e scegliere **Start Monitoring D2C Messages** (Avvia il monitoraggio dei messaggi D2C).

Per arrestare il monitoraggio dei messaggi, eseguire il comando **Azure IoT Hub: Stop monitoring D2C message** (Hub IoT di Azure: Interrompi il monitoraggio del messaggio D2C) nel riquadro comandi. 


## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

Rimuovere il runtime del servizio IoT Edge in base alla piattaforma del dispositivo IoT (Linux o Windows).

#### <a name="windows"></a>Windows

Rimuovere il runtime IoT Edge.

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

Eliminare i contenitori creati nel dispositivo. 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

#### <a name="linux"></a>Linux

Rimuovere il runtime IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Eliminare i contenitori creati nel dispositivo. 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

Rimuovere il runtime del contenitore.

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di Funzioni di Azure contenente il codice per filtrare i dati non elaborati generati dal dispositivo IoT Edge. Quando si è pronti per compilare moduli personalizzati, sono disponibili altre informazioni sullo [sviluppo di Funzioni di Azure con Azure IoT Edge per Visual Studio Code](how-to-develop-csharp-function.md). 

Continuare con le esercitazioni successive per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Trovare le medie usando una finestra mobile in Analisi di flusso di Azure](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
