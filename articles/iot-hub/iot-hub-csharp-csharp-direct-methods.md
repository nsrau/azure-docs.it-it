---
title: Usare metodi diretti dell'Hub IoT di Azure (.NET/.NET) | Microsoft Docs
description: Come usare metodi diretti dell'Hub IoT di Azure. Si usa Azure IoT SDK per dispositivi per .NET per implementare un'app per dispositivi simulata che include un metodo diretto e Azure IoT SDK per servizi per .NET per implementare un'app di servizio che richiama il metodo diretto.
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: dkshir
ms.openlocfilehash: 9ce1fbebb6417c10618aa182e3c1d9ddf8132fb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-netnet"></a>Usare metodi diretti (.NET/.NET)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

In questa esercitazione verranno sviluppate due app console .NET:

* **CallMethodOnDevice**, un'app back-end .NET, che chiama un metodo nell'app per dispositivi simulata e visualizza la risposta.
* **SimulateDeviceMethods**, un'app console che simula un dispositivo che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e risponde al metodo chiamato dal cloud.

> [!NOTE]
> L'articolo [Azure IoT SDK][lnk-hub-sdks] offre informazioni sui vari Azure IoT SDK che è possibile usare per compilare applicazioni da eseguire nei dispositivi e il backend della soluzione.
> 
> 

Per completare questa esercitazione, sono necessari:

