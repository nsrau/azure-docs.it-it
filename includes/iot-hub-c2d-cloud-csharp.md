## Inviare un messaggio da cloud a dispositivo dal back-end dell'app

In questa sezione si scriverà un'app console di Windows che invia messaggi da cloud a dispositivo all'app del dispositivo simulato.

1. Nella soluzione Visual Studio corrente creare un nuovo progetto di app desktop di Visual C# usando il modello di progetto **Applicazione console**. Denominare il progetto **SendCloudToDevice**.

   	![Nuovo progetto in Visual Studio][20]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet per la soluzione**.

	Verrà visualizzata la finestra **Gestisci pacchetti NuGet**.

3. Cercare `Microsoft Azure Devices`, fare clic su **Installa** e accettare le condizioni per l'uso.

	Verrà quindi scaricato e installato il [pacchetto NuGet Azure IoT - Service SDK] e verrà aggiunto un riferimento a tale pacchetto.

4. Aggiungere l'istruzione `using` seguente all'inizio del file **Program.cs**:

		using Microsoft.Azure.Devices;

5. Aggiungere i campi seguenti alla classe **Program**. Sostituire il valore del segnaposto con la stringa di connessione all'hub IoT da [Introduzione all'hub IoT di Azure per .NET]\:

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Aggiungere il metodo seguente alla classe **Program**:

		private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

	Questo metodo invia un nuovo messaggio da cloud a dispositivo al dispositivo con ID `myFirstDevice`. Cambiare questo parametro di conseguenza, nel caso in cui sia stato modificato rispetto a quello usato in [Introduzione all'hub IoT di Azure per .NET].

7. Aggiungere infine le righe seguenti al metodo **Main**:

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. Da Visual Studio fare clic con il pulsante destro sulla soluzione e selezionare **Imposta progetti di avvio...**. Selezionare **Progetti di avvio multipli**, quindi selezionare l’azione **Start** per **ProcessDeviceToCloudMessages**, **SimulatedDevice** e **SendCloudToDevice**.

9.  Premere **F5**. Devono avviarsi tutte e tre le applicazioni. Selezionare la finestra **SendCloudToDevice** e premere **Invio**. Verrà visualizzato il messaggio ricevuto dall'applicazione simulata.

    ![Ricezione di un messaggio da parte dell'app][21]

## Ricevere feedback di recapito
È possibile richiedere la conferma della ricezione (o della scadenza) dall'hub IoT per ogni messaggio da cloud a dispositivo. In questo modo il back-end cloud informa facilmente la logica di ripetizione o di compensazione. Per altre informazioni sul feedback da cloud a dispositivo, vedere [Guida per gli sviluppatori dell'hub IoT di Azure][IoT Hub Developer Guide - C2D].

In questa sezione si modificherà l'app **SendCloudToDevice** per richiedere feedback e riceverli dall'hub IoT.

1. In Visual Studio, nel progetto **SendCloudToDevice** aggiungere il seguente metodo alla classe **Programma**.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Si noti che il modello di ricezione è identico a quello utilizzato per ricevere messaggi da cloud a dispositivo dall'app dispositivo.

2. Aggiungere il metodo seguente nel metodo **Main** immediatamente dopo la riga `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`:

        ReceiveFeedbackAsync();

3. Per richiedere feedback del recapito del messaggio da cloud a dispositivo, è necessario specificare una proprietà nel metodo **SendCloudToDeviceMessageAsync**. Aggiungere la riga seguente, subito dopo la riga `var commandMessage = new Message(...);`:

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Premere **F5** per eseguire le applicazioni. Si dovrebbero avviare tutte e tre le applicazioni. Selezionare la finestra **SendCloudToDevice** e premere **Invio**. Viene visualizzata la ricezione del messaggio da parte dell'app simulata e, dopo alcuni secondi, la ricezione del messaggio di feedback da parte dell'applicazione **SendCloudToDevice**.

    ![Ricezione di un messaggio da parte dell'app][22]

> [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN [Transient Fault Handling] \(Gestione degli errori temporanei).

<!-- Links -->

[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d
[pacchetto NuGet Azure IoT - Service SDK]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Introduzione all'hub IoT di Azure per .NET]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[20]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png
[21]: ./media/iot-hub-c2d-cloud-csharp/sendc2d1.png
[22]: ./media/iot-hub-c2d-cloud-csharp/sendc2d2.png

<!---HONumber=AcomDC_0608_2016-->