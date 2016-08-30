## Inviare messaggi all'hub eventi

In questa sezione si scriverà un'app di console Windows che invia eventi all'hub eventi.

1. In Visual Studio creare un nuovo progetto di app desktop di Visual C# usando il modello di progetto **Applicazione console**. Assegnare al progetto il nome **Sender**.

	![][7]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet per la soluzione**.

3. Fare clic sulla scheda **Sfoglia** e quindi cercare `Microsoft Azure Service Bus`. Assicurarsi che il nome del progetto **Sender** sia specificato nella casella **Versione/i**. Fare clic su **Installa** e accettare le condizioni per l'utilizzo.

	![][8]  

	Visual Studio scarica e installa il [pacchetto NuGet delle librerie del bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) e aggiunge un riferimento al pacchetto.

4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs**:

	```
	using System.Threading;
	using Microsoft.ServiceBus.Messaging;
	```

5. Aggiungere i campi seguenti alla classe **Program**, sostituendo i valori dei segnaposto con il nome dell'hub eventi creato nella sezione precedente e la stringa di connessione a livello di spazio dei nomi salvata in precedenza.

	```
	static string eventHubName = "{Event Hub name}";
	static string connectionString = "{send connection string}";
	```

6. Aggiungere il metodo seguente alla classe **Program**:

	```
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
	```

	Questo metodo invia continuamente gli eventi all'hub eventi con un ritardo di 200 ms.

7. Aggiungere infine le righe seguenti al metodo **Main**:

	```
	Console.WriteLine("Press Ctrl-C to stop the sender process");
	Console.WriteLine("Press Enter to start now");
	Console.ReadLine();
	SendingRandomMessages();
	```


<!-- Images -->  
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png

<!---HONumber=AcomDC_0824_2016-->