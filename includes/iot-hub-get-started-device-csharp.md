## Creare un'app di dispositivo simulato

In questa sezione si creerà un'app console di Windows che simula un dispositivo che invia messaggi da dispositivo a cloud a un hub IoT.

1. In Visual Studio aggiungere un nuovo progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console**. Assicurarsi che la versione di .NET Framework sia 4.5.1 o successiva. Denominare il progetto **SimulatedDevice**.

   	![][30]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **SimulatedDevice** e quindi scegliere **Gestisci pacchetti NuGet**.

3. Nella finestra **Gestione pacchetti NuGet** scegliere **Sfoglia**, cercare **Microsoft.Azure.Devices.Client**, fare clic su **Installa** per installare il pacchetto **Microsoft.Azure.Devices.Client** e accettare le condizioni per l'utilizzo.

	Viene scaricato e installato un riferimento al pacchetto NuGet [Device SDK per Azure IoT][lnk-device-nuget] e viene aggiunto un riferimento a tale pacchetto e alle relative dipendenze.

4. Aggiungere l'istruzione `using` seguente all'inizio del file **Program.cs**:

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;


5. Aggiungere i campi seguenti alla classe **Program**, sostituendo i valori segnaposto con il nome host dell'hub IoT recuperato nelle sezioni *Creare un hub IoT* e *Creare un'identità di dispositivo*:

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

	Questo metodo invia un nuovo messaggio da dispositivo a cloud ogni secondo. Il messaggio contiene un oggetto serializzato JSON con deviceId e un numero generato casualmente per simulare un sensore di velocità del vento.

7. Aggiungere infine le righe seguenti al metodo **Main**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Per impostazione predefinita, il metodo **Create** crea un'istanza di **DeviceClient** che usa il protocollo AMQP per comunicare con l'hub IoT. Per usare il protocollo HTTPS, usare l'override del metodo **Create** che consente di specificare il protocollo. Se si sceglie di usare il protocollo HTTPS, è necessario aggiungere anche il pacchetto NuGet **Microsoft.AspNet.WebApi.Client** al progetto per includere lo spazio dei nomi **System.Net.Http.Formatting**.

Questa esercitazione illustra i passaggi per creare un client per dispositivi dell'hub IoT. In alternativa, è possibile usare l'estensione di Visual Studio [Servizio connesso per Azure IoT Hub][lnk-connected-service] per aggiungere il codice necessario all'applicazione client per dispositivi.


> [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN relativo alla [gestione degli errori temporanei][lnk-transient-faults].

<!-- Links -->

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_0413_2016-->