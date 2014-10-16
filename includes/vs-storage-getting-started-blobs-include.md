###### Creare un contenitore

I BLOB di archiviazione si trovano nei contenitori esattamente come i file si trovano nelle cartelle.

-   Per ottenere un riferimento al contenitore che si desidera utilizzare, è possibile utilizzare un oggetto **CloudBlobClient**.

-   È possibile chiamare il metodo CreateCloudBlobClient() per creare un contenitore, se necessario.

Il codice seguente mostra come creare un contenitore di archiviazione BLOB. Il codice crea un oggetto **BlobClient**, in modo da permettere di accedere alle funzioni dell'oggetto, ad esempio per creare un contenitore di archiviazione. Il codice tenta quindi di fare riferimento a un contenitore di archiviazione denominato "mycontainer". Se non è in grado di trovare un contenitore con quel nome, ne crea uno.

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “mycontainer.”
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If “mycontainer” doesn’t exist, create it.
    container.CreateIfNotExists();

**NOTA:** Usare questo blocco di codice davanti al codice nelle sezioni seguenti.

###### Caricare un BLOB in un contenitore

Per caricare un file BLOB in un contenitore, ottenere un riferimento a un contenitore e usarlo per ottenere un riferimento a un BLOB. Dopo aver ottenuto un riferimento al BLOB, sarà possibile caricarvi qualsiasi flusso di dati chiamando il metodo UploadFromStream. Questa operazione permetterà di creare il BLOB, se non esiste già, oppure di sovrascriverlo se esiste già. Nell'esempio seguente viene illustrato come caricare un BLOB in un contenitore e si presuppone che il contenitore sia già stato creato.

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

###### Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, ottenere prima un riferimento al contenitore. Sarà quindi possibile chiamare il metodo ListBlobs() del contenitore per recuperare i BLOB e/o le directory disponibili. Per accedere all'insieme avanzato di proprietà e metodi per un oggetto **IListBlobItem** restituito, è necessario eseguirne il cast in un oggetto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Se il tipo del BLOB non è noto, è possibile usare un controllo del tipo per determinare quello in cui viene eseguito il cast. Il codice seguente illustra come recuperare e restituire come output l'URI di ogni elemento in un contenitore denominato "foto".

    // Get a reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop through items in the container and output the length and URI for each 
    // item.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

È possibile elencare i contenuti di un contenitore BLOB in altri modi. Per altre informazioni, vedere [Come utilizzare l'archiviazione BLOB da .NET][Come utilizzare l'archiviazione BLOB da .NET].

###### Scaricare un BLOB

Per scaricare un BLOB, ottenere prima di tutto un riferimento al BLOB, quindi chiamare il metodo DownloadToStream(). L'esempio seguente usa il metodo DownloadToStream() per trasferire i contenuti del BLOB a un oggetto stream che è quindi possibile salvare come file locale.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

È possibile salvare i BLOB come file in altri modi. Per altre informazioni, vedere [Come utilizzare l'archiviazione BLOB da .NET][1].

###### Eliminare un BLOB

Per eliminare un BLOB, ottenere prima di tutto un riferimento al BLOB, quindi chiamare il metodo Delete().

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();

  [Come utilizzare l'archiviazione BLOB da .NET]: http://azure.microsoft.com/it-it/documentation/articles/storage-dotnet-how-to-use-blobs/#list-blob
  [1]: http://azure.microsoft.com/it-it/documentation/articles/storage-dotnet-how-to-use-blobs/#download-blobs
