---
title: Introduzione all'identità del modulo e ai moduli gemelli dell'hub IoT di Azure (portale e .NET) | Microsoft Docs
description: Come creare l'identità del modulo e aggiornare il modulo gemello usando il portale e .NET.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: dobett
ms.openlocfilehash: b4502dfc8f856516989326c8d748a5d13fdba02b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634591"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Creare l'identità del modulo e i moduli gemelli dell'hub IoT con il portale e l'SDK per dispositivi .NET

> [!NOTE]
> [Le identità del modulo e i moduli gemelli](iot-hub-devguide-module-twins.md) sono simili alle identità del dispositivo e ai dispositivi gemelli dell'hub IoT di Azure, ma offrono una granularità superiore. Mentre l'identità del dispositivo e il dispositivo gemello dell'hub IoT di Azure consentono all'applicazione back-end di configurare un dispositivo e forniscono visibilità sulle condizioni del dispositivo, l'identità del modulo e il modulo gemello forniscono queste funzionalità per i singoli componenti di un dispositivo. Nei dispositivi con più componenti, ad esempio i dispositivi basati su sistema operativo o i dispositivi firmware, è possibile isolare la configurazione e le condizioni di ogni componente.

In questa esercitazione si apprenderà come: 
1. Creare un'identità del modulo nel portale. 
2. Usare l'SDK per dispositivi .NET per aggiornare il modulo gemello dal dispositivo.

> [!NOTE]
> Per informazioni sugli Azure IoT SDK che consentono di compilare le applicazioni da eseguire nei dispositivi e i back-end della soluzione, vedere [Azure IoT SDK][lnk-hub-sdks].

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Visual Studio 2015 o Visual Studio 2017.
* Un account Azure attivo. Se non si dispone di un account, è possibile creare un [account di valutazione gratuita] in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

## <a name="create-a-device-identity-in-the-portal"></a>Creare un'identità del dispositivo nel portale

L'hub IoT è stato creato. Aprire il [portale](https://portal.azure.com) e passare all'hub IoT. Fare clic su Dispositivi IoT e quindi su Aggiungi per creare un'identità del dispositivo. Assegnarle il nome **MyFirstDevice**. 

![Creare l'identità del dispositivo][8]

Dopo il salvataggio, nell'elenco delle identità del dispositivo si vedrà che l'identità MyFirstDevice è stata creata correttamente.

![ID dispositivo creato][11]

Ora fare clic sulla riga. Verranno visualizzati i dettagli del dispositivo.

![Dettagli dispositivo][10]

## <a name="create-a-module-identity-in-the-portal"></a>Creare un'identità del modulo nel portale

In una identità del dispositivo è possibile creare fino a 20 identità del modulo. Fare clic sul pulsante **Add Module Identity** (Aggiungi identità modulo) in alto per creare la prima identità del modulo, denominata **myFirstModule**. 

![Dettagli dispositivo][9]

Salvare e fare clic sull'identità del modulo appena creata. Verranno visualizzati i dettagli dell'identità del modulo. Salvare la stringa di connessione - chiave primaria Verrà usata nelle prossima sezione per configurare il modulo nel dispositivo.

![Dettagli dispositivo][12]

<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-net-device-sdk"></a>Aggiornare il modulo gemello usando l'SDK per dispositivi .NET

L'identità del modulo è stata correttamente creata nell'hub IoT. Proviamo ora a comunicare con il cloud dal dispositivo simulato. Una volta creata un'identità del modulo, viene implicitamente creato anche un modulo gemello nell'hub IoT. In questa sezione, nel dispositivo simulato verrà creata un'app console .NET che aggiorna le proprietà segnalate del modulo gemello.

1. **Creare un progetto di Visual Studio** - In Visual Studio aggiungere un progetto desktop di Windows classico Visual C# alla soluzione esistente usando il modello di progetto **App console (.NET Framework)**. Verificare che la versione di .NET Framework sia 4.6.1 o successiva. Assegnare al progetto il nome **UpdateModuleTwinReportedProperties**.

    ![Creare un progetto di Visual Studio][13]

2. **Installare la versione più recente di Azure IoT SDK per dispositivi .NET** - L'identità del modulo e il modulo gemello sono in anteprima pubblica. Sono disponibili solo nella versione preliminare di Azure IoT SDK per dispositivi. In Visual Studio aprire Strumenti > Gestione pacchetti NuGet > Gestisci pacchetti NuGet per la soluzione. Cercare Microsoft.Azure.Devices.Client. Verificare di aver selezionato la casella di controllo Includi versione preliminare. Selezionare la versione più recente e installarla. Ora si ha accesso a tutte le funzionalità dei moduli. 

    ![Installare Azure IoT SDK per dispositivi .NET V1.16.0-preview-005][14]

3. **Ottenere la stringa di connessione del modulo** - Accedere al [portale di Azure][lnk-portal]. Passare all'hub IoT e fare clic su Dispositivi IoT. Trovare myFirstDevice e aprirlo. Si vedrà che myFirstModule è stato creato correttamente. Copiare la stringa di connessione del modulo. Sarà necessaria nel prossimo passaggio.

    ![Dettagli del modulo nel portale di Azure] [15]

4. **Creare l'app console UpdateModuleTwinReportedProperties** Aggiungere le istruzioni `using` seguenti all'inizio del file **Program.cs**:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

    Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione del modulo.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>“;
    private static ModuleClient Client = null;
    ```

    Aggiungere il metodo **OnDesiredPropertyChanged** seguente alla classe **Program**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    Aggiungere infine le righe seguenti al metodo **Main** :

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin));

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Client.CloseAsync().Wait();
    }
    ```

    Questo esempio di codice mostra come recuperare il modulo gemello e aggiornare le proprietà segnalate con il protocollo AMQP. L'anteprima pubblica supporta AMQP solo per le operazioni di modulo gemello.
    ```

## Run the apps

You are now ready to run the apps. In Visual Studio, in Solution Explorer, right-click your solution, and then click **Set StartUp projects**. Select **Multiple startup projects**, and then select **Start** as the action for the console app. And then press F5 to start both apps running. 

## Next steps

To continue getting started with IoT Hub and to explore other IoT scenarios, see:

* [Get started with IoT Hub module identity and module twin using .NET backup and .NET device][lnk-csharp-csharp-getstarted]
* [Getting started with IoT Edge][lnk-iot-edge]


<!-- Images. -->
[8]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-device-id.JPG
[9]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[10]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-details.JPG
[11]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-id-created.JPG
[12]:./media\iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
