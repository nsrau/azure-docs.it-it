---
title: Modulo di IoT Edge C# per Azure Data Box Edge | Microsoft Docs
description: Informazioni su come sviluppare un modulo di IoT Edge C# che può essere distribuito in Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 734ad263356ab9f91c7cb92ab174a14e0c5dd867
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775183"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Sviluppare un C# modulo IOT Edge per spostare i file in data box Edge

Questo articolo illustra come creare un modulo di IoT Edge per la distribuzione con il dispositivo Data Box Edge. Azure Data Box Edge è una soluzione di archiviazione che consente di elaborare i dati e inviarli in rete ad Azure.

È possibile usare i moduli di Azure IoT Edge con Data Box Edge per trasformare i dati quando viene spostato in Azure. Il modulo usato in questo articolo implementa la logica per copiare un file da una condivisione locale in una condivisione cloud nel dispositivo Data Box Edge.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un registro contenitori per archiviare e gestire i moduli, ossia immagini Docker.
> * Creare un modulo di IoT Edge per eseguire la distribuzione nel dispositivo Data Box Edge.


## <a name="about-the-iot-edge-module"></a>Informazioni sul modulo IoT Edge

Il dispositivo Data Box Edge può distribuire ed eseguire i moduli di IoT Edge. I moduli di Edge sono essenzialmente contenitori Docker che eseguono un'attività specifica, come inserire un messaggio da un dispositivo, trasformare un messaggio o inviare un messaggio a un hub IoT. In questo articolo si creerà un modulo che copia i file da una condivisione locale a una condivisione cloud nel dispositivo Data Box Edge.

1. I file vengono scritti nella condivisione locale del dispositivo Data Box Edge.
2. Il generatore di eventi del file crea un evento di file per ogni file scritto nella condivisione locale. Gli eventi di file vengono generati anche quando un file viene modificato. Gli eventi di file vengono quindi inviati all'hub di IoT Edge, nel runtime di IoT Edge.
3. Il modulo personalizzato di IoT Edge elabora l'evento di file per creare un oggetto dell'evento di file che contiene anche un percorso relativo del file. Il modulo genera un percorso assoluto usando il percorso relativo del file e copia il file dalla condivisione locale alla condivisione cloud. Il modulo elimina quindi il file dalla condivisione locale.

![Funzionamento del modulo di Azure IoT Edge in Data Box Edge](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

Quando il file è nella condivisione cloud, viene caricato automaticamente nell'account di Archiviazione di Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di avere:

- Un dispositivo Data Box Edge in esecuzione.

    - Il dispositivo ha anche una risorsa dell'hub IoT associata.
    - Il dispositivo ha il ruolo di calcolo Edge configurato.
    Per altre informazioni, vedere [configurare il calcolo](data-box-edge-deploy-configure-compute.md#configure-compute) per il data box Edge.

- Le risorse di sviluppo seguenti:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). È possibile che sia necessario creare un account per scaricare e installare il software.

## <a name="create-a-container-registry"></a>Creare un registro contenitori

Un registro contenitori di Azure è un registro Docker privato in Azure nel quale è possibile archiviare e gestire le immagini del contenitore Docker privato. Due servizi molto diffusi per il registro Docker disponibili nel cloud sono il Registro Azure Container e Hub Docker. Questo articolo usa il Registro contenitori.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Selezionare **Crea una risorsa > Contenitori > Registro contenitori**. Fare clic su **Create**(Crea).
3. Specificare:

   1. Un valore **Nome registro** all'interno di Azure contenente da 5 a 50 caratteri alfanumerici.
   2. Scegliere una **Sottoscrizione**.
   3. Scegliere un **gruppo di risorse** esistente oppure crearne uno nuovo.
   4. Selezionare un **percorso**. È consigliabile inserire la stessa posizione associata alla risorsa Data Box Edge.
   5. Impostare **Utente amministratore** su **Abilita**.
   6. Impostare lo SKU **Di base**.

      ![Crea registro contenitori](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Selezionare **Create**.
5. Dopo aver creato il registro contenitori, passare al registro e selezionare **Chiavi di accesso**.

    ![Ottenere le chiavi di accesso](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Copiare i valori nei campi **Server di accesso**, **Nome utente** e **Password**. Questi valori vengono usati più avanti per pubblicare l'immagine Docker nel registro e per aggiungere le credenziali del registro al runtime di Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Creare un progetto di modulo IoT Edge

La procedura seguente consente di creare un progetto di modulo di IoT Edge basato sull'SDK di.NET Core 2.1. Il progetto usa Visual Studio Code e l'estensione Azure IoT Edge.

### <a name="create-a-new-solution"></a>Creare una nuova soluzione

Creare un modello di soluzione C# che è possibile personalizzare con il proprio codice.

1. In Visual Studio Code selezionare **Visualizza > Riquadro comandi** per aprire il riquadro comandi di VS Code.
2. Nel riquadro comandi immettere ed eseguire il comando **Azure: Accedere** e seguire le istruzioni per accedere all'account Azure. Se è stato già effettuato l'accesso, è possibile ignorare questo passaggio.
3. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: Nuova soluzione IoT Edge**. Nel riquadro comandi immettere le informazioni seguenti per creare la soluzione:

    1. Selezionare la cartella in cui si vuole creare la soluzione.
    2. Specificare un nome per la soluzione o accettare quello predefinito **EdgeSolution**.
    
        ![Creare una nuova soluzione 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Scegliere **C# Module** (Modulo C#) come modello di modulo.
    4. Sostituire il nome del modulo predefinito con il nome che si desidera assegnare, in questo caso, **FileCopyModule**.
    
        ![Creare una nuova soluzione 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Specificare il registro contenitori creato nella sezione precedente come repository di immagini per il primo modulo. Sostituire **localhost:5000** con il valore del server di accesso copiato.

        La stringa finale sarà simile a `<Login server name>/<Module name>`. In questo esempio la stringa è: `mycontreg2.azurecr.io/filecopymodule`.

        ![Creare una nuova soluzione 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Passare a **File > Apri cartella**.

    ![Creare una nuova soluzione 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Trovare e puntare alla cartella **EdgeSolution** creata in precedenza. La finestra di VS Code carica l'area di lavoro della soluzione IoT Edge con i cinque componenti di livello superiore. In questo articolo non verranno modificati la cartella **.vscode**, il file **.gitignore**, il file **.env** e **deployment.template.json**.
    
    L'unico componente che verrà modificato è la cartella dei moduli. Questa cartella include il codice C# per il modulo e i file Docker per compilare il modulo come un'immagine del contenitore.

    ![Creare una nuova soluzione 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Aggiornare il modulo con il codice personalizzato

1. In Esplora VS Code aprire **moduli > FileCopyModule > Program.cs**.
2. Nella parte superiore dello **spazio dei nomi CSharpModule** aggiungere le istruzioni using seguenti per i tipi che verranno usati in un secondo momento. **Microsoft.Azure.Devices.Client.Transport.Mqtt** è un protocollo per inviare messaggi all'hub di IoT Edge.

    ```
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;
    using Newtonsoft.Json;
    ```
3. Aggiungere la variabile **InputFolderPath** e **OutputFolderPath** alla classe Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Aggiungere la classe fileEvent per definire il corpo del messaggio.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. Nel metodo **Init** il codice crea e configura un oggetto **ModuleClient**. Questo oggetto consente al modulo di connettersi al runtime locale di Azure IoT Edge tramite il protocollo MQTT per inviare e ricevere messaggi. La stringa di connessione usata nel metodo Init viene specificata nel modulo dal runtime di IoT Edge. Il codice registra un callback FileCopy per ricevere i messaggi da un hub di IoT Edge tramite l'endpoint **input1**.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Inserire il codice per **FileCopy**.

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub. 
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. Salvare questo file.

## <a name="build-your-iot-edge-solution"></a>Compilare la soluzione IoT Edge

Nella sezione precedente è stata creata una soluzione IoT Edge ed è stato aggiunto il codice a FileCopyModule per copiare i file dalla condivisione locale alla condivisione cloud. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori.

1. In VSCode passare a terminale > nuovo terminale per aprire un nuovo Visual Studio Code terminale integrato.
2. Accedere a Docker immettendo il comando seguente nel terminale integrato.

    `docker login <ACR login server> -u <ACR username>`

    Usare il nome utente e il server di accesso copiati prima dal registro contenitori. 

    ![Compilare ed eseguire il push della soluzione IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Quando richiesto, specificare la password. È anche possibile recuperare i valori per il server di accesso, il nome utente e la password dalle **chiavi di accesso** nel registro contenitori del portale di Azure.
 
3. Dopo aver specificato le credenziali, è quindi possibile eseguire il push dell'immagine del modulo in Registro Azure Container. Nello strumento di esplorazione di VS Code fare clic con il pulsante destro del mouse sul file **module.json** e scegliere **Build and Push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge).

    ![Compilare ed eseguire il push della soluzione IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Quando si richiede di compilare la soluzione, Visual Studio Code esegue due comandi nel terminale integrato: docker build e docker push. Questi due comandi compilano il codice, includono CSharpModule.dll in contenitori e ne eseguono il push nel registro contenitori specificato quando è stata inizializzata la soluzione.

    Verrà chiesto di scegliere la piattaforma del modulo. Selezionare *amd64* corrispondente a Linux.

    ![Seleziona la piattaforma](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Sono supportati solo i moduli Linux.

    È possibile che venga visualizzato l'avviso seguente che può essere ignorato:

    *Program.cs(77,44): avviso CS1998: Questo metodo asincrono non contiene operatori 'Await', pertanto verrà eseguito in modo sincrono. Provare a usare l'operatore 'await' per attendere chiamate ad API non di blocco oppure 'await Task.Run(...)' per effettuare elaborazioni basate sulla CPU in un thread in background.*

4. È possibile visualizzare l'indirizzo completo dell'immagine del contenitore con tag nel terminale integrato di VS Code. L'indirizzo dell'immagine è costituito dalle informazioni presenti nel file module.json nel formato `<repository>:<version>-<platform>`. In questo articolo, dovrebbe essere simile a `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`.

## <a name="next-steps"></a>Passaggi successivi

Per distribuire ed eseguire questo modulo in Data Box Edge, vedere la procedura descritta in [aggiungere un modulo](data-box-edge-deploy-configure-compute.md#add-a-module).
