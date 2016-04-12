## Inviare messaggi interattivi dal dispositivo simulato

In questa sezione verrà modificata l'applicazione del dispositivo simulato creata nell'esercitazione [Introduzione all'hub IoT] per inviare messaggi interattivi da dispositivo a cloud all'hub IoT.

1. In Visual Studio, nel progetto **SimulatedDevice** aggiungere il seguente metodo alla classe **Programma**.

    ```
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
    ```

    Questo metodo è molto simile al metodo **SendDeviceToCloudMessagesAsync** nel progetto **SimulatedDevice**. Le uniche differenze sono l'impostazione della proprietà di sistema **MessageId** e una proprietà utente denominata **messageType**. Il codice assegna un identificatore univoco globale (GUID) alla proprietà **MessageId**, che il bus di servizio può usare per deduplicare i messaggi ricevuti. L'esempio usa la proprietà **messageType** per distinguere i messaggi interattivi dai messaggi del punto dati. L'applicazione trasferisce queste informazioni nelle proprietà del messaggio anziché nel corpo del messaggio, in modo che il processore di eventi non abbia bisogno di deserializzare il messaggio per eseguirne il routing.

    > [AZURE.NOTE] È importante creare la proprietà **MessageId** usata per deduplicare i messaggi interattivi nel codice del dispositivo, poiché comunicazioni di rete intermittenti (o altri errori) possono comportare trasmissioni multiple dello stesso messaggio dal dispositivo. È anche possibile usare un ID semantico del messaggio, ad esempio un hash dei campi dati del messaggio pertinente, al posto di un GUID.

2. Aggiungere il metodo seguente al metodo **Main** immediatamente prima della riga `Console.ReadLine()`:

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] Per semplicità, questa esercitazione non implementa alcun criterio di ripetizione. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN relativo alla [gestione degli errori temporanei].

<!-- Links -->
[gestione degli errori temporanei]: https://msdn.microsoft.com/library/hh675232.aspx
[Introduzione all'hub IoT]: iot-hub-csharp-csharp-getstarted.md

<!---HONumber=AcomDC_0309_2016-->