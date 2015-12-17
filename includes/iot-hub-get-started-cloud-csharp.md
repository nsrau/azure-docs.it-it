## Creare un'identità del dispositivo

In questa sezione si scriverà un'applicazione console di Windows che consente di creare una nuova identità del dispositivo nell'hub IoT. Fare riferimento alla sezione **Registro identità del dispositivo** della [Guida per sviluppatori di hub IoT][IoT Hub Developer Guide - Identity Registry] per ulteriori informazioni. Dopo l'esecuzione di questa applicazione console, sarà necessario un ID e una chiave da utilizzare come identità del dispositivo per inviare messaggi dal dispositivo al cloud all’hub IoT.

1. In Visual Studio creare un nuovo progetto di app desktop di Visual C# usando il modello di progetto **Applicazione console**. Denominare il progetto **CreateDeviceIdentity**.

	![][10]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet per la soluzione**.

	Verrà visualizzata la finestra **Gestione pacchetti NuGet**.

3. Assicurarsi che l'opzione **Includi versione preliminare** sia selezionata. Cercare quindi `Microsoft Azure Devices`, fare clic su **Installa** e accettare le condizioni per l'uso.

	![][11]

4. Verrà quindi scaricato e installato il pacchetto NuGet [Microsoft Azure Devices SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e verrà aggiunto un riferimento a tale pacchetto.

4. Aggiungere l'istruzione `using` seguente all'inizio del file **Program.cs**:

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Aggiungere i campi seguenti alla classe **Program**, sostituendo i valori dei segnaposto con il nome dell'hub IoT creato nella sezione precedente e la relativa stringa di connessione:

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. Aggiungere il metodo seguente alla classe **Program**:

		private async static Task AddDeviceAsync()
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

	Questo metodo creerà una nuova identità del dispositivo con ID **myFirstDevice** (nel caso esista già un'identità con lo stesso ID, la recupererà semplicemente). Quindi, l'app visualizzerà la chiave primaria per tale identità. Questa chiave verrà utilizzata dal dispositivo simulato per connettersi all'hub IoT.

7. Aggiungere infine le righe seguenti al metodo **Main**:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Eseguire l’applicazione e annotare la chiave del dispositivo.

    ![][12]

> [AZURE.NOTE] È importante notare che il registro dell’identità dell’hub IoT viene utilizzato solo per memorizzare le identità dei dispositivi allo scopo di proteggere l'accesso, ovvero archiviare le credenziali di sicurezza e abilitare o disabilitare l'accesso del singolo dispositivo. I metadati dell’applicazione del dispositivo devono essere archiviati in un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per sviluppatori di hub IoT][IoT Hub Developer Guide - Identity Registry].

## Ricezione di messaggi da dispositivo a cloud

In questa sezione si creerà un'app console di Windows che legge i messaggi da dispositivo a cloud dall’hub IoT. L'hub IOT espone un endpoint compatibile con [Hub eventi][Event Hubs Overview] per leggere i messaggi da dispositivo a cloud. Per semplicità, questa esercitazione consente di creare un lettore semplificato che non è appropriato per una distribuzione a produttività elevata. Sono disponibili ulteriori informazioni su come elaborare i messaggi da dispositivo a cloud dell'hub IoT nell'esercitazione [Elaborare i messaggi da dispositivo a cloud](iot-hub-csharp-csharp-process-d2c.md) . Per altre informazioni su come elaborare i messaggi da Hub eventi, è possibile fare riferimento all'esercitazione [Introduzione all'Hub eventi].

1. Nella soluzione corrente di Visual Studio fare clic su **File -> Aggiungi -> Progetto** per creare un nuovo progetto di app desktop di Visual C# usando il modello di progetto **Applicazione console**. Denominare il progetto **ReadDeviceToCloudMessages**.

    ![][10]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet**.

    Viene visualizzata la finestra di dialogo **Gestione pacchetti NuGet**.

3. Cercare `WindowsAzure.ServiceBus`, fare clic su **Installa** e accettare le condizioni per l'uso.

    Verrà quindi scaricato e installato il [bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) e verrà aggiunto un riferimento ad esso.

4. Aggiungere l'istruzione `using` seguente all'inizio del file **Program.cs**:

        using Microsoft.ServiceBus.Messaging;

5. Aggiungere i campi seguenti alla classe **Program**, sostituendo i valori dei segnaposto con il nome dell'hub IoT creato nella sezione precedente e la relativa stringa di connessione:

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Aggiungere il metodo seguente alla classe **Program**:

        private async static Task ReceiveMessagesFromDeviceAsync(string partition)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.Now);
            while (true)
            {
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
            }
        }

    Questo metodo utilizza un client EventHub per ricevere da tutte le partizioni di ricezione da dispositivo a cloud dell'hub IoT. Si noti che, durante la creazione di un EventHubReceiver, viene passato un parametro `DateTime.Now`. Consente di creare un destinatario che riceverà solo i messaggi inviati dopo l'avvio.

7. Aggiungere infine le righe seguenti al metodo **Main**:

        Console.WriteLine("Receive messages\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        foreach (string partition in d2cPartitions)
        {
           ReceiveMessagesFromDeviceAsync(partition);
        }  
        Console.ReadLine();


<!-- Links -->

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Introduzione all'Hub eventi]: event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Event Hubs Overview]: event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Azure Storage account]: storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Azure preview portal]: https://portal.azure.com/


<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!----HONumber=Nov15_HO3-->