## Caricare un file da un dispositivo simulato

In questa sezione verrà modificata l'applicazione del dispositivo simulato creata in [Inviare messaggi da cloud a dispositivo con hub IoT] per ricevere i messaggi da cloud a dispositivo dall'hub IoT.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **SimulatedDevice** e quindi scegliere **Gestisci pacchetti NuGet...**. 

    Verrà visualizzata la finestra Gestisci pacchetti NuGet.

2. Cercare `WindowsAzure.Storage`, fare clic su **Installa** e accettare le condizioni per l'uso.

    Verrà quindi scaricato e installato l’[SDK di Archiviazione di Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) e verrà aggiunto un riferimento ad esso.

3. Nel file **Program.cs** aggiungere le istruzioni seguenti all’inizio del file:

        using System.IO;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. Nella classe **Program**, modificare il metodo **ReceiveC2dAsync** nel modo seguente:
         
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                if (receivedMessage.Properties.ContainsKey("command") && receivedMessage.Properties["command"] == "FileUpload")
                {
                    UploadFileToBlobAsync(receivedMessage);
                    continue;
                }

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
            }
        }

    In questo modo **ReceiveC2dAsync** distingue i messaggi con la proprietà `command` impostata su `FileUpload`, che verrà gestita dal metodo **UploadFileToBlobAsync**.

    Aggiungere il metodo seguente per gestire i comandi di caricamento file.
   
        private static async Task UploadFileToBlobAsync(Message fileUploadCommand)
        {
            var fileUri = fileUploadCommand.Properties["fileUri"];
            var blob = new CloudBlockBlob(new Uri(fileUri));

            byte[] data = new byte[10 * 1024 * 1024];
            Random rng = new Random();
            rng.NextBytes(data);

            MemoryStream msWrite = new MemoryStream(data);
            msWrite.Position = 0;
            using (msWrite)
            {
                await blob.UploadFromStreamAsync(msWrite);
            }
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Uploaded file to: {0}", fileUri);
            Console.ResetColor();

            await deviceClient.CompleteAsync(fileUploadCommand);
        }

    Questo metodo utilizza l’SDK di Archiviazione di Azure per caricare un BLOB di 10 Mb generato casualmente nell'URI specificato. Fare riferimento a [Archiviazione di Azure - Come utilizzare i BLOB] per ulteriori informazioni su come caricare BLOB.

> [AZURE.NOTE]Si noti come questa implementazione del dispositivo simulato completa il messaggio da cloud a dispositivo solo dopo che il BLOB è stato caricato. Questo approccio semplifica l'elaborazione dei file caricati nel back-end, in quanto il riconoscimento del recapito rappresenta la disponibilità del file caricato per l'elaborazione. Come spiegato nella [Guida per sviluppatori di hub IoT][IoT Hub Developer Guide - C2D], tuttavia, un messaggio che non viene completato prima del *timeout della visibilità* (in genere 1 minuto) viene reinserito nella coda del dispositivo e il metodo **ReceiveAsync()** lo riceverà nuovamente. Per gli scenari in cui il caricamento del file può richiedere più tempo, potrebbe essere preferibile per il dispositivo simulato mantenere un archivio durevole dei processi di caricamento correnti. In questo modo il dispositivo simulato completa il messaggio da cloud a dispositivo prima del completamento del caricamento del file, quindi invia un messaggio da dispositivo a cloud notificando al back-end il completamento.

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Archiviazione di Azure - Come utilizzare i BLOB]: https://azure.microsoft.com/it-IT/documentation/articles/storage-dotnet-how-to-use-blobs/#upload-a-blob-into-a-container

<!-- Images -->

<!---HONumber=Oct15_HO3-->