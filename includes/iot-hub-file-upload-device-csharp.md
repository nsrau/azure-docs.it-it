## Associare un account di archiviazione di Azure all'hub IoT

Poiché il dispositivo simulato caricherà un file in un BLOB di Archiviazione di Azure, è necessario disporre di un account di Archiviazione di Azure associato all'hub IoT. È possibile usare un account di archiviazione esistente o seguire le istruzioni contenute in [Informazioni sugli account di archiviazione di Azure] per crearne uno nuovo. Per associare un account di Archiviazione di Azure all'hub IoT, seguire le istruzioni in [Gestire hub IoT tramite il portale di Azure].

## Caricare un file da un dispositivo simulato

In questa sezione viene modificata l'applicazione del dispositivo simulato creata in [Inviare messaggi da cloud a dispositivo con hub IoT] per ricevere i messaggi da cloud a dispositivo dall'hub IoT.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **SimulatedDevice**, scegliere **Aggiungi** e quindi **Elemento esistente...**. Passare a un file di immagine e includerlo nel progetto. Nell'esercitazione si presuppone che l'immagine sia denominata `image.jpg`.

2. Fare clic con il pulsante destro del mouse sull'immagine e quindi scegliere **Proprietà**. Controllare che **Copia in directory di output** sia impostato su **Copia sempre**.

    ![][1]

3. Nel file **Program.cs** aggiungere le istruzioni seguenti all’inizio del file:

        using System.IO;

4. Aggiungere il metodo seguente alla classe **Program**:
         
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();

            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }

            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }

    Il metodo `UploadToBlobAsync` accetta il nome del file e l'origine del flusso del file da caricare e gestisce il caricamento nell'archiviazione. Nell'applicazione console viene visualizzato il tempo necessario per caricare il file.

5. Aggiungere il metodo seguente al metodo **Main** immediatamente prima della riga `Console.ReadLine()`:

        SendToBlobAsync();

> [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN [Transient Fault Handling] (Gestione degli errori temporanei).

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Informazioni sugli account di archiviazione di Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Gestire hub IoT tramite il portale di Azure]: ../articles/iot-hub-manage-through-portal/#file-upload

<!-- Images -->
[1]: ../articles/iot-hub/media/iot-hub-csharp-csharp-file-upload/image-properties.png

<!---HONumber=AcomDC_0622_2016-->