* Visual Studio 2015 o Visual Studio 2017.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Se invece si desidera creare l'identità del dispositivo a livello di codice, leggere la sezione corrispondente nell'articolo [Connettere il dispositivo simulato all'hub IoT tramite .NET][lnk-device-identity-csharp].


## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato
In questa sezione viene creata un'app console .NET che risponde a un metodo chiamato dal back-end della soluzione.

1. In Visual Studio aggiungere un progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console** . Assegnare al progetto il nome **SimulateDeviceMethods**.
   
    ![Nuova app per il dispositivo di Windows classico in Visual C#][img-createdeviceapp]
    
1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **SimulateDeviceMethods**, quindi scegliere **Gestisci pacchetti NuGet...**.
1. Nella finestra **Gestione pacchetti NuGet** selezionare **Sfoglia** e cercare **microsoft.azure.devices.client**. Selezionare **Installa** per installare il pacchetto **microsoft.azure.devices.client** e accettare le condizioni d'uso. Questa procedura scarica, installa e aggiunge un riferimento al pacchetto NuGet [Azure IoT SDK per dispositivi][lnk-nuget-client-sdk] e alle relative dipendenze.
   
    ![App client della finestra Gestione pacchetti NuGet][img-clientnuget]
1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

1. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione del dispositivo annotato nella sezione precedente.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. Aggiungere quanto segue per implementare il metodo diretto nel dispositivo:

        static Task<MethodResponse> WriteLineToConsole(MethodRequest methodRequest, object userContext)
        {
            Console.WriteLine();
            Console.WriteLine("\t{0}", methodRequest.DataAsJson);
            Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

            string result = "'Input was written to log.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

1. Infine aggiungere il codice seguente al metodo **Main** per aprire la connessione all'hub IoT e inizializzare il listener del metodo:
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "writeLine" method
            Client.SetMethodHandlerAsync("writeLine", WriteLineToConsole, null).Wait();
            Console.WriteLine("Waiting for direct method call\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "writeLine" handler
            Client.SetMethodHandlerAsync("writeLine", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
1. In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Imposta progetti di avvio...**. Selezionare **Progetto di avvio singolo**, quindi selezionare il progetto **SimulateDeviceMethods** nel menu a discesa.        

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio i tentativi di connessione, come indicato nell'articolo di MSDN [Transient Fault Handling][lnk-transient-faults] (Gestione degli errori temporanei).
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>Chiamare un metodo diretto in un dispositivo
In questa sezione viene creata un'app console .NET che chiama un metodo nell'app per dispositivo simulato e quindi visualizza la risposta.

1. In Visual Studio aggiungere un progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console** . Verificare che la versione di .NET Framework sia 4.5.1 o successiva. Assegnare al progetto il nome **CallMethodOnDevice**.
   
    ![Nuovo progetto desktop di Windows classico in Visual C#][img-createserviceapp]
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **CallMethodOnDevice** e quindi scegliere **Gestisci pacchetti NuGet**.
3. Nella finestra **Gestione pacchetti NuGet** selezionare **Esplora**, cercare **microsoft.azure.devices**, selezionare **Installa** per installare il pacchetto **Microsoft.Azure.Devices** e accettare le condizioni per l'uso. Questa procedura scarica, installa e aggiunge un riferimento al [pacchetto NuGet Azure IoT - SDK per dispositivi][lnk-nuget-service-sdk] e alle relative dipendenze.
   
    ![Finestra Gestione pacchetti NuGet][img-servicenuget]

4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione dell'hub IoT creato nella sezione precedente.
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Aggiungere il metodo seguente alla classe **Program** :
   
        private static async Task InvokeMethod()
        {
            var methodInvocation = new CloudToDeviceMethod("writeLine") { ResponseTimeout = TimeSpan.FromSeconds(30) };
            methodInvocation.SetPayloadJson("'a line to be written'");

            var response = await serviceClient.InvokeDeviceMethodAsync("myDeviceId", methodInvocation);

            Console.WriteLine("Response status: {0}, payload:", response.Status);
            Console.WriteLine(response.GetPayloadAsJson());
        }
   
    Questo metodo richiama un metodo diretto con nome `writeLine` nel dispositivo `myDeviceId`. Scrive quindi la risposta specificata dal dispositivo nella console. Si noti che è possibile specificare un valore di timeout per la risposta del dispositivo.
7. Aggiungere infine le righe seguenti al metodo **Main** :
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Imposta progetti di avvio...**. Selezionare **Progetto di avvio singolo**, quindi selezionare il progetto **CallMethodOnDevice** nel menu a discesa.

## <a name="run-the-applications"></a>Eseguire le applicazioni
A questo punto è possibile eseguire le applicazioni.

1. Eseguire l'app per dispositivi .NET **SimulateDeviceMethods**. Dovrebbe iniziare ad ascoltare le chiamate di metodo provenienti dall'IoT Hub: 

    ![Esecuzione dell'app per dispositivi][img-deviceapprun]
1. Quando il dispositivo è connesso e in attesa di chiamate del metodo, eseguire l'app .NET **CallMethodOnDevice** per richiamare il metodo nell'app per dispositivo simulato. La risposta del dispositivo dovrebbe essere scritta nella console.
   
    ![Esecuzione dell'app di servizio][img-serviceapprun]
1. Il dispositivo risponde quindi al metodo stampando questo messaggio:
   
    ![Metodo diretto chiamato sul dispositivo][img-directmethodinvoked]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato configurato un nuovo hub IoT nel Portale di Azure ed è stata quindi creata un'identità del dispositivo nel registro di identità dell'hub IoT. Questa identità del dispositivo è stata usata per consentire all'app del dispositivo simulato di reagire ai metodi richiamati dal cloud. È stata anche creata un'applicazione che richiama i metodi sul dispositivo e visualizza la risposta dal dispositivo. 

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Introduzione all'hub IoT]
* [Pianificare processi in più dispositivi][lnk-devguide-jobs]

Per informazioni su come estendere la soluzione IoT e pianificare le chiamate al metodo su più dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-tutorial-jobs].

<!-- Images. -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-direct-methods/device-app-nuget.png
[img-createserviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-service-app.png
[img-servicenuget]: ./media/iot-hub-csharp-csharp-direct-methods/service-app-nuget.png
[img-deviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-device-app.png
[img-serviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-service-app.png
[img-directmethodinvoked]: ./media/iot-hub-csharp-csharp-direct-methods/direct-method-invoked.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[Introduzione all'hub IoT]: iot-hub-node-node-getstarted.md
