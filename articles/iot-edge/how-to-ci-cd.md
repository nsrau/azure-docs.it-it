---
title: Integrazione e distribuzione continue di Azure IoT Edge | Microsoft Docs
description: Panoramica sull'integrazione e sulla distribuzione continue di Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5099ca70503ba2ed4ae8f4969a9199816c4986fb
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302572"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Integrazione e distribuzione continue in Azure IoT Edge

Questo articolo descrive come usare le funzionalità di integrazione continua e di distribuzione continua di Azure DevOps Services e Microsoft Team Foundation Server (TFS) per compilare, testare e distribuire applicazioni in Azure IoT Edge in modo rapido ed efficiente. 

In questo articolo verrà spiegato come:
* Creare e archiviare un esempio di soluzione IoT Edge contenente unit test.
* Installare l'estensione Azure IoT Edge per Azure DevOps.
* Configurare l'integrazione continua (CI) per compilare la soluzione ed eseguire gli unit test.
* Configurare la distribuzione continua (CD) per distribuire la soluzione e vedere i responsi.

Per seguire la procedura descritta in questo articolo sono richiesti 30 minuti.

![CI e CD](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Creare una soluzione Azure IoT Edge di esempio usando Visual Studio Code

In questa sezione si crea una soluzione IoT Edge di esempio contenente unit test che è possibile eseguire come parte del processo di compilazione. Prima di applicare le indicazioni di questa sezione, completare la procedura descritta in [Sviluppare una soluzione IoT Edge con più moduli in Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. Nel riquadro comandi di VS Code digitare ed eseguire il comando **Edge: New IoT Edge solution**. Selezionare quindi la cartella dell'area di lavoro, specificare il nome della soluzione (il nome predefinito è **EdgeSolution**) e creare un modulo C# (**FilterModule**) come primo modulo utente di questa soluzione. È anche necessario specificare il repository di immagini Docker per il primo modulo. Il repository di immagini Docker si basa su un registro Docker locale (`localhost:5000/filtermodule`). Per un'ulteriore integrazione continua, è necessario modificare l'impostazione di questo repository in modo che si basi sul Registro contenitori di Azure (`<your container registry address>/filtermodule`) o su Hub Docker.

    ![Impostare il record di controllo di accesso](./media/how-to-ci-cd/acr.png)

2. La finestra VS Code caricherà l'area di lavoro della soluzione IoT Edge. È facoltativamente possibile digitare ed eseguire **Edge: aggiungi modulo IoT Edge** per aggiungere altri moduli. Nella cartella radice sono presenti una cartella `modules`, una cartella `.vscode` e un file modello del manifesto della distribuzione. Tutti i codici dei moduli utente saranno sottocartelle della cartella `modules`. `deployment.template.json` è il modello del manifesto della distribuzione. Alcuni dei parametri di questo file verranno analizzati da `module.json`, presente nella cartella di ogni modulo.

3. L'esempio di soluzione IoT Edge è ora pronto. Il modulo predefinito in C# si comporta come modulo di messaggi pipe. Nel file `deployment.template.json` si vede che questa soluzione contiene due moduli. Il messaggio viene generato dal modulo `tempSensor` e inoltrato direttamente tramite pipe con `FilterModule`, quindi viene inviato all'hub IoT. Sostituire l'intero file **Program.cs** con il contenuto riportato di seguito. Per altre informazioni su questo frammento di codice, è possibile fare riferimento a [Creare un progetto di modulo C# di IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project).

    ```csharp
    namespace FilterModule
    {
        using System;
        using System.IO;
        using System.Runtime.InteropServices;
        using System.Runtime.Loader;
        using System.Security.Cryptography.X509Certificates;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using System.Collections.Generic;     // for KeyValuePair<>
        using Microsoft.Azure.Devices.Shared; // for TwinCollection
        using Newtonsoft.Json;                // for JsonConvert

        public class MessageBody
        {
            public Machine machine { get; set; }
            public Ambient ambient { get; set; }
            public string timeCreated { get; set; }
        }
        public class Machine
        {
            public double temperature { get; set; }
            public double pressure { get; set; }
        }
        public class Ambient
        {
            public double temperature { get; set; }
            public int humidity { get; set; }
        }

        public class Program
        {
            static int counter;
            static int temperatureThreshold { get; set; } = 25;

            static void Main(string[] args)
            {
                Init().Wait();

                // Wait until the app unloads or is cancelled
                var cts = new CancellationTokenSource();
                AssemblyLoadContext.Default.Unloading += (ctx) => cts.Cancel();
                Console.CancelKeyPress += (sender, cpe) => cts.Cancel();
                WhenCancelled(cts.Token).Wait();
            }

            /// <summary>
            /// Handles cleanup operations when app is cancelled or unloads
            /// </summary>
            public static Task WhenCancelled(CancellationToken cancellationToken)
            {
                var tcs = new TaskCompletionSource<bool>();
                cancellationToken.Register(s => ((TaskCompletionSource<bool>)s).SetResult(true), tcs);
                return tcs.Task;
            }

            /// <summary>
            /// Initializes the ModuleClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init()
            {
                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessage, ioTHubModuleClient);
            }

            /// <summary>
            /// This method is called whenever the module is sent a message from the EdgeHub. 
            /// It just pipe the messages without any change.
            /// It prints all the incoming messages.
            /// </summary>
            static async Task<MessageResponse> FilterMessage(Message message, object userContext)
            {
                int counterValue = Interlocked.Increment(ref counter);

                var moduleClient = userContext as ModuleClient;
                if (moduleClient == null)
                {
                    throw new InvalidOperationException("UserContext doesn't contain " + "expected values");
                }

                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                var filteredMessage = filter(message);

                if (filteredMessage != null && !string.IsNullOrEmpty(messageString))
                {
                    var pipeMessage = new Message(messageBytes);
                    foreach (var prop in message.Properties)
                    {
                        pipeMessage.Properties.Add(prop.Key, prop.Value);
                    }
                    await moduleClient.SendEventAsync("output1", pipeMessage);
                    Console.WriteLine("Received message sent");
                }
                return MessageResponse.Completed;
            }

            public static Message filter(Message message)
            {
                var counterValue = Interlocked.Increment(ref counter);

                var messageBytes = message.GetBytes();
                var messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message {counterValue}: [{messageString}]");

                // Get message body
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
                    return filteredMessage;
                }
                return null;
            }
        }
    }
    ```

4. Creare un progetto di unit test .Net Core. In Esplora file di VS Code, creare una nuova cartella **tests\FilterModuleTest** nell'area di lavoro. Nel terminale integrato VS Code (**Ctrl + '**), eseguire quindi i comandi seguenti per creare un progetto di test xunit e aggiungere il riferimento al progetto **FilterModule**.

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![Struttura di cartelle](./media/how-to-ci-cd/add-test-project.png)

5. Nella cartella **FilterModuleTest** aggiornare il nome file di **UnitTest1.cs** con **FilterModuleTest.cs**. Selezionare e aprire **FilterModuleTest.cs**, sostituire tutto il codice con il frammento di codice seguente, che contiene gli unit test per il progetto FilterModule.

    ```csharp
    using Xunit;
    using FilterModule;
    using Newtonsoft.Json;
    using System;
    using System.IO;
    using System.Runtime.InteropServices;
    using System.Runtime.Loader;
    using System.Security.Cryptography.X509Certificates;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;

    namespace FilterModuleTest
    {
        public class FilterModuleTest
        {
            [Fact]
            public void filterLessThanThresholdTest()
            {
                var source = createMessage(25 - 1);
                var result = Program.filter(source);
                Assert.True(result == null);
            }

            [Fact]
            public void filterMoreThanThresholdAlertPropertyTest()
            {
                var source = createMessage(25 + 1);
                var result = Program.filter(source);
                Assert.True(result.Properties["MessageType"] == "Alert");
            }

            [Fact]
            public void filterMoreThanThresholdCopyPropertyTest()
            {
                var source = createMessage(25 + 1);
                source.Properties.Add("customTestKey", "customTestValue");
                var result = Program.filter(source);
                Assert.True(result.Properties["customTestKey"] == "customTestValue");
            }

            private Message createMessage(int temperature)
            {
                var messageBody = createMessageBody(temperature);
                var messageString = JsonConvert.SerializeObject(messageBody);
                var messageBytes = Encoding.UTF8.GetBytes(messageString);
                return new Message(messageBytes);
            }

            private MessageBody createMessageBody(int temperature)
            {
                var messageBody = new MessageBody
                {
                    machine = new Machine
                    {
                        temperature = temperature,
                        pressure = 0
                    },
                    ambient = new Ambient
                    {
                        temperature = 0,
                        humidity = 0
                    },
                    timeCreated = string.Format("{0:O}", DateTime.Now)
                };

                return messageBody;
            }
        }
    }
    ```

6. Nel terminale integrato, è possibile immettere i comandi seguenti per eseguire gli unit test localmente. 
    ```cmd
    dotnet test
    ```

    ![Unit test](./media/how-to-ci-cd/unit-test.png)

7. Salvare i progetti, quindi archiviarli nel repository di Azure DevOps o TFS.
    

> [!NOTE]
> Per altre informazioni sull'uso di Azure Repos, vedere [Condividere il codice con Visual Studio e Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-continuous-integration"></a>Configurare l'integrazione continua
In questa sezione si crea una pipeline di compilazione che viene configurata per l'esecuzione automatica quando si archiviano modifiche all'esempio di soluzione IoT Edge e che esegue automaticamente gli unit test che contiene.

1. Accedere all'organizzazione Azure DevOps (**https://**_account-personale_**.visualstudio.com**) e aprire il progetto in cui è stata archiviata l'app di esempio.

    ![Archiviare il codice](./media/how-to-ci-cd/init-project.png)

1. Visita [Azure IoT Edge per Azure DevOps](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) in Marketplace di Azure DevOps. Fare clic su **Get it free** e seguire la procedura guidata per installare l'estensione nell'organizzazione Azure DevOps o scaricarla in TFS.

    ![Installare l'estensione](./media/how-to-ci-cd/install-extension.png)

1. In Azure DevOps, aprire l'hub **Compilazione e &amp; versione**, quindi nella scheda **Compilazioni** scegliere **+ Nuova pipeline**. In alternativa, se si dispone già di pipeline di compilazione, scegliere il pulsante **+ Nuova**. 

    ![Nuova compilazione](./media/how-to-ci-cd/add-new-build.png)

1. Se richiesto, selezionare il tipo di origine **Git Azure DevOps**. Selezionare quindi progetto, repository e ramo in cui si trova il codice. Scegliere **Continua**.

    ![Selezionare Git Azure DevOps](./media/how-to-ci-cd/select-vsts-git.png)

1. Nella finestra **Select a template** (Selezionare un modello) scegliere **start with an Empty process** (iniziare con un processo vuoto).

    ![Avviare un processo vuoto](./media/how-to-ci-cd/start-with-empty.png)

1. Fare clic su **+** sul lato destro di **Phase 1** (Fase 1) per aggiungere un'attività alla fase. Cercare e selezionare **.Net Core**, quindi fare clic sul pulsante **Add** (Aggiungi) per aggiungere l'attività alla fase.

    ![Test dotnet](./media/how-to-ci-cd/add-dot-net-core.png)

1. Aggiornare il **Display name** (Nome visualizzato) con **dotnet test**, quindi nell'elenco a discesa **Command** (Comando) selezionare **test**. Nella casella **Path to project(s)** (Percorso dei progetti) aggiungere il percorso indicato di seguito.

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![Configurare il test dotnet](./media/how-to-ci-cd/dotnet-test.png)

1. Fare clic su **+** sul lato destro di **Phase 1** (Fase 1) per aggiungere un'attività alla fase. Cercare e selezionare **Azure IoT Edge**, quindi fare clic sul pulsante **Add** (Aggiungi) **due volte** per aggiungere queste attività alla fase.

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. Nella prima attività di Azure IoT Edge, aggiornare il **Display name** (Nome visualizzato) con **Module Build and Push** (Compilazione e push del modulo), quindi nell'elenco a discesa **Action** (Azione) selezionare **Build and Push** (Compila ed esegui il push). Nella casella di testo **Module.json File** (File Module.json), aggiungere il percorso seguente. Scegliere quindi il **Container Registry Type** (Tipo di registro contenitori), assicurarsi di configurare e selezionare lo stesso registro nel codice. Questa attività compila ed esegue il push di tutti i moduli della soluzione e pubblica il risultato nel registro contenitori specificato. Se il push dei moduli verrà eseguito in registri diversi, è possibile avere più attività **Module Build and Push** (Compilazione e push del modulo).

    ```
    **/module.json
    ```

    ![Compilazione e push del modulo](./media/how-to-ci-cd/module-build-push.png)

1. Nella seconda attività di Azure IoT Edge aggiornare il **Display name** (Nome visualizzato) con **Deploy to IoT Edge device** (Distribuisci a dispositivo IoT Edge), quindi nell'elenco a discesa **Action** (Azione) selezionare **Deploy to IoT Edge device** (Distribuisci a dispositivo IoT Edge). Selezionare la sottoscrizione di Azure e immettere il nome dell'hub IoT. È possibile specificare un ID distribuzione IoT Edge e la priorità della distribuzione. È inoltre possibile scegliere di distribuire a uno o più dispositivi. Se si distribuisce a più dispositivi, è necessario specificare la condizione di destinazione del dispositivo. Se ad esempio si vuole usare i Tag del dispositivo come condizione, è necessario aggiornare i tag di dispositivo corrispondenti prima della distribuzione. 

    ![Distribuire in Edge](./media/how-to-ci-cd/deploy-to-edge.png)

1. Fare clic su **Processo** e assicurarsi che **Coda agente** sia impostato su **Hosted Linux Preview** (Anteprima Linux ospitata).

    ![Configurare](./media/how-to-ci-cd/configure-env.png)

1. Visualizzare la scheda **Trigger** e attivare il trigger **Integrazione continua**. Verificare che il ramo contenente il codice sia incluso.

    ![Trigger](./media/how-to-ci-cd/configure-trigger.png)

1. Salvare la nuova pipeline di compilazione e accodare una nuova compilazione. Fare clic sul pulsante **Save & queue** (Salva e accoda).

1. Scegliere il collegamento alla compilazione nella barra dei messaggi che viene visualizzata. Passare in alternativa alla pipeline di compilazione per visualizzare il processo di compilazione in coda più recente.

    ![Compilare](./media/how-to-ci-cd/build-def.png)

1. Al termine della compilazione, vengono visualizzati il riepilogo per ogni attività e i risultati nel file di log in tempo reale. 
    
    ![Complete](./media/how-to-ci-cd/complete.png)

1. È possibile tornare a VS Code e controllare Esplora dispositivi hub IoT. Viene avviato il dispositivo Edge con il modulo (assicurarsi di avere aggiunto le credenziali del registro al runtime Edge).

    ![Esecuzione di Edge](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>Distribuzione continua in dispositivi IoT Edge

Per abilitare la distribuzione continua, fondamentalmente è necessario configurare i processi CI con i dispositivi IoT Edge appropriati, abilitando i **Trigger** per i rami del progetto. In una situazione di DevOps classica, un progetto contiene due rami principali. Il ramo master è la versione stabile del codice e il ramo di sviluppo contiene le modifiche più recenti del codice. È consigliabile che ogni sviluppatore del team crei una copia tramite fork del ramo di sviluppo nel proprio ramo di funzionalità quando inizia ad aggiornare il codice. In questo modo tutti i commit vengono eseguiti nei rami di funzionalità al di fuori del ramo di sviluppo. È inoltre consigliabile testare ogni commit sottoposto a push tramite il sistema CI. Dopo aver testato completamente il codice in locale, il ramo di funzionalità deve essere unito al ramo di sviluppo tramite una richiesta pull. Quando il codice nel ramo di sviluppo viene testato tramite il sistema CI, può essere unito al ramo master tramite una richiesta pull.

Durante la distribuzione nei dispositivi IoT Edge, esistono quindi tre ambienti principali.
- Nel ramo di funzionalità, è possibile usare un dispositivo IoT Edge simulato nel computer di sviluppo o distribuire in un dispositivo IoT Edge fisico.
- Nel ramo di sviluppo occorre distribuire in un dispositivo IoT Edge fisico.
- Nel ramo master, i dispositivi IoT Edge di destinazione devono essere i dispositivi di produzione.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla distribuzione IoT Edge, vedere [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala)
* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).
