## Provisioning di un account di archiviazione di Azure
Poiché il dispositivo simulato caricherà un file in un BLOB di archiviazione di Azure, è necessario disporre di un account di archiviazione Azure. È possibile utilizzarne uno esistente o seguire le istruzioni in [Informazioni sull'archiviazione di Azure] per crearne uno nuovo. Prendere nota della stringa di connessione dell’account di archiviazione.

## Inviare un URI del BLOB di Azure al dispositivo simulato

In questa sezione verrà modificata l’app console **SendCloudtoDevice** creata in [Inviare messaggi da cloud a dispositivo con hub IoT] per includere un URI BLOB di Azure con una firma di accesso condiviso. In questo modo il back-end cloud concede l'accesso in scrittura al blob solo al destinatario del messaggio da cloud a dispositivo.

1. In Visual Studio fare doppio clic sul progetto **SendCloudtoDevice**, quindi fare clic su **Gestisci pacchetti NuGet...**. 

    Verrà visualizzata la finestra Gestisci pacchetti NuGet.

2. Cercare `WindowsAzure.Storage`, fare clic su **Installa** e accettare le condizioni per l'uso.

    Verrà quindi scaricato e installato l’[SDK di Archiviazione di Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) e verrà aggiunto un riferimento ad esso.

3. Nel file **Program.cs** aggiungere le istruzioni seguenti all’inizio del file:

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. Nella classe **Programma** aggiungere i campi di classe seguenti, sostituendo la stringa di connessione dell'account di archiviazione.

        static string storageConnectionString = "{storage connection string}";

    Aggiungere quindi il metodo seguente (è possibile sostituire qualsiasi nome di contenitore di BLOB, in questa esercitazione viene utilizzato **iothubfileuploadtutorial**):
   
        private static async Task<string> GenerateBlobUriAsync()
        {
            var storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("iothubfileuploadtutorial");
            await blobContainer.CreateIfNotExistsAsync();

            var blobName = String.Format("deviceUpload_{0}", Guid.NewGuid().ToString());
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);

            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
            sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
            sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
            sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            return blob.Uri + sasBlobToken;
        }

    Questo metodo crea un nuovo riferimento al BLOB e genera un URI della firma di accesso condiviso, come descritto in [Creare e utilizzare una firma di accesso condiviso con il servizio BLOB](https://azure.microsoft.com/it-IT/documentation/articles/storage-dotnet-shared-access-signature-part-2/). Si noti che il metodo precedente genera un URI della firma valido per 24 ore. Se il dispositivo di destinazione richiede più tempo per caricare il file (ad esempio si connette raramente, dispone di una connettività non affidabile per caricare un file di grandi dimensioni), è possibile considerare tempi di scadenza per le firme.

5. Modificare **SendCloudToDeviceMessageAsync** nel modo seguente:

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message();
            commandMessage.Properties["command"] = "FileUpload";
            commandMessage.Properties["fileUri"] = await GenerateBlobUriAsync();
            commandMessage.Ack = DeliveryAcknowledgement.Full;

            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Questo metodo invia un messaggio da cloud a dispositivo che contiene due proprietà applicazione: una che identifica il messaggio come un comando per caricare un file, l'altro che contiene l'URI del BLOB. Questo componente richiede inoltre i riconoscimenti completi della consegna. Si noti che le informazioni nelle due proprietà applicazione possono essere serializzate in un corpo del messaggio, ma questo implicherebbe un’ulteriore elaborazione per serializzare e deserializzare le informazioni.

<!-- Links -->

[Informazioni sull'archiviazione di Azure]: https://azure.microsoft.com/it-IT/documentation/articles/storage-create-storage-account/#create-a-storage-account

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/it-IT/library/hh680901(v=pandp.50).aspx
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->

<!---HONumber=Nov15_HO3-->