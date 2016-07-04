## Ricevere la notifica di caricamento di un file

In questa sezione verrà scritta un'app console di Windows che riceve messaggi di notifica di caricamento dall'hub IoT.

1. Nella soluzione Visual Studio corrente creare un nuovo progetto di Windows in Visual C# usando il modello di progetto **Applicazione console**. Denominare il progetto **ReadFileUploadNotification**.

    ![Nuovo progetto in Visual Studio][2]

2. In Esplora soluzioni, fare clic con il pulsante destro del mouse sul progetto **ReadFileUploadNotification** e quindi scegliere **Gestisci pacchetti NuGet**.

    Verrà visualizzata la finestra Gestisci pacchetti NuGet.

2. Cercare `Microsoft.Azure.Devices`, fare clic su **Installa** e accettare le condizioni per l'uso.

	Verrà quindi scaricato e installato il [pacchetto NuGet Azure IoT - Service SDK] nel progetto **ReadFileUploadNotification** e verrà aggiunto un riferimento a tale pacchetto.

3. Nel file **Program.cs** aggiungere le istruzioni seguenti all’inizio del file:

        using Microsoft.Azure.Devices;

4. Aggiungere i campi seguenti alla classe **Program**. Sostituire il valore del segnaposto con la stringa di connessione all'hub IoT da [Introduzione all'hub IoT di Azure per .NET]\:

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
        
5. Aggiungere il metodo seguente alla classe **Program**:
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();

            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();

                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }

    Si noti che il modello di ricezione è identico a quello utilizzato per ricevere messaggi da cloud a dispositivo dall'app dispositivo.

6. Aggiungere infine le righe seguenti al metodo **Main**:

        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

<!-- Links -->

[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d
[pacchetto NuGet Azure IoT - Service SDK]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Introduzione all'hub IoT di Azure per .NET]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[2]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_0622_2016-->