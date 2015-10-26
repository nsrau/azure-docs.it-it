## Ricezione di messaggi dal dispositivo simulato

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

    Il metodo `ReceiveAsync` restituisce in modo asincrono il messaggio ricevuto nel momento in cui viene ricevuto dal dispositivo. Restituisce *null* dopo un periodo di timeout specificabile (in questo caso viene utilizzato il valore predefinito di 1 minuto). Quando questo si verifica, si desidera che il codice continui l'attesa di nuovi messaggi. Questo è il motivo della riga `if (receivedMessage == null) continue`.

    La chiamata a `CompleteAsync()` notifica all’hub IoT che il messaggio è stato elaborato correttamente e può essere rimosso correttamente dalla coda del dispositivo. Se è avvenuto qualcosa che ha impedito all'app dispositivo di completare l'elaborazione del messaggio, l’hub IoT lo recapiterà nuovamente; è quindi importante che la logica di elaborazione del messaggio nell'app dispositivo sia *idempotente*, ovvero la ricezione dello stesso messaggio più volte dovrebbe produrre lo stesso risultato. Un'applicazione può inoltre `Abandon` temporaneamente un messaggio, per cui l’hub IoT conseverà il messaggio nella coda per un utilizzo futuro; o `Reject` un messaggio, che consente di rimuovere definitivamente il messaggio dalla coda. Fare riferimento alla [Guida per sviluppatori di Hub IoT][IoT Hub Developer Guide - C2D] per ulteriori informazioni sul ciclo di vita del messaggio da cloud a dispositivo.

> [AZURE.NOTE]Quando si utilizza HTTP/1 anziché AMQP come trasporto, `ReceiveAsync` verrà restituito immediatamente. Il modello supportato per i messaggi da dispositivo a cloud con HTTP/1 è dispositivi collegati occasionalmente che controllano i messaggi raramente (cioè meno di ogni 25 minuti). La generazione di ulteriori ricezioni HTTP/1 comporterà la limitazione delle richieste da parte dell’hub IoT. Fare riferimento alla [Guida per sviluppatori di Hub IoT][IoT Hub Developer Guide - C2D] per ulteriori informazioni sulle differenze tra supporto AMQP e HTTP/1 e sulla limitazione delle richieste da parte dell’hub IoT.

2. Aggiungere il metodo seguente al metodo **Main** immediatamente prima della riga `Console.ReadLine()`:

        ReceiveC2dAsync();

> [AZURE.NOTE]Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione si consiglia di implementare criteri di nuovi tentativi (ad esempio backoff esponenziale), come indicato nell'articolo di MSDN [Gestione degli errori temporanei].

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->

<!---HONumber=Oct15_HO3-->