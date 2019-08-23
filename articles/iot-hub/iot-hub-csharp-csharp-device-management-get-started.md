---
title: Introduzione alla gestione dei dispositivi dell'hub IoT di Azure (.NET/.NET) | Microsoft Docs
description: Come usare la gestione dei dispositivi dell'hub IoT di Azure per riavviare un dispositivo remoto. Si usa Azure IoT SDK per dispositivi per .NET per implementare un'app per dispositivi simulata che include un metodo diretto e Azure IoT SDK per servizi per .NET per implementare un'app di servizio che richiama il metodo diretto.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 5a0be55df9be67fcf6ff5d53e18e3eb2b0e69d7f
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904469"
---
# <a name="get-started-with-device-management-net"></a>Introduzione alla gestione dei dispositivi (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Questa esercitazione illustra come:

* Creare un hub IoT nel portale di Azure e un'identità del dispositivo nell'hub IoT.

* Creare un'app di dispositivo simulato contenente un metodo diretto per il riavvio del dispositivo. I metodi diretti vengono richiamati dal cloud.

* Creare un'app console .NET che chiama il metodo diretto di riavvio nell'app per dispositivo simulato tramite l'hub IoT.

Al termine di questa esercitazione si ottengono due app console .NET:

* **SimulateManagedDevice**. Questa app si connette all'hub delle cose con l'identità del dispositivo creata in precedenza, riceve un metodo diretto di riavvio, simula un riavvio fisico e segnala il tempo per l'ultimo riavvio.

* **TriggerReboot**. Questa app chiama un metodo diretto nell'app per dispositivo simulato, Visualizza la risposta e visualizza le proprietà segnalate aggiornate.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* Visual Studio.

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Attivare un riavvio remoto nel dispositivo con un metodo diretto

In questa sezione si crea un'app console .NET, usando C#, che avvia un riavvio remoto su un dispositivo usando un metodo diretto. L'app esegue query nel dispositivo gemello per ottenere l'ora dell'ultimo riavvio del dispositivo in questione.

1. In Visual Studio selezionare **Crea un nuovo progetto**.

1. In **creare un nuovo progetto**individuare e selezionare il modello di progetto **App Console (.NET Framework)** , quindi fare clic su **Avanti**.

1. In **configurare il nuovo progetto**assegnare al progetto il nome *TriggerReboot*e selezionare .NET Framework versione 4.5.1 o successiva. Selezionare **Create**.

    ![Nuovo progetto desktop di Windows classico in Visual C#](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **TriggerReboot** e quindi scegliere **Gestisci pacchetti NuGet**.

1. Selezionare **Sfoglia**, quindi cercare e selezionare **Microsoft. Azure. Devices**. Selezionare **Installa** per installare il pacchetto **Microsoft. Azure. Devices** .

    ![Finestra Gestione pacchetti NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Questa procedura scarica, installa e aggiunge un riferimento al pacchetto NuGet [Azure IoT SDK per servizi](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e alle relative dipendenze.

1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Aggiungere i campi seguenti alla classe **Program** . Sostituire il `{iot hub connection string}` valore del segnaposto con la stringa di connessione dell'hub Internet che è stata copiata in precedenza in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string).

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Aggiungere il metodo seguente alla classe **Program**.  Questo codice ottiene il dispositivo gemello per il dispositivo in fase di riavvio e restituisce le proprietà segnalate.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Aggiungere il metodo seguente alla classe **Program**.  Il codice attiva il riavvio nel dispositivo usando un metodo diretto.

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

1. Aggiungere infine le righe seguenti al metodo **Main** :

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Selezionare **Compila** > **Compila soluzione**.

> [!NOTE]
> Questa esercitazione esegue un'unica query per le proprietà segnalate del dispositivo. Nel codice di produzione è consigliabile eseguire il polling per rilevare le modifiche nelle proprietà segnalate.

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato

In questa sezione verrà illustrato come:

* Creare un'app console .NET che risponde a un metodo diretto chiamato dal cloud.

* Attivare un riavvio del dispositivo simulato.

* Usare le proprietà segnalate per abilitare le query nei dispositivi gemelli in modo da identificare i dispositivi e l'ora dell'ultimo riavvio.

Per creare l'app per dispositivo simulato, seguire questa procedura:

1. In Visual Studio, nella soluzione TriggerReboot già creata, selezionare **file** > **nuovo** > **progetto**. In **creare un nuovo progetto**individuare e selezionare il modello di progetto **App Console (.NET Framework)** , quindi fare clic su **Avanti**.

1. In **Configura il nuovo progetto**, denominare il progetto *SimulateManagedDevice*e per **soluzione**selezionare **Aggiungi a soluzione**. Selezionare **Create**.

    ![Nome e aggiunta del progetto alla soluzione](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nuovo progetto **SimulateManagedDevice** , quindi scegliere **Gestisci pacchetti NuGet**.

1. Selezionare **Sfoglia**, quindi cercare e selezionare **Microsoft. Azure. Devices. client**. Selezionare **Installa**.

    ![App client della finestra Gestione pacchetti NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Questo passaggio consente di scaricare, installare e aggiungere un riferimento al pacchetto NuGet [Azure Azure per dispositivi SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e alle relative dipendenze.

1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Aggiungere i campi seguenti alla classe **Program** . Sostituire il `{device connection string}` valore del segnaposto con la stringa di connessione del dispositivo annotata in precedenza in [registrare un nuovo dispositivo nell'hub](#register-a-new-device-in-the-iot-hub).

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Aggiungere quanto segue per implementare il metodo diretto nel dispositivo:

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

1. Infine aggiungere il codice seguente al metodo **Main** per aprire la connessione all'hub IoT e inizializzare il listener del metodo:

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

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Imposta progetti di avvio**.

1. Per**progetto di avvio** **Proprietà** > comuni selezionare **progetto di avvio singolo**, quindi selezionare il progetto **SimulateManagedDevice** . Selezionare **OK** per salvare le modifiche.

1. Selezionare **Compila** > **Compila soluzione**.

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come suggerito in [gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Eseguire le app

A questo punto si è pronti per eseguire le app.

1. Per eseguire l'app per dispositivi .NET **SimulateManagedDevice**, in Esplora soluzioni, fare clic con il pulsante destro del mouse sul progetto **SimulateManagedDevice** , scegliere **debug**e quindi selezionare **Avvia nuova istanza**. L'app dovrebbe iniziare ad ascoltare le chiamate ai metodi dall'hub Internet.

1. Dopo che il dispositivo è connesso e in attesa di chiamate al metodo, fare clic con il pulsante destro del mouse sul progetto **TriggerReboot** , scegliere **debug**e quindi selezionare **Avvia nuova istanza**.

   Dovrebbero essere restituiti il messaggio "Riavvio" nella console dell'app **SimulatedManagedDevice** e le proprietà segnalate del dispositivo, che includono l'ora dell'ultimo riavvio, nella console dell'app **TriggerReboot**.

    ![Esecuzione del servizio e dell'app per dispositivi](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
