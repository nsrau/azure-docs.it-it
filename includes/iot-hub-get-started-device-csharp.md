## Creare un'app di dispositivo simulato

In questa sezione si scriverà un'app console di Windows che simula un dispositivo inviando messaggi da dispositivo a cloud a un hub IoT.

1. Nella soluzione corrente di Visual Studio, fare clic su **File -> Aggiungi -> Progetto** per creare un nuovo progetto di app desktop di Visual C# utilizzando il modello di progetto **Applicazione console**. Denominare il progetto **SimulatedDevice**.

   	![][30]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet per la soluzione**.

	Verrà visualizzata la finestra Gestisci pacchetti NuGet.

3. Cercare `Microsoft Azure Devices Client`, fare clic su **Installa** e accettare le condizioni per l'uso.

	Verrà quindi scaricato e installato il [pacchetto NuGet Azure IoT - Device SDK] e verrà aggiunto un riferimento a tale pacchetto.

4. Aggiungere l'istruzione `using` seguente all'inizio del file **Program.cs**:

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. Aggiungere i campi seguenti alla classe **Program**, sostituendo i valori segnaposto con l'URI dell’hub IoT e la chiave del dispositivo recuperata nelle sezioni **Creare un hub IoT** e **Creare un’identità di dispositivo**, rispettivamente:

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub URI}";
        static string deviceKey = "{deviceKey}";

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

                Thread.Sleep(1000);
            }
        }

	Questo metodo invia un nuovo messaggio da dispositivo a cloud ogni secondo, contenente un oggetto JSON serializzato con il deviceId e un numero generato casualmente, che rappresenta un sensore di velocità del vento simulato.

7. Aggiungere infine le righe seguenti al metodo **Main**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Per impostazione predefinita, il metodo **Crea** un **DeviceClient** che usa il protocollo AMQP per comunicare con IoT Hub. Per usare il protocollo HTTPS, usare l'override del metodo **Crea** per specificare il protocollo. Se si sceglie di utilizzare il protocollo HTTPS, è inoltre necessario aggiungere il pacchetto NuGet **Microsoft.AspNet.WebApi.Client** al progetto per includere lo spazio dei nomi **System.Net.Http.Formatting**.


> [AZURE.NOTE]Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione si consiglia di implementare criteri di nuovi tentativi (ad esempio backoff esponenziale), come indicato nell'articolo di MSDN [Gestione degli errori temporanei].

<!-- Links -->

[pacchetto NuGet Azure IoT - Device SDK]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[Gestione degli errori temporanei]: https://msdn.microsoft.com/it-IT/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=Nov15_HO2-->