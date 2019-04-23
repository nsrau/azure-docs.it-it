---
title: Introduzione alla gestione dei dispositivi dell'hub IoT di Azure (.NET/.NET) | Microsoft Docs
description: Come usare la gestione dei dispositivi dell'hub IoT di Azure per riavviare un dispositivo remoto. Si usa Azure IoT SDK per dispositivi per .NET per implementare un'app per dispositivi simulata che include un metodo diretto e Azure IoT SDK per servizi per .NET per implementare un'app di servizio che richiama il metodo diretto.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: robinsh
ms.openlocfilehash: 556c10cc5ec5e528857a120dadb16c2a10202ed3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788962"
---
# <a name="get-started-with-device-management-netnet"></a>Introduzione alla gestione dei dispositivi (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Questa esercitazione illustra come:

* Creare un hub IoT nel portale di Azure e un'identità del dispositivo nell'hub IoT.

* Creare un'app di dispositivo simulato contenente un metodo diretto per il riavvio del dispositivo. I metodi diretti vengono richiamati dal cloud.

* Creare un'app console .NET che chiama il metodo diretto di riavvio nell'app per dispositivo simulato tramite l'hub IoT.

Al termine di questa esercitazione si ottengono due app console .NET:

* **SimulateManagedDevice**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza, riceve un metodo diretto per il riavvio, simula un riavvio fisico e segnala il tempo impiegato per l'ultimo riavvio.

* **TriggerReboot**, che chiama un metodo diretto nell'app per dispositivo simulato, visualizza la risposta e le proprietà segnalate aggiornate.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Visual Studio 2017.

* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Ottenere la stringa di connessione per l'hub IoT

[!INCLUDE [iot-hub-find-include-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Attivare un riavvio remoto nel dispositivo con un metodo diretto

In questa sezione viene creata un'app console .NET (tramite C#) che attiva un riavvio remoto su un dispositivo usando un metodo diretto. L'app esegue query sul dispositivo gemello per ottenere l'ora dell'ultimo riavvio per il dispositivo.

1. In Visual Studio aggiungere un progetto desktop di Windows classico Visual C# a una nuova soluzione usando il modello di progetto **App console (.NET Framework)**. Verificare che la versione di .NET Framework sia 4.5.1 o successiva. Assegnare al progetto il nome **TriggerReboot**.

    ![Nuovo progetto desktop di Windows classico in Visual C#](./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png)

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **TriggerReboot** e quindi fare clic su **Gestisci pacchetti NuGet**.

3. Nella finestra **Gestione pacchetti NuGet** selezionare **Esplora**, cercare **Microsoft.Azure.Devices**, selezionare **Installa** per installare il pacchetto **Microsoft.Azure.Devices** e accettare le condizioni per l'uso. Questa procedura scarica, installa e aggiunge un riferimento al pacchetto NuGet [Azure IoT SDK per dispositivi](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e alle relative dipendenze.

    ![Finestra Gestione pacchetti NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png)

4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```
        
5. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione dell'hub IoT per l'hub creato nella sezione "Creare un hub IoT". 
   
   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

6. Aggiungere il metodo seguente alla classe **Program**.  Questo codice ottiene il dispositivo gemello per il dispositivo in fase di riavvio e restituisce le proprietà segnalate.
   
   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```
        
7. Aggiungere il metodo seguente alla classe **Program**.  Il codice attiva il riavvio nel dispositivo usando un metodo diretto.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

7. Aggiungere infine le righe seguenti al metodo **Main** :
   
   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

8. Compilare la soluzione.

> [!NOTE]
> Questa esercitazione esegue un'unica query per le proprietà segnalate del dispositivo. Nel codice di produzione è consigliabile eseguire il polling per rilevare le modifiche nelle proprietà segnalate.

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato

In questa sezione vengono eseguite le attività seguenti:

* Creare un'app console .NET che risponde a un metodo diretto chiamato dal cloud.

* Attivare un riavvio del dispositivo simulato.

* Usare le proprietà segnalate per abilitare le query nei dispositivi gemelli in modo da identificare i dispositivi e l'ora dell'ultimo riavvio.

1. In Visual Studio aggiungere un progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console** . Denominare il progetto **SimulateManagedDevice**.
   
    ![Nuova app per il dispositivo di Windows classico in Visual C#](./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png)
    
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **SimulateManagedDevice** e quindi scegliere **Gestisci pacchetti NuGet**.

3. Nella finestra **Gestione pacchetti NuGet** selezionare **Sfoglia** e cercare **Microsoft.Azure.Devices.Client**. Selezionare **Installa** per installare il pacchetto **microsoft.azure.devices.client** e accettare le condizioni d'uso. Questa procedura scarica, installa e aggiunge un riferimento al pacchetto [NuGet Azure IoT SDK per dispositivi](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e alle relative dipendenze.
   
    ![App client della finestra Gestione pacchetti NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png)
    
4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione del dispositivo annotato nella sezione precedente.

    ```csharp
    static string DeviceConnectionString = 
      "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```
6. Aggiungere quanto segue per implementare il metodo diretto nel dispositivo:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = "'Reboot started.'";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

7. Infine aggiungere il codice seguente al metodo **Main** per aprire la connessione all'hub IoT e inizializzare il listener del metodo:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```
        
8. In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Imposta progetti di avvio...**. Selezionare **Progetto di avvio singolo** e quindi selezionare il progetto **SimulateManagedDevice** nel menu a discesa. Compilare la soluzione.       

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come suggerito nell'articolo [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Eseguire le app

A questo punto è possibile eseguire le app.

1. Per eseguire l'app per dispositivi .NET **SimulateManagedDevice**. fare clic con il pulsante destro del mouse sul progetto **SimulateManagedDevice**, scegliere **Debug**, quindi selezionare **Avvia nuova istanza**. Dovrebbe iniziare ad ascoltare le chiamate di metodo provenienti dall'IoT hub. 

2. Quando il dispositivo è connesso e in attesa di chiamate del metodo, eseguire l'app .NET **TriggerReboot** per richiamare il metodo di riavvio nell'app per dispositivo simulato. Per eseguire questa operazione, fare clic con il pulsante destro del mouse sul progetto **TriggerReboot**, scegliere **Debug**, quindi selezionare **Avvia nuova istanza**. Dovrebbero essere restituiti il messaggio "Riavvio" nella console dell'app **SimulatedManagedDevice** e le proprietà segnalate del dispositivo, che includono l'ora dell'ultimo riavvio, nella console dell'app **TriggerReboot**.
   
    ![Esecuzione del servizio e dell'app per dispositivi](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
