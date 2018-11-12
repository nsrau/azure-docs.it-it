---
title: Pianificare processi con l'hub IoT di Azure (.NET/.NET) | Microsoft Docs
description: Come pianificare un processo dell'hub IoT di Azure per richiamare un metodo diretto su più dispositivi. Usare Azure IoT SDK per dispositivi per .NET per implementare le app per dispositivi simulati un'app di servizio per eseguire il processo.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: dobett
ms.openlocfilehash: d97fe45d728b156d72e22a5b255d149f7a02c7f3
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261044"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Pianificare e trasmettere processi (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Usare l'hub IoT per pianificare e tenere traccia dei processi che aggiornano milioni di dispositivi. Usare i processi per:

* Aggiornare le proprietà desiderate
* Aggiornare i tag
* Richiamare metodi diretti

Un processo esegue il wrapping di una di queste azioni e tiene traccia dell'esecuzione rispetto a un set di dispositivi, definito da una query su dispositivi gemelli. Ad esempio, un'applicazione back-end può usare un processo per chiamare un metodo diretto su 10.000 dispositivi che riavvia i dispositivi stessi. È necessario specificare il set di dispositivi con una query di dispositivi gemelli e pianificare il processo in modo che venga eseguito in un secondo momento. L'applicazione può quindi tenere traccia dell'avanzamento mentre ognuno dei dispositivi riceve ed esegue il metodo diretto di riavvio.

Per altre informazioni su queste funzionalità, vedere:

* Dispositivi gemelli e proprietà: [Introduzione ai dispositivi gemelli](iot-hub-csharp-csharp-twin-getstarted.md) ed [Esercitazione: Come usare le proprietà dei dispositivi gemelli](tutorial-device-twins.md)

* Metodi diretti: [Guida per sviluppatori dell'hub IoT - Metodi diretti](iot-hub-devguide-direct-methods.md) ed [Esercitazione: Usare metodi diretti](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione illustra come:

* Creare un'app per dispositivi che implementa un metodo diretto denominato **LockDoor** che può essere chiamato dall'app back-end.

* Creare un'app back-end che crea un processo per chiamare il metodo diretto **LockDoor** su più dispositivi. Un altro processo invia gli aggiornamenti di proprietà desiderati a più dispositivi.

Al termine di questa esercitazione si ottengono due app console .NET (C#):

**SimulateDeviceMethods** che si connette all'hub IoT e implementa il metodo diretto **LockDoor**.

**ScheduleJob** che usa i processi per chiamare il metodo diretto **LockDoor** e aggiornare le proprietà di dispositivi gemelli desiderate su più dispositivi.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Visual Studio 2017.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato

In questa sezione viene creata un'app console .NET che risponde a un metodo diretto chiamato dal back-end della soluzione.

1. In Visual Studio aggiungere un progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console** . Assegnare al progetto il nome **SimulateDeviceMethods**.
   
    ![Nuova app per il dispositivo di Windows classico in Visual C#](./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png)
    
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **SimulateDeviceMethods**, quindi scegliere **Gestisci pacchetti NuGet...**.

3. Nella finestra **Gestione pacchetti NuGet** selezionare **Sfoglia** e cercare **Microsoft.Azure.Devices.Client**. Selezionare **Installa** per installare il pacchetto **microsoft.azure.devices.client** e accettare le condizioni d'uso. Questa procedura scarica, installa e aggiunge un riferimento al pacchetto [NuGet Azure IoT SDK per dispositivi](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e alle relative dipendenze.
   
    ![App client della finestra Gestione pacchetti NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione del dispositivo annotato nella sezione precedente:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

6. Aggiungere quanto segue per implementare il metodo diretto nel dispositivo:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

7. Aggiungere quanto segue per implementare nel dispositivo il listener del dispositivo gemello:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

8. Infine aggiungere il codice seguente al metodo **Main** per aprire la connessione all'hub IoT e inizializzare il listener del metodo:
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
9. Salvare il lavoro e compilare la soluzione.         

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio dei tentativi di connessione, come suggerito nell'articolo [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Pianificare i processi per chiamare un metodo diretto e inviare gli aggiornamenti dei dispositivi gemelli

In questa sezione si crea un'app console .NET (usando C#) che usa i processi per chiamare il metodo diretto **LockDoor** e inviare gli aggiornamenti di proprietà desiderati a più dispositivi.

1. In Visual Studio aggiungere un progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console** . Assegnare al progetto il nome **ScheduleJob**.

    ![Nuovo progetto desktop di Windows classico in Visual C#](./media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png)

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ScheduleJob** e quindi scegliere **Gestisci pacchetti NuGet**.

3. Nella finestra **Gestione pacchetti NuGet** selezionare **Esplora**, cercare **Microsoft.Azure.Devices**, selezionare **Installa** per installare il pacchetto **Microsoft.Azure.Devices** e accettare le condizioni per l'uso. Questa procedura scarica, installa e aggiunge un riferimento al pacchetto NuGet [Azure IoT SDK per servizi](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e alle relative dipendenze.

    ![Finestra Gestione pacchetti NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png)

4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Aggiungere l'istruzione `using` seguente se non è già presente nelle istruzioni predefinite.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

6. Aggiungere i campi seguenti alla classe **Program** . Sostituire i segnaposto con la stringa di connessione dell'hub IoT creato nella sezione precedente e il nome del dispositivo.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

7. Aggiungere il metodo seguente alla classe **Program** :

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

8. Aggiungere il metodo seguente alla classe **Program** :

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

9. Aggiungere un altro metodo alla classe **Program**:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Per altre informazioni sulla sintassi della query, vedere [Linguaggio di query di hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
    > 

10. Aggiungere infine le righe seguenti al metodo **Main** :

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

11. Salvare il lavoro e compilare la soluzione. 

## <a name="run-the-apps"></a>Eseguire le app

A questo punto è possibile eseguire le app.

1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Compila**. **Progetti di avvio multipli**. Assicurarsi che `SimulateDeviceMethods` si trovi all'inizio dell'elenco, seguito da `ScheduleJob`. Impostare le azioni di entrambi su **Avvia** e fare clic su **OK**.

2. Eseguire i progetti facendo clic su **Avvia** o passare al menu **Debug** e scegliere **Avvia debug**.

3. L'output viene visualizzato sia dal dispositivo che dalle app back-end.

    ![Eseguire le app per pianificare i processi](./media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato un processo per pianificare un metodo diretto in un dispositivo e aggiornare le proprietà di un dispositivo gemello.

Per altre informazioni sull'hub IoT e sui modelli di gestione dei dispositivi, ad esempio in modalità remota tramite l'aggiornamento del firmware air, vedere [Esercitazione: Come eseguire un aggiornamento del firmware](tutorial-firmware-update.md).

Per altre informazioni sulla distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge, vedere la [Guida introduttiva di IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).