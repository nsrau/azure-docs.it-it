## Inviare messaggi all'hub eventi

In questa sezione si scriverà un'app di console Windows che invia eventi all'hub eventi.

1. In Visual Studio creare un nuovo progetto di app desktop di Visual C# usando il modello di progetto **Applicazione console**. Assegnare al progetto il nome **Sender**.

   	![][7]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet per la soluzione**.

	Verrà visualizzata la finestra di dialogo Gestisci pacchetti NuGet.

3. Cercare `Microsoft Azure Service Bus`, fare clic su **Installa** e accettare le condizioni per l'uso.

	![][8]

	Verrà quindi scaricato e installato il <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">pacchetto NuGet Azure Service Bus library</a> e verrà aggiunto un riferimento a tale pacchetto.

4. Aggiungere l'istruzione `using` seguente all'inizio del file **Program.cs**:

		using System.Threading;
		using Microsoft.ServiceBus.Messaging;

5. Aggiungere i campi seguenti per la classe **Program**, sostituendo i valori dei segnaposto con il nome dell'hub eventi creato nella sezione precedente e la stringa di connessione con i diritti **Send**:

		static string eventHubName = "{event hub name}";
        static string connectionString = "{send connection string}";

6. Aggiungere il metodo seguente alla classe **Program**:

		static void SendingRandomMessages()
        {
            var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
            while (true)
            {
                try
                {
                    var message = Guid.NewGuid().ToString();
                    Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                    eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
                }
                catch (Exception exception)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                    Console.ResetColor();
                }

                Thread.Sleep(200);
            }
        }

	Questo metodo invia continuamente gli eventi all'hub eventi con un ritardo di 200 ms.

7. Aggiungere infine le righe seguenti al metodo **Main**:

		Console.WriteLine("Press Ctrl-C to stop the sender process");
        Console.WriteLine("Press Enter to start now");
        Console.ReadLine();
        SendingRandomMessages();


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png

<!---HONumber=July15_HO3-->