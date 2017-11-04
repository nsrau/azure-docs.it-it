---
title: Aggiornamento firmware del dispositivo con l'hub IoT di Azure (.NET/.NET) | Microsoft Docs
description: Come usare la gestione dei dispositivi nell'hub IoT di Azure per avviare un aggiornamento del firmware del dispositivo. Usare Azure IoT SDK per dispositivi per .NET per implementare un'app dispositivo simulato e Azure IoT SDK per servizi per .NET per implementare un'app di servizio che attiva l'aggiornamento del firmware.
services: iot-hub
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: v-jamebr
ms.openlocfilehash: bd0a227861d75dc66af8fb4865a17a3b6d0f70ba
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Usare la gestione dei dispositivi per avviare un aggiornamento del firmware del dispositivo (.NET/.NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Introduzione
Nell'esercitazione [Introduzione alla gestione dei dispositivi][lnk-dm-getstarted] è stato illustrato come usare il [dispositivo gemello][lnk-devtwin] e le primitive dei [metodi diretti][lnk-c2dmethod] per riavviare un dispositivo in modalità remota. Questa esercitazione usa le stesse primitive dell'hub IoT e illustra come eseguire un aggiornamento del firmware simulato completo.  Questo modello viene usato nell'implementazione dell'aggiornamento del firmware per l'[esempio di implementazione del dispositivo Raspberry Pi][lnk-rpi-implementation].

Questa esercitazione illustra come:

* Creare un'app console .NET che chiama il metodo diretto **firmwareUpdate** nell'app dispositivo simulato tramite l'hub IoT.
* Creare un'app di dispositivo simulato che implementa un metodo diretto **firmwareUpdate**. Questo metodo avvia un processo in più fasi che attende di scaricare l'immagine del firmware, quindi la scarica e infine la applica. Durante l'esecuzione di ogni fase, il dispositivo usa le proprietà segnalate per aggiornare lo stato.

Al termine dell'esercitazione saranno disponibili un'app per il dispositivo console .NET (C#) e un'app back-end console .NET (C#):

* **SimulatedDeviceFwUpdate**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza, riceve il metodo diretto **firmwareUpdate** ed esegue un processo in più stati per simulare un aggiornamento del firmware, inclusi l'attesa del download dell'immagine, il download della nuova immagine e infine l'applicazione dell'immagine.

* **TriggerFWUpdate**, che usa l'SDK del servizio per chiamare in modalità remota il metodo diretto **firmwareUpdate** sul dispositivo simulato, visualizza la risposta e visualizza regolarmente (ogni 500 ms) le proprietà segnalate aggiornate.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Visual Studio 2015 o Visual Studio 2017.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

Vedere l'articolo [Introduzione alla gestione dei dispositivi](iot-hub-csharp-csharp-device-management-get-started.md) per creare l'hub IoT e ottenere la stringa di connessione relativa.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Attivare un aggiornamento del firmware remoto nel dispositivo con un metodo diretto
In questa sezione viene creata un'app console .NET (tramite C#) che avvia un aggiornamento del firmware remoto in un dispositivo. L'applicazione usa un metodo diretto per avviare l'aggiornamento e usa le query di dispositivo gemello per ottenere periodicamente lo stato di aggiornamento del firmware attivo.

1. In Visual Studio aggiungere un progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console** . Assegnare al progetto il nome **TriggerFWUpdate**.

    ![Nuovo progetto desktop di Windows classico in Visual C#][img-createserviceapp]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **TriggerFWUpdate** e quindi scegliere **Gestisci pacchetti NuGet**.
3. Nella finestra **Gestione pacchetti NuGet** selezionare **Esplora**, cercare **microsoft.azure.devices**, selezionare **Installa** per installare il pacchetto **Microsoft.Azure.Devices** e accettare le condizioni per l'uso. Questa procedura scarica, installa e aggiunge un riferimento al [pacchetto NuGet Azure IoT - SDK per dispositivi][lnk-nuget-service-sdk] e alle relative dipendenze.

    ![Finestra Gestione pacchetti NuGet][img-servicenuget]
4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Aggiungere i campi seguenti alla classe **Program** . Sostituire i valori dei segnaposto multipli con la stringa di connessione dell'hub IoT creato nella sezione precedente e l'ID del dispositivo.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Aggiungere il metodo seguente alla classe **Program**. Questo metodo esegue il polling per l'aggiornamento dello stato del dispositivo gemello ogni 500 millisecondi. Il metodo scrive nella console solo quando lo stato risulta modificato. Per questo esempio, per evitare l'uso di messaggi hub IoT aggiuntivi nella sottoscrizione, il polling si interrompe quando il dispositivo segnala lo stato **applyComplete** o un errore.  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. Aggiungere il metodo seguente alla classe **Program** :

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. Aggiungere infine le righe seguenti al metodo **Main** . Viene creato un manager registro per la lettura del dispositivo gemello, viene avviata l'attività di polling su un thread di lavoro e quindi viene attivato l'aggiornamento del firmware.
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. Compilare la soluzione.

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato
In questa sezione verrà illustrato come:

* Creare un'app console .NET che risponde a un metodo diretto chiamato dal cloud
* Simulare un aggiornamento del firmware attivato da un servizio back-end tramite un metodo diretto
* Usare le proprietà segnalate per abilitare le query nei dispositivi gemelli in modo da identificare i dispositivi e l'ora dell'ultimo completamento di un aggiornamento del firmware

1. In Visual Studio aggiungere un progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console** . Assegnare al progetto il nome **SimulateDeviceFWUpdate**.
   
    ![Nuova app per il dispositivo di Windows classico in Visual C#][img-createdeviceapp]
    
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **SimulateDeviceFWUpdate** e quindi scegliere **Gestisci pacchetti NuGet**.
3. Nella finestra **Gestione pacchetti NuGet** selezionare **Sfoglia** e cercare **microsoft.azure.devices.client**. Selezionare **Installa** per installare il pacchetto **microsoft.azure.devices.client** e accettare le condizioni d'uso. Questa procedura scarica, installa e aggiunge un riferimento al pacchetto NuGet [Azure IoT SDK per dispositivi][lnk-nuget-client-sdk] e alle relative dipendenze.
   
    ![App client della finestra Gestione pacchetti NuGet][img-clientnuget]
4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione del dispositivo annotata nella sezione **Creare un'identità del dispositivo**.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Aggiungere il metodo seguente per segnalare lo stato al cloud tramite il dispositivo gemello: 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. Aggiungere il metodo seguente per simulare il download dell'immagine firmware:
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Aggiungere il metodo seguente per simulare l'applicazione dell'immagine firmware al dispositivo:
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  Aggiungere il metodo seguente per simulare l'attesa del download dell'immagine firmware. Aggiornare lo stato a **waiting** e cancellare le altre proprietà di aggiornamento firmware sul dispositivo gemello. Queste proprietà vengono cancellate per rimuovere valori esistenti derivanti da aggiornamenti precedenti del firmware. L'operazione è necessaria perché le proprietà segnalate vengono inviate come operazione PATCH (delta) e non come operazione PUT (set completo di proprietà che sostituisce tutti i valori precedenti). In genere i dispositivi vengono informati che è disponibile un aggiornamento e i criteri definiti dall'amministratore fanno in modo che il dispositivo avvii il download e applichi l'aggiornamento. In questa funzione viene eseguita la logica per abilitare tali criteri. 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. Aggiungere il metodo seguente per eseguire il download. Il metodo aggiorna lo stato a **downloading** attraverso il dispositivo gemello, chiama il metodo simulateDownloadImage e segnala lo stato **downloadComplete** o **downloadFailed** attraverso il dispositivo gemello a seconda del risultato dell'operazione di download. 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. Aggiungere il metodo seguente per applicare l'immagine. Il metodo aggiorna lo stato a **applying** attraverso il dispositivo gemello, chiama il metodo simulateApplyImage e aggiorna lo stato a **applyComplete** o **applyFailed** attraverso il dispositivo gemello a seconda del risultato dell'operazione di applicazione. 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. Aggiungere il metodo seguente per suddividere in sequenze l'operazione di aggiornamento del firmware, dall'attesa del download dell'immagine fino all'applicazione dell'immagine al dispositivo:
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. Aggiungere il metodo seguente per gestire il metodo diretto **updateFirmware** dal cloud. Il metodo estrae l'URL per l'aggiornamento del firmware dal payload del messaggio e lo passa all'attività **doUpdate**, che viene avviata in un altro thread del pool di thread. Quindi restituisce immediatamente la risposta del metodo al cloud.
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> Questo metodo avvia l'aggiornamento simulato per l'esecuzione come **Task** e quindi risponde immediatamente alla chiamata del metodo, informando il servizio che l'aggiornamento del firmware è stato avviato. Lo stato dell'aggiornamento e il completamento vengono inviati al servizio mediante le proprietà reported del dispositivo gemello. La risposta alla chiamata del metodo viene effettuata all'avvio dell'aggiornamento anziché dopo il suo completamento, per le ragioni seguenti:
> * In genere un processo di aggiornamento reale richiede un tempo superiore al timeout di chiamata del metodo.
> * È molto probabile che un processo di aggiornamento richieda un riavvio, con conseguente riavvio dell'app e non disponibilità dell'oggetto **MethodRequest**. L'aggiornamento delle proprietà reported è invece possibile anche dopo il riavvio. 

14. Infine aggiungere il codice seguente al metodo **Main** per aprire la connessione all'hub IoT e inizializzare il listener del metodo:
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. Compilare la soluzione.       

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri per i tentativi, ad esempio un backoff esponenziale, come illustrato nell'articolo di MSDN [Transient Fault Handling][lnk-transient-faults] (Gestione degli errori temporanei).


## <a name="run-the-apps"></a>Eseguire le app
A questo punto è possibile eseguire le app.
1. Eseguire l'app per dispositivi .NET **SimulatedDeviceFWUpdate**.  Fare clic con il pulsante destro del mouse sul progetto **SimulatedDeviceFWUpdate**, scegliere **Debug** e quindi selezionare **Avvia nuova istanza**. Dovrebbe iniziare ad ascoltare le chiamate di metodo provenienti dall'IoT Hub: 

2. Quando il dispositivo è connesso e in attesa di chiamate del metodo, eseguire l'app .NET **TriggerFWUpdate** per richiamare il metodo di aggiornamento del firmware nell'app per dispositivo simulato. Fare clic con il pulsante destro del mouse sul progetto **TriggerFWUpdate**, scegliere **Debug** e quindi selezionare **Avvia nuova istanza**. La sequenza di aggiornamento viene visualizzata nella console **SimulatedDeviceFWUpdate** e la sequenza segnalata mediante le proprietà reported del dispositivo viene visualizzata nella console **TriggerFWUpdate**. Il completamento del processo richiede alcuni secondi. 
   
    ![Esecuzione del servizio e dell'app per dispositivi][img-combinedrun]


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato usato un metodo diretto per attivare un aggiornamento del firmware remoto in un dispositivo e sono state usate le proprietà segnalate per conoscere lo stato del processo di aggiornamento del firmware.

Per informazioni su come estendere la soluzione IoT e pianificare le chiamate al metodo su più dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-tutorial-jobs].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/