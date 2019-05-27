---
title: Introduzione all'identità del modulo e ai moduli gemelli dell'hub IoT di Azure (.NET) | Microsoft Docs
description: Come creare l'identità del modulo e aggiornare il modulo gemello usando gli SDK per IoT per .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 5421423441d03a7375feea2ca0dfe289993145ee
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873215"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-net-back-end-and-net-device"></a>Introduzione all'identità del modulo e ai moduli gemelli dell'hub IoT usando il back-end .NET e i dispositivi .NET

> [!NOTE]
> [Le identità del modulo e i moduli gemelli](iot-hub-devguide-module-twins.md) sono simili alle identità del dispositivo e ai dispositivi gemelli dell'hub IoT di Azure, ma offrono una granularità superiore. Mentre l'identità del dispositivo e il dispositivo gemello dell'hub IoT di Azure consentono all'applicazione back-end di configurare un dispositivo e forniscono visibilità sulle condizioni del dispositivo, l'identità del modulo e il modulo gemello forniscono queste funzionalità per i singoli componenti di un dispositivo. Nei dispositivi con più componenti, ad esempio i dispositivi basati su sistema operativo o i dispositivi firmware, è possibile isolare la configurazione e le condizioni di ogni componente.

Al termine di questa esercitazione si ottengono due app console .NET:

* **CreateIdentities**, che crea un'identità del dispositivo, un'identità del modulo e una chiave di sicurezza associata per connettere i client dispositivo e modulo.

* **UpdateModuleTwinReportedProperties**, che invia le proprietà segnalate aggiornate del modulo gemello all'hub IoT.

> [!NOTE]
> Per informazioni sugli Azure IoT SDK che consentono di compilare le applicazioni da eseguire nei dispositivi e i back-end della soluzione, vedere [Azure IoT SDK](iot-hub-devguide-sdks.md).

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Visual Studio.

* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Ottenere la stringa di connessione per l'hub IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

L'hub IoT è stato così creato e sono ora disponibili il nome host e la stringa di connessione dell'hub necessari per completare il resto di questa esercitazione.

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]


## <a name="update-the-module-twin-using-net-device-sdk"></a>Aggiornare il modulo gemello usando l'SDK per dispositivi .NET

In questa sezione, nel dispositivo simulato viene creata un'app console .NET che aggiorna le proprietà segnalate del modulo gemello.

1. **Creare un progetto di Visual Studio:** In Visual Studio aggiungere un progetto desktop di Windows classico Visual C# alla soluzione esistente usando il modello di progetto **App console (.NET Framework)**. Verificare che la versione di .NET Framework sia 4.6.1 o successiva. Assegnare al progetto il nome **UpdateModuleTwinReportedProperties**.

    ![Creare un progetto di Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

2. **Installare la versione più recente di Azure IoT Hub SDK per dispositivi .NET:** Le funzionalità di identità del modulo e di modulo gemello sono disponibili in anteprima pubblica. Sono disponibili solo nella versione preliminare di Azure IoT SDK per dispositivi. In Visual Studio aprire Strumenti > Gestione pacchetti NuGet > Gestisci pacchetti NuGet per la soluzione. Cercare Microsoft.Azure.Devices.Client. Verificare di aver selezionato la casella di controllo Includi versione preliminare. Selezionare la versione più recente e installarla. Ora si ha accesso a tutte le funzionalità dei moduli. 

    ![Installare Azure IoT SDK per dispositivi .NET V1.16.0-preview-005](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

3. **Ottenere la stringa di connessione del modulo** -- Accedere al [portale di Azure](https://portal.azure.com/). Passare all'hub IoT e fare clic su Dispositivi IoT. Trovare myFirstDevice e aprirlo. Si vedrà che myFirstModule è stato creato correttamente. Copiare la stringa di connessione del modulo. Sarà necessaria nel prossimo passaggio.

    ![Dettagli del modulo nel portale di Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

4. **Creare un'app console UpdateModuleTwinReportedProperties**

    Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione del modulo.

    ```csharp
    private const string ModuleConnectionString = 
      "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

    Aggiungere il metodo **OnDesiredPropertyChanged** seguente alla classe **Program**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
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
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

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
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    Questo esempio di codice mostra come recuperare il modulo gemello e aggiornare le proprietà segnalate con il protocollo AMQP. L'anteprima pubblica supporta AMQP solo per le operazioni di modulo gemello.

5. Oltre al metodo **Main** riportato sopra, è possibile aggiungere il blocco di codice seguente per inviare eventi all'hub IoT dal modulo:

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Eseguire le app

A questo punto è possibile eseguire le app. In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli** e quindi selezionare **Avvia** come azione per l'app console. Quindi premere F5 per avviare l'app.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Introduzione alla gestione dei dispositivi](iot-hub-node-node-device-management-get-started.md)
* [Introduzione a IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)