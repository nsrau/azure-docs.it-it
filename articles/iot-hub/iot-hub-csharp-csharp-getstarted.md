<properties
	pageTitle="Introduzione all'hub IoT di Azure per C# | Microsoft Azure"
	description="Esercitazione introduttiva all'hub IoT di Azure con C#. Usare l'hub IoT di Azure e C# con gli SDK IoT di Microsoft Azure per implementare una soluzione Internet delle cose."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/16/2016"
     ms.author="dobett"/>

# Introduzione all'hub IoT di Azure per .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al termine di questa esercitazione si avranno tre applicazioni console Windows:

* **CreateDeviceIdentity**, che crea un'identità del dispositivo e la chiave di sicurezza associata per connettere il dispositivo simulato.
* **ReadDeviceToCloudMessages**, che consente di visualizzare i dati di telemetria inviati dal dispositivo simulato.
* **SimulatedDevice**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e invia un messaggio di telemetria ogni secondo usando il protocollo AMQPS.

> [AZURE.NOTE] Per informazioni sui diversi SDK che consentono di compilare le applicazioni da eseguire nei dispositivi e nel back-end della soluzione, vedere [SDK hub IoT][lnk-hub-sdks].

Per completare l'esercitazione sono necessari gli elementi seguenti:

+ Microsoft Visual Studio 2015

+ Un account Azure attivo. Se non si ha un account è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere [Crea subito il tuo account Azure gratuito][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

L'hub IoT è stato così creato e sono ora disponibili il nome host e la stringa di connessione necessari per completare il resto di questa esercitazione.

## Creare un'identità del dispositivo

In questa sezione si scriverà un'app console di Windows che consente di creare una nuova identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non sia presente una voce nel registro delle identità del dispositivo. Vedere la sezione "Registro delle identità dei dispositivi" della [Guida per gli sviluppatori dell'hub IoT di Azure][lnk-devguide-identity] per altre informazioni. Quando si esegue questa app console vengono generati un ID dispositivo univoco e una chiave con cui il dispositivo può identificarsi quando invia messaggi da dispositivo a cloud all'hub IoT.

1. In Visual Studio aggiungere un nuovo progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console**. Verificare che la versione di .NET Framework sia 4.5.1 o successiva. Denominare il progetto **CreateDeviceIdentity**.

	![Nuovo progetto desktop di Windows classico in Visual C#][10]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **CreateDeviceIdentity** e quindi scegliere **Gestisci pacchetti NuGet**.

3. Nella finestra **Gestione pacchetti NuGet** selezionare **Esplora**, cercare **microsoft.azure.devices**, selezionare **Installa** per installare il pacchetto **Microsoft.Azure.Devices** e accettare le condizioni per l'utilizzo. Verrà scaricato e installato il pacchetto NuGet [Microsoft Azure IoT Service SDK][lnk-nuget-service-sdk] e verrà aggiunto un riferimento a tale pacchetto e alle relative dipendenze.

	![Finestra Gestione pacchetti NuGet][11]

4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs**:

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Aggiungere i campi seguenti alla classe **Program**. Sostituire il valore del segnaposto con la stringa di connessione dell'hub IoT creato nella sezione precedente.

		static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Aggiungere il metodo seguente alla classe **Program**:

		private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

	Questo metodo crea una nuova identità del dispositivo con ID **myFirstDevice**. Se questo ID dispositivo è già disponibile nel registro, il codice recupera semplicemente le informazioni sul dispositivo esistenti. L'app visualizzerà quindi la chiave primaria per l'identità. Questa chiave verrà usata dal dispositivo simulato per connettersi all'hub IoT.

7. Aggiungere infine le righe seguenti al metodo **Main**:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Eseguire l'applicazione e prendere nota della chiave del dispositivo.

    ![Chiave del dispositivo generata dall'applicazione][12]

> [AZURE.NOTE] Il registro delle identità dell'hub IoT consente di archiviare solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza e un flag di attivazione/disattivazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT di Azure][lnk-devguide-identity].

## Ricezione di messaggi da dispositivo a cloud

In questa sezione si creerà un'app console di Windows che legge i messaggi da dispositivo a cloud dall'hub IoT. L'hub IoT espone un endpoint compatibile con [Hub eventi di Azure][lnk-event-hubs-overview] per abilitare la lettura dei messaggi da dispositivo a cloud. Per semplicità, questa esercitazione crea un lettore di base non adatto per una distribuzione con velocità effettiva elevata. Per informazioni sull'elaborazione di messaggi da dispositivo a cloud su vasta scala, vedere l'[Esercitazione: elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-process-d2c-tutorial]. Per altre informazioni su come elaborare i messaggi da Hub eventi, vedere l'esercitazione [Introduzione all'Hub eventi][lnk-eventhubs-tutorial]. Questa esercitazione è applicabile agli endpoint compatibili con Hub eventi di hub IoT.

> [AZURE.NOTE] L'endpoint compatibile con Hub eventi per la lettura di messaggi da dispositivo a cloud usa sempre il protocollo AMQPS.

1. In Visual Studio aggiungere un nuovo progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console**. Verificare che la versione di .NET Framework sia 4.5.1 o successiva. Denominare il progetto **ReadDeviceToCloudMessages**.

    ![Nuovo progetto desktop di Windows classico in Visual C#][10]

2. In Esplora soluzioni, fare clic con il pulsante destro del mouse sul progetto **ReadDeviceToCloudMessages** e quindi scegliere **Gestisci pacchetti NuGet**.

3. Nella finestra **Gestione pacchetti NuGet** cercare **WindowsAzure.ServiceBus**, fare clic su **Installa** e accettare le condizioni per l'utilizzo. Verrà quindi scaricato e installato il [bus di servizio di Azure][lnk-servicebus-nuget] con tutte le relative dipendenze e verrà aggiunto un riferimento ad esso. Questo pacchetto consente all'applicazione di connettersi all'endpoint compatibile con l'Hub eventi dell'hub IoT.

4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs**:

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Aggiungere i campi seguenti alla classe **Program**. Sostituire il valore del segnaposto con la stringa di connessione dell'hub IoT creato nella sezione "Creare un hub IoT".

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Aggiungere il metodo seguente alla classe **Program**:

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    Questo metodo usa un'istanza **EventHubReceiver** per ricevere i messaggi da tutte le partizioni di ricezione da dispositivo a cloud dell'hub IoT. Si noti come viene passato un parametro `DateTime.Now` quando si crea l'oggetto **EventHubReceiver** in modo che riceva solo i messaggi inviati dopo l'avvio. Ciò è utile in un ambiente di test perché consente di visualizzare il set di messaggi corrente, ma in un ambiente di produzione il codice deve verificare di avere elaborato tutti i messaggi. Per altre informazioni, vedere l'[Esercitazione: elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-process-d2c-tutorial].

7. Aggiungere infine le righe seguenti al metodo **Main**:

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## Creare un'app di dispositivo simulato

In questa sezione si creerà un'app console di Windows che simula un dispositivo che invia messaggi da dispositivo a cloud a un hub IoT.

1. In Visual Studio aggiungere un nuovo progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console**. Verificare che la versione di .NET Framework sia 4.5.1 o successiva. Denominare il progetto **SimulatedDevice**.

    ![Nuovo progetto desktop di Windows classico in Visual C#][10]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **SimulatedDevice** e quindi scegliere **Gestisci pacchetti NuGet**.

3. Nella finestra **Gestione pacchetti NuGet** selezionare **Esplora**, cercare **Microsoft.Azure.Devices.Client**, selezionare **Installa** per installare il pacchetto **Microsoft.Azure.Devices.Client** e accettare le condizioni per l'utilizzo. Verrà scaricato e installato il pacchetto NuGet [Azure IoT - Device SDK][lnk-device-nuget] e verrà aggiunto un riferimento a tale pacchetto e alle relative dipendenze.

4. Aggiungere l'istruzione `using` seguente all'inizio del file **Program.cs**:

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. Aggiungere i campi seguenti alla classe **Program**. Sostituire i valori segnaposto con il nome host dell'hub IoT recuperato nella sezione "Creare un hub IoT" e la chiave del dispositivo recuperata dalla sezione "Creare un'identità del dispositivo".

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Aggiungere il metodo seguente alla classe **Program**:

		private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

	Questo metodo invia un nuovo messaggio da dispositivo a cloud ogni secondo. Il messaggio contiene un oggetto serializzato JSON con ID dispositivo e un numero generato casualmente per simulare un sensore di velocità del vento.

7. Aggiungere infine le righe seguenti al metodo **Main**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Per impostazione predefinita, il metodo **Create** crea un'istanza di **DeviceClient** che usa il protocollo AMQP per comunicare con l'hub IoT. Per usare il protocollo HTTPS, usare l'override del metodo **Create** che consente di specificare il protocollo. Se si usa il protocollo HTTPS è necessario aggiungere anche il pacchetto NuGet **Microsoft.AspNet.WebApi.Client** al progetto per includere lo spazio dei nomi **System.Net.Http.Formatting**.

Questa esercitazione illustra i passaggi per creare un client per dispositivi dell'hub IoT. È anche possibile usare l'estensione di Visual Studio [Connected Service for Azure IoT Hub][lnk-connected-service] per aggiungere il codice necessario all'applicazione client per dispositivi.


> [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN relativo alla [gestione degli errori temporanei][lnk-transient-faults].

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli** e quindi selezionare **Avvio** come azione per entrambi i progetti **ReadDeviceToCloudMessages** e **SimulatedDevice**.

   	![Proprietà del progetto di avvio][41]  

2.	Premere **F5** avviare entrambe le app in esecuzione. L'output della console dall'app **SimulatedDevice** visualizza i messaggi inviati dal dispositivo simulato all'hub IoT. L'output della console dall'app **ReadDeviceToCloudMessages** visualizza i messaggi ricevuti dall'hub IoT.

   	![Output della console dalle app][42]

3. Il riquadro **Utilizzo** nel [portale di Azure][lnk-portal] mostra il numero di messaggi inviati all'hub:

    ![Riquadro Utilizzo del portale di Azure][43]


## Passaggi successivi

In questa esercitazione è stato configurato un nuovo hub IoT nel portale ed è stata quindi creata un'identità del dispositivo nel registro delle identità dell'hub. Questa identità del dispositivo è stata usata per consentire all'app del dispositivo simulato di inviare all'hub messaggi da dispositivo a cloud. È stata anche creata un'app che visualizza i messaggi ricevuti dall'hub.

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

- [Connessione del dispositivo][lnk-connect-device]
- [Introduzione alla gestione dei dispositivi][lnk-device-management]
- [Introduzione a Gateway SDK][lnk-gateway-SDK]

Per informazioni sull'estensione della soluzione IoT e l'elaborazione di messaggi da dispositivo a cloud su vasta scala, vedere [Esercitazione: elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-process-d2c-tutorial].

<!-- Images. --> 
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_0831_2016-->