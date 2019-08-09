---
title: Introduzione all'identità del modulo e ai moduli gemelli dell'hub IoT di Azure (.NET) | Microsoft Docs
description: Come creare l'identità del modulo e aggiornare il modulo gemello usando gli SDK per IoT per .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.openlocfilehash: e5d2e485283f71b27bb5e93330a54e1b987044c4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883829"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Introduzione all'identità del modulo dell'hub Internet e al modulo gemello (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Le identità del modulo e i moduli gemelli](iot-hub-devguide-module-twins.md) sono simili alle identità del dispositivo e ai dispositivi gemelli dell'hub IoT di Azure, ma offrono una granularità superiore. Mentre l'identità del dispositivo e il dispositivo gemello dell'hub Azure è possibile abilitare l'applicazione back-end per configurare un dispositivo e fornire visibilità sulle condizioni del dispositivo, un'identità del modulo e un modulo gemello forniscono queste funzionalità per i singoli componenti di un dispositivo. Nei dispositivi compatibili con più componenti, ad esempio dispositivi basati sul sistema operativo o dispositivi firmware, le identità dei moduli e i moduli gemelli consentono la configurazione e le condizioni isolate per ogni componente.

Al termine di questa esercitazione si ottengono due app console .NET:

* **CreateIdentities**. Questa app crea un'identità del dispositivo, un'identità del modulo e una chiave di sicurezza associata per connettere i client del dispositivo e del modulo.

* **UpdateModuleTwinReportedProperties**. Questa app invia le proprietà segnalate dal modulo gemello aggiornato all'hub Internet.

> [!NOTE]
> Per informazioni sugli Azure IoT SDK che consentono di compilare le applicazioni da eseguire nei dispositivi e i back-end della soluzione, vedere [Azure IoT SDK](iot-hub-devguide-sdks.md).

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Visual Studio.

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="create-a-hub"></a>Creare un hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aggiornare il modulo gemello usando l'SDK per dispositivi .NET

In questa sezione, nel dispositivo simulato viene creata un'app console .NET che aggiorna le proprietà segnalate del modulo gemello.

Prima di iniziare, ottenere la stringa di connessione del modulo. Accedere al [portale di Azure](https://portal.azure.com/). Passare all'hub e selezionare **dispositivi**. Trovare **myFirstDevice**. Selezionare **myFirstDevice** per aprirlo e quindi selezionare **myFirstModule** per aprirlo. In **Dettagli identità del modulo**copiare la **stringa di connessione (chiave primaria)** quando necessario nella procedura seguente.

   ![Dettagli del modulo nel portale di Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. In Visual Studio aggiungere un nuovo progetto alla soluzione selezionando **file** > **nuovo** > **progetto**. In crea un nuovo progetto selezionare **app console (.NET Framework)** e fare clic su **Avanti**.

1. Assegnare al progetto il nome *UpdateModuleTwinReportedProperties*. Per **soluzione**selezionare **Aggiungi a soluzione**. Verificare che la versione di .NET Framework sia 4.6.1 o successiva.

    ![Creare un progetto di Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Selezionare **Crea** per creare il progetto.

1. In Visual Studio aprire **strumenti** >  > **Gestione pacchetti NuGet** **Gestisci pacchetti NuGet per la soluzione**. Selezionare la scheda **Sfoglia**.

1. Cercare e selezionare **Microsoft. Azure. Devices. client**, quindi selezionare **Install (installa**).

    ![Installare la versione corrente di .NET Service SDK dell'hub Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione del modulo.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. Aggiungere il metodo **OnDesiredPropertyChanged** seguente alla classe **Program**:

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

1. Aggiungere le righe seguenti al metodo **Main** :

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

1. Facoltativamente, è possibile aggiungere queste istruzioni al metodo **Main** per inviare un evento all'hub delle cose dal modulo. Posizionare queste righe sotto il `try catch` blocco.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Eseguire le app

È ora possibile eseguire le app.

1. In **Esplora soluzioni**in Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Imposta progetti di avvio**.

1. In **Proprietà comuni**selezionare **progetto di avvio.**

1. Selezionare **progetti di avvio multipli**, quindi selezionare **Avvia** come azione per le app e **OK** per accettare le modifiche.

1. Premere **F5** per avviare le app.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Introduzione alla gestione dei dispositivi](iot-hub-node-node-device-management-get-started.md)

* [Introduzione a IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
