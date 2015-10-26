## Inviare messaggi interattivi dal dispositivo simulato

In questa sezione verrà modificata l'applicazione dispositivo simulato per inviare messaggi interattivi da dispositivo a cloud all'hub IoT.

1. In Visual Studio, nel progetto **SimulatedDevice** aggiungere il seguente metodo alla classe **Programma**.
   
        private static async void SendDeviceToCloudInteractiveMessagesAsync()
        {
            while (true)
            {
                var interactiveMessageString = "Alert message!";
                var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
                interactiveMessage.Properties["messageType"] = "interactive";
                interactiveMessage.MessageId = Guid.NewGuid().ToString();

                await deviceClient.SendEventAsync(interactiveMessage);
                Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

                Thread.Sleep(10000);
            }
        }

    Questo metodo è molto simile al metodo `SendDeviceToCloudMessagesAsync()` creato in [Introduzione all’hub IoT], le uniche differenze sono che la proprietà di sistema `MessageId` e una proprietà utente denominata `messageType` sono ora impostate. La proprietà `MessageId` è impostata su un id univoco globale (guid) che verrà utilizzato per deduplicare le ricezioni dei messaggi. La proprietà `messageType` viene utilizzata per distinguere i messaggi interattivi dai messaggi del punto dati. Queste informazioni vengono passate nelle proprietà del messaggio, invece che nel corpo del messaggio, in modo che il processore di eventi nel back-end non debba deserializzare il messaggio intero solo per eseguire il routing.

> [AZURE.NOTE]È importante che `MessageId`, utilizzata per deduplicare messaggi interattivi, venga creata nel dispositivo, poiché comunicazioni di rete intermittenti (o altri errori) possono comportare trasmissioni multiple dello stesso messaggio dal dispositivo. È inoltre possibile utilizzare un id di messaggio semantico (ad esempio, un hash dei campi dati del messaggio rilevante), invece di un guid.

2. Aggiungere il metodo seguente al metodo **Main** immediatamente prima della riga `Console.ReadLine()`:

        SendDeviceToCloudInteractiveMessagesAsync();

> [AZURE.NOTE]Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione si consiglia di implementare criteri di nuovi tentativi (ad esempio backoff esponenziale), come indicato nell'articolo di MSDN [Gestione degli errori temporanei].

<!-- Links -->
[Introduzione all’hub IoT]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

<!---HONumber=Oct15_HO3-->