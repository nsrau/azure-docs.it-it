## Ricezione di messaggi sul dispositivo simulato

In questa sezione verrà modificata l'applicazione del dispositivo simulato creata in [Introduzione all’hub IoT] per ricevere i messaggi da cloud a dispositivo dall'hub IoT.

1. In Visual Studio, nel progetto **SimulatedDevice** aggiungere il seguente metodo alla classe **Programma**.

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    Il metodo `ReceiveAsync` restituisce in modo asincrono il messaggio ricevuto nel momento in cui viene ricevuto dal dispositivo. Restituisce *null* dopo un periodo di timeout specificabile (in questo caso viene usato il valore predefinito di un minuto). Quando questo si verifica, il codice deve continuare l'attesa di nuovi messaggi. Questo è il motivo della riga `if (receivedMessage == null) continue`.

    La chiamata a `CompleteAsync()` notifica all'hub IoT che il messaggio è stato elaborato correttamente. Il messaggio può essere rimosso dalla coda del dispositivo in modo sicuro. Se si è verificato un evento che ha impedito all'app per dispositivo di completare l'elaborazione del messaggio, l'hub IoT lo recapita nuovamente. È quindi importante che la logica di elaborazione del messaggio nell'app per dispositivo sia *idempotente*, in modo che la ricezione dello stesso messaggio più volte produca lo stesso risultato. Un'applicazione può anche abbandonare temporaneamente un messaggio e, di conseguenza, l'hub IoT mantiene il messaggio nella coda per un uso futuro. In alternativa, l'applicazione può rifiutare un messaggio, rimuovendolo così definitivamente dalla coda. Per altre informazioni sul ciclo di vita del messaggio da cloud a dispositivo, vedere [Guida per gli sviluppatori dell'hub IoT di Azure][IoT Hub Developer Guide - C2D].

> [AZURE.NOTE] Quando si usa HTTP/1 anziché AMQP come trasporto, il metodo `ReceiveAsync` verrà restituito immediatamente. Il modello supportato per i messaggi da dispositivo a cloud con HTTP/1 è dispositivi collegati occasionalmente che controllano i messaggi raramente (a intervalli inferiori 25 minuti). La generazione di altre ricezioni HTTP/1 comporta la limitazione delle richieste da parte dell'hub IoT. Fare riferimento alla [Guida per gli sviluppatori dell'hub IoT di Azure][IoT Hub Developer Guide - C2D] per maggiori informazioni sulle differenze tra supporto AMQP e HTTP/1 e sulla limitazione delle richieste da parte dell’hub IoT.

2. Aggiungere il metodo seguente al metodo **Main** immediatamente prima della riga `Console.ReadLine()`:

        ReceiveC2dAsync();

> [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN [Transient Fault Handling] \(Gestione degli errori temporanei).

<!-- Links -->
[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d

<!-- Images -->

<!---HONumber=AcomDC_0608_2016-->