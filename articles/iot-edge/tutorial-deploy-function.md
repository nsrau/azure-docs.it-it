---
title: 'Esercitazione: distribuire una funzione di Azure in un dispositivo - Azure IoT Edge | Microsoft Docs'
description: In questa esercitazione una funzione di Azure viene distribuita come modulo IoT Edge, quindi in un dispositivo perimetrale.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 2c2a2659b6b9c77b36001af1602c904e7d200b56
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433038"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Esercitazione: Distribuire funzioni di Azure come moduli IoT Edge

È possibile usare Funzioni di Azure per distribuire il codice che implementa la logica di business direttamente nei dispositivi Azure IoT Edge. Questa esercitazione illustra la creazione e distribuzione di una funzione di Azure che filtra i dati del sensore nel dispositivo IoT Edge simulato. Viene usato il dispositivo IoT Edge simulato che è stato creato nelle guide introduttive per la distribuzione di Azure IoT Edge in un dispositivo simulato in [Windows](quickstart.md) o [Linux](quickstart-linux.md). In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Usare Visual Studio Code per creare una funzione di Azure.
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla in un registro contenitori.
> * Distribuire il modulo dal registro contenitori al dispositivo IoT Edge.
> * Visualizzare i dati filtrati.

<center>

![Diagramma - Architettura dell'esercitazione, staging e modulo di distribuzione delle funzioni](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>I moduli di Funzioni di Azure in Azure IoT Edge sono in [anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pubblica. 

La funzione di Azure creata in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Questa funzione invia solo messaggi upstream all'hub IoT di Azure quando la temperatura è superiore a una soglia specificata. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione è necessario aver completato l'esercitazione precedente per configurare l'ambiente di sviluppo per i contenitori Linux: [Sviluppare moduli IoT Edge per i dispositivi Linux](tutorial-develop-for-linux.md). Completando tale esercitazione, saranno soddisfatti i prerequisiti seguenti: 

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Un [dispositivo Linux che esegue Azure IoT Edge](quickstart-linux.md)
* Un registro contenitori, ad esempio [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurato con [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurato per eseguire i contenitori Linux.

Per sviluppare un modulo IoT Edge in Funzioni di Azure, installare i prerequisiti aggiuntivi seguenti nel computer di sviluppo: 

* [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Creare un progetto per le funzioni

Gli strumenti di Azure IoT per Visual Studio Code installati nei prerequisiti forniscono funzionalità di gestione, nonché alcuni modelli di codice. In questa sezione si usa Visual Studio Code per creare una soluzione IoT Edge che contiene una funzione di Azure. 

### <a name="create-a-new-project"></a>Creare un nuovo progetto

Creare un modello di soluzione C# che è possibile personalizzare con il proprio codice.

1. Aprire Visual Studio Code nel computer di sviluppo.

2. Aprire il riquadro comandi di VS Code selezionando **Visualizza** > **Riquadro comandi**.

3. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: Nuova soluzione IoT Edge**. Seguire i prompt nel riquadro comandi per creare la soluzione.

   | Campo | Valore |
   | ----- | ----- |
   | Selezionare la cartella | Nel computer di sviluppo scegliere la posizione in cui Visual Studio Code dovrà creare i file della soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione, ad esempio **FunctionSolution**, oppure accettare l'impostazione predefinita. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **Azure Functions - C#** (Funzioni di Azure - C#). |
   | Provide a module name (Specificare un nome per il modulo) | Assegnare al modulo il nome **CSharpFunction**. |
   | Provide Docker image repository for the module (Specificare il repository di immagini Docker per il modulo) | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore è prepopolata dall'ultimo passaggio. Sostituire **localhost:5000** con il valore del server di accesso in Registro Azure Container. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure. La stringa finale è simile a \<nome registro\>.azurecr.io/CSharpFunction. |

   ![Specificare il repository di immagini Docker](./media/tutorial-deploy-function/repository.png)

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

Si aggiungerà ora del codice in modo che il modulo elabori i messaggi nel dispositivo Edge prima di inoltrarli all'hub IoT.

1. In Visual Studio Code aprire **modules** > **CSharpFunction** > **CSharpFunction.cs**.

1. Sostituire il contenuto del file **CSharpFunction.cs** con il codice seguente. Questo codice riceve dati di telemetria sulla temperatura dell'ambiente e del computer e inoltra il messaggio all'hub IoT solo se la temperatura del computer supera una soglia definita.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
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
   }
   ```

1. Salvare il file.

## <a name="build-your-iot-edge-solution"></a>Compilare la soluzione IoT Edge

Nella sezione precedente è creata una soluzione IoT Edge ed è stato aggiunto a **CSharpFunction** il codice per filtrare i messaggi in cui la temperatura del computer segnalata è inferiore alla soglia accettabile. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori.

In questa sezione fornire le credenziali per il registro contenitori per la seconda volta (la prima è stata nel file **env** della soluzione IoT Edge) effettuando l'accesso in locale dal computer di sviluppo in modo che Visual Studio Code possa eseguire il push delle immagini nel registro.

1. Aprire il terminale integrato di VS Code selezionando **Visualizza** > **Terminale**. 

2. Accedere al registro contenitori immettendo il comando seguente nel terminale integrato. Usare il nome utente e il server di accesso copiati prima da Registro Azure Container.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Quando viene chiesta la password, incollare la password (non sarà visibile nella finestra del terminale) per il registro contenitori e premere **Invio**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file deployment.template.json e scegliere **Build and Push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge). 

Quando si comunica a Visual Studio Code di compilare la soluzione, prima di tutto con le informazioni del modello di distribuzione viene generato un file deployment.json in una nuova cartella denominata **config**. Vengono quindi eseguiti due comandi nel terminale integrato: `docker build` e `docker push`. Questi due comandi compilano il codice, includono le funzioni in contenitori e ne eseguono il push nel registro contenitori specificato quando è stata inizializzata la soluzione. 

## <a name="view-your-container-image"></a>Visualizzare l'immagine del contenitore

Visual Studio Code genera un messaggio di conferma quando viene eseguito il push dell'immagine del contenitore nel registro contenitori. Per verificare personalmente se l'operazione ha esito positivo, è possibile visualizzare l'immagine nel registro. 

1. Nel portale di Azure passare al Registro Azure Container. 
2. Selezionare **Repository**.
3. Il repository **csharpfunction** dovrebbe essere visualizzato nell'elenco. Selezionare il repository per visualizzare altri dettagli.
4. Nella sezione **Tag** dovrebbe essere visualizzato il tag **0.0.1-amd64**. Questo tag indica la versione e la piattaforma dell'immagine che è stata compilata. Questi valori sono impostati nel file module.json nella cartella CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Distribuire ed eseguire la soluzione

È possibile usare il portale di Azure per distribuire il modulo della funzione in un dispositivo IoT Edge con la stessa procedura seguita nelle guide introduttive. È anche possibile distribuire e monitorare i moduli da Visual Studio Code. Le sezioni seguenti usano gli strumenti di Azure IoT per Visual Studio Code elencati nei prerequisiti. Installare subito l'estensione, se non è già stato fatto. 

1. Nello strumento di esplorazione di VS Code espandere la sezione **Azure IoT Hub dispositivi** (Dispositivi dell'hub IoT di Azure). 

2. Fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge e quindi selezionare **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo). 

3. Passare alla cartella della soluzione che contiene **CSharpFunction**. Aprire la cartella config, selezionare il file **deployment.json** e quindi scegliere **Select Edge Deployment Manifest** (Selezionare il manifesto della distribuzione IoT Edge).

4. Aggiornare la sezione **Azure IoT Hub Devices** (Dispositivi dell'hub IoT di Azure). Dovrebbe essere visualizzata la nuova **CSharpFunction** in esecuzione insieme al modulo **TempSensor** e a **$edgeAgent** e **$edgeHub**. La visualizzazione dei nuovi moduli potrebbe richiedere un po' di tempo. Il dispositivo IoT Edge deve recuperare le informazioni sulla nuova distribuzione dall'hub IoT, avviare i nuovi contenitori e quindi segnalare lo stato all'hub IoT. 

   ![Visualizzare i moduli distribuiti in VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Visualizzare i dati generati

È possibile visualizzare tutti i messaggi in arrivo all'hub IoT eseguendo **Azure IoT Hub: Start Monitoring Built-in Event Endpoint (Hub IoT di Azure: Avvia monitoraggio endpoint eventi predefinito)** nel riquadro comandi.

È anche possibile applicare un filtro alla visualizzazione per mostrare tutti i messaggi in arrivo all'hub IoT da un dispositivo specifico. Fare clic con il pulsante destro del mouse sul dispositivo nella sezione **Azure IoT Hub Devices** (Dispositivi dell'hub IoT di Azure) e scegliere **Start Monitoring Built-in Event Endpoint** (Avvia monitoraggio endpoint eventi predefinito).

Per arrestare il monitoraggio dei messaggi, eseguire il comando **hub IoT di Azure: Stop Monitoring Built-in Event Endpoint** (Hub IoT di Azure: Arresta monitoraggio endpoint eventi predefinito) nel riquadro comandi. 

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test. 

In caso contrario, è possibile eliminare le risorse di Azure e le configurazioni locali create in questo articolo per evitare addebiti. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo della funzione di Azure con codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. Quando si è pronti per compilare moduli personalizzati, sono disponibili altre informazioni sullo [sviluppo con Azure IoT Edge per Visual Studio Code](how-to-vs-code-develop-module.md). 

Continuare con le esercitazioni successive per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Trovare le medie usando una finestra mobile in Analisi di flusso di Azure](tutorial-deploy-stream-analytics.md)
