<properties linkid="manage-services-storage-net-shared-access-signature-part-2" urlDisplayName="" pageTitle="Create and use a SAS with the Blob Service | Microsoft Azure" metaKeywords="Azure blob, shared access signatures, stored access policy" description="Explore generating and using shared access signatures with the Blob service" metaCanonical="" services="storage" documentationCenter="" title="Part 2: Create and Use a SAS with the Blob Service" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

Firme di accesso condiviso, parte 2: creazione e utilizzo di una firma di accesso condiviso con il servizio BLOB
================================================================================================================

Nella [parte 1](../storage-dotnet-shared-access-signature-part-1/) di questa esercitazione è stata fornita una descrizione dettagliata delle firme di accesso condiviso e sono state illustrate le procedure consigliate per utilizzarle. Nella parte 2 verrà spiegato come generare e quindi utilizzare le firme di accesso condiviso con il servizio BLOB di Azure. Negli esempi, scritti in C\#, viene utilizzata la libreria client di archiviazione di Azure per .NET. Gli scenari presentati includono i seguenti aspetti relativi all'utilizzo delle firme di accesso condiviso:

-   Generazione di una firma di accesso condiviso per un contenitore
-   Generazione di una firma di accesso condiviso per un BLOB
-   Creazione di criteri di accesso archiviati per gestire le firme per le risorse di un contenitore
-   Test delle firme di accesso condiviso da un'applicazione client

Informazioni sull'esercitazione
===============================

In questa esercitazione verrà illustrata in dettaglio la procedura di creazione delle firme di accesso condiviso per contenitori e BLOB e verranno create due applicazioni console. La prima applicazione console genera firme di accesso condiviso per un contenitore e per un BLOB. Tale applicazione conosce le chiavi dell'account di archiviazione. La seconda applicazione console, che fungerà da applicazione client, accede alle risorse di contenitore e BLOB utilizzando le firme di accesso condiviso create con la prima applicazione. Tale applicazione utilizza le firme di accesso condiviso solo per autenticare l'accesso alle risorse di contenitore e BLOB, pertanto non conosce le chiavi dell'account.

Parte 1: creazione di un'applicazione console per generare firme di accesso condiviso
=====================================================================================

In primo luogo verificare che la libreria client di archiviazione di Azure per .NET (versione 2.0) sia installata. È possibile installare il [pacchetto NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "pacchetto NuGet") contenente gli assembly più aggiornati per la libreria client. Questa è la procedura consigliata per essere certi di disporre delle correzioni più recenti. È inoltre possibile scaricare la libreria client inclusa nella versione più recente di [Azure SDK per .NET](http://www.windowsazure.com/en-us/downloads/).

In Visual Studio creare una nuova applicazione console Windows e assegnare ad essa il nome **GenerateSharedAccessSignatures**. Aggiungere i riferimenti a **Microsoft.WindowsAzure.Configuration.dll** e **Microsoft.WindowsAzure.Storage.dll**, utilizzando uno dei seguenti approcci:

-   Se si desidera installare il pacchetto NuGet, installare prima l'[estensione Gestione pacchetti NuGet per Visual Studio](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). In Visual Studio selezionare **Progetto | Manage NuGet Packages**, cercare online **Archiviazione di Azure** e attenersi alle istruzioni per l'installazione.
-   In alternativa, individuare gli assembly nell'installazione di Azure SDK e aggiungervi i riferimenti.

All'inizio del file Program.cs aggiungere le istruzioni **using** seguenti:

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Modificare il file app.config in modo che contenga un'impostazione di configurazione con una stringa di connessione che punta all'account di archiviazione. Il file app.config dovrebbe essere simile al seguente:

    <configuration>
      <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings> 
    </configuration>

Generazione dell'URI di una firma di accesso condiviso per un contenitore
-------------------------------------------------------------------------

Per iniziare, verrà aggiunto un metodo per generare una firma di accesso condiviso per un nuovo contenitore. In questo caso la firma non è associata a criteri di accesso archiviati, pertanto include nell'URI le informazioni relative alla scadenza e alle autorizzazioni concesse.

In primo luogo, aggiungere al metodo **Main()** il codice per autenticare l'accesso all'account di archiviazione e creare un nuovo contenitore:

    static void Main(string[] args)
        {
            //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
        
        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();
        
        //Insert calls to the methods created below here...
        
        //Require user input before closing the console window.
        Console.ReadLine();
        }

Aggiungere quindi un nuovo metodo che genera la firma di accesso condiviso per il contenitore e restituisce l'URI della firma:

    static string GetContainerSasUri(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
        //In this case no start time is specified, so the shared access signature becomes valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;
        
        //Generate the shared access signature on the container, setting the constraints directly on the signature.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
        
        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
        }

Aggiungere le righe seguenti alla fine del metodo **Main()**, prima della chiamata a **Console.ReadLine()**, per chiamare **GetContainerSasUri()** e scrivere l'URI della firma nella finestra della console:

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Compilare ed eseguire nell'output l'URI della firma di accesso condiviso per il nuovo contenitore. L'URI sarà simile al seguente:

https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Dopo avere eseguito il codice, la firma di accesso condiviso creata per il contenitore rimarrà valida per le quattro ore successive. La firma concede l'autorizzazione per elencare i BLOB e scrivere un nuovo BLOB nel contenitore.

Generazione dell'URI di una firma di accesso condiviso per un BLOB
------------------------------------------------------------------

A questo punto verrà scritto codice simile per creare un nuovo BLOB all'interno del contenitore e per generarvi una firma di accesso condiviso. Tale firma non è associata a criteri di accesso archiviati, pertanto include nell'URI le informazioni relative all'ora di inizio, alla scadenza e alle autorizzazioni.

Aggiungere un nuovo metodo che crea un nuovo BLOB e vi scrive del testo, quindi genera una firma di accesso condiviso e restituisce l'URI della firma:

    static string GetBlobSasUri(CloudBlobContainer container)
        {
            //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");
        
        //Upload text to the blob. If the blob does not yet exist, it will be created. 
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
            {
                blob.UploadFromStream(ms);
            }
            
            //Set the expiry time and permissions for the blob.
        //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
        //The shared access signature will be valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
        
        //Generate the shared access signature on the blob, setting the constraints directly on the signature.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);
        
        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
        }

Alla fine del metodo **Main()**, prima della chiamata a **Console.ReadLine()**, aggiungere le righe seguenti per chiamare **GetBlobSasUri()** e scrivere l'URI della firma di accesso condiviso nella finestra della console:

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

Compilare ed eseguire nell'output l'URI della firma di accesso condiviso per il nuovo BLOB. L'URI sarà simile al seguente:

https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

Generazione dei criteri di accesso archiviati per il contenitore
----------------------------------------------------------------

A questo punto verranno creati criteri di accesso archiviati per il contenitore che consentiranno di definire i vincoli per le eventuali firme di accesso condiviso ad essi associate.

Negli esempi precedenti l'ora di inizio (implicitamente o esplicitamente), la scadenza e le autorizzazioni sono state specificate nell'URI stesso della firma di accesso condiviso. Negli esempi seguenti questi parametri verranno specificati nei criteri di accesso archiviati e non nella firma di accesso condiviso. In tal modo sarà possibile modificare questi vincoli senza creare nuovamente la firma di accesso condiviso.

Si noti che è possibile specificare uno più vincoli nella firma di accesso condiviso e quelli rimanenti nei criteri di accesso archiviati. È tuttavia possibile specificare ora di inizio, scadenza e autorizzazioni solo in una delle due posizioni, pertanto non è consentito specificare le autorizzazioni sia nella firma di accesso condiviso che nei criteri di accesso archiviati.

Aggiungere un nuovo metodo che crea nuovi criteri di accesso archiviati e restituisce il nome dei criteri:

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container, string policyName)
        {
            //Create a new stored access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
            };
            
            //Get the container's existing permissions.
        BlobContainerPermissions permissions = new BlobContainerPermissions();
        
        //Add the new policy to the container's permissions.
        permissions.SharedAccessPolicies.Clear();
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
        }

Alla fine del metodo **Main()**, prima della chiamata a **Console.ReadLine()**, aggiungere le righe seguenti per chiamare il metodo **CreateSharedAccessPolicy()**:

    //Create an access policy on the container, which may be optionally used to provide constraints for 
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

Generazione dell'URI di una firma di accesso condiviso per un contenitore che utilizza criteri di accesso
---------------------------------------------------------------------------------------------------------

Verrà quindi creata un'altra firma di accesso condiviso per il contenitore creato in precedenza, ma questa volta la firma verrà associata ai criteri di accesso creati nell'esempio precedente.

Aggiungere un nuovo metodo per generare un'altra firma di accesso condiviso per il contenitore:

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
        {
            //Generate the shared access signature on the container. In this case, all of the constraints for the 
        //shared access signature are specified on the stored access policy.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);
        
        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
        }

Alla fine del metodo **Main()**, prima della chiamata a **Console.ReadLine()**, aggiungere le righe seguenti per chiamare il metodo **GetContainerSasUriWithPolicy**:

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

Generazione dell'URI di una firma di accesso condiviso per un BLOB che utilizza criteri di accesso
--------------------------------------------------------------------------------------------------

Verrà infine aggiunto un metodo simile per creare un altro BLOB e generare una firma di accesso condiviso associata a criteri di accesso.

Aggiungere un nuovo metodo per creare un BLOB e generare una firma di accesso condiviso:

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
        {
            //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");
        
        //Upload text to the blob. If the blob does not yet exist, it will be created. 
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a shared access signature. " + 
            "A stored access policy defines the constraints for the signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
            {
                blob.UploadFromStream(ms);
            }
            
            //Generate the shared access signature on the blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);
        
        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
        }

Alla fine del metodo **Main()**, prima della chiamata a **Console.ReadLine()**, aggiungere le righe seguenti per chiamare il metodo **GetBlobSasUriWithPolicy**:

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

L'intero metodo **Main()** dovrebbe ora essere simile al seguente. Eseguirlo per scrivere gli URI delle firme di accesso condiviso nella finestra della console, quindi copiarli e incollarli in un file di testo per utilizzarli nella seconda parte dell'esercitazione.

    static void Main(string[] args)
        {
            //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
        
        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();
        
        //Generate a SAS URI for the container, without a stored access policy.
        Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
        Console.WriteLine();
        
        //Generate a SAS URI for a blob within the container, without a stored access policy.
        Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
        Console.WriteLine();
        
        //Create an access policy on the container, which may be optionally used to provide constraints for 
        //shared access signatures on the container and the blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
        
        //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();
        
        //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();
        
        Console.ReadLine();
        }

Quando si esegue l'applicazione console GenerateSharedAccessSignatures, l'output della finestra della console sarà simile a quello seguente. Queste sono le firme di accesso condiviso che verranno utilizzate nella parte 2 dell'esercitazione.

![sas-console-output-1](./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG)

Parte 2: creazione di un'applicazione console per testare le firme di accesso condiviso
=======================================================================================

Per testare le firme di accesso condiviso create negli esempi precedenti, verrà creata una seconda applicazione console che utilizza le firme per eseguire operazioni sul contenitore e su un BLOB.

Si noti che, se sono passate più di quattro ore da quando è stata completata la prima parte dell'esercitazione, le firme generate con scadenza impostata su quattro ore non saranno più valide. Analogamente le firme associate ai criteri di accesso archiviati scadono dopo 10 ore. Se sono scaduti uno o entrambi questi intervalli, è necessario eseguire il codice nella prima applicazione console per generare nuove firme di accesso condiviso da utilizzare nella seconda parte dell'esercitazione.

In Visual Studio creare una nuova applicazione console Windows e assegnare ad essa il nome **ConsumeSharedAccessSignatures**. Aggiungere i riferimenti a **Microsoft.WindowsAzure.Configuration.dll** e **Microsoft.WindowsAzure.Storage.dll**, come è già stato fatto in precedenza.

All'inizio del file Program.cs aggiungere le istruzioni **using** seguenti:

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Nel corpo del metodo **Main()** aggiungere le seguenti costanti e aggiornarne i valori in base alle firme di accesso condiviso generate nella parte 1 dell'esercitazione.

    static void Main(string[] args)
        {
            string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
        }

Aggiunta di un metodo per testare le operazioni su contenitore con una firma di accesso condiviso
-------------------------------------------------------------------------------------------------

Verrà ora aggiunto un metodo per testare alcune operazioni rappresentative sul contenitore utilizzando una firma di accesso condiviso. Si noti che la firma di accesso condiviso viene utilizzata per restituire un riferimento al contenitore, autenticando l'accesso al contenitore sulla base della sola firma.

Aggiungere il metodo seguente a Program.cs:

    static void UseContainerSAS(string sas)
        {
            //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<Uri> blobUris = new List<Uri>();

        try
            {
                //Write operation: write a new blob to the container. 
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
                MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
                {
                    blob.UploadFromStream(msWrite);
                }
                Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
            }
            catch (StorageException e)
            {
                Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
            }
        
            try
            {
                //List operation: List the blobs in the container, including the one just added.
            foreach (ICloudBlob blobListing in container.ListBlobs())
                {
                    blobUris.Add(blobListing.Uri);
                }
                Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
            }
            catch (StorageException e)
            {
                Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
            }
        
            try
            {
                //Read operation: Get a reference to one of the blobs in the container and read it. 
            CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
                {
                    blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
                }
                Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
            }
            catch (StorageException e)
            {
                Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
            }
            Console.WriteLine();

        try
            {
                //Delete operation: Delete a blob in the container.
            CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
            }
            catch (StorageException e)
            {
                Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
            }        
        }

Aggiornare il metodo **Main()** in modo che chiami **UseContainerSAS()** con entrambe le firme di accesso condiviso create per il contenitore:

    static void Main(string[] args)
        {
            string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy); 
        
        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
        }

Aggiunta di un metodo per testare le operazioni su BLOB con una firma di accesso condiviso
------------------------------------------------------------------------------------------

Verrà infine aggiunto un metodo per testare alcune operazioni rappresentative sul BLOB utilizzando una firma di accesso condiviso. In questo caso verrà utilizzato il costruttore **CloudBlockBlob(String)**, passandolo nella firma di accesso condiviso, per restituire un riferimento al BLOB. Non è richiesto un altro tipo di autenticazione; è basato esclusivamente sulla firma.

Aggiungere il metodo seguente a Program.cs:

    static void UseBlobSAS(string sas)
        {
            //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        try
            {
                //Write operation: write a new blob to the container. 
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
                MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
                {
                    blob.UploadFromStream(msWrite);
                }
                Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
            }
            catch (StorageException e)
            {
                Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
            }
        
            try
            {
                //Read operation: Read the contents of the blob.
            MemoryStream msRead = new MemoryStream();
            using (msRead)
                {
                    blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                    {
                        string line;
                    while ((line = reader.ReadLine()) != null)
                        {
                            Console.WriteLine(line);
                        }
                    }
                }
                Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
            }
            catch (StorageException e)
            {
                Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
            }
        
            try
            {
                //Delete operation: Delete the blob.
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
            }
            catch (StorageException e)
            {
                Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
            }        
        }

Aggiornare il metodo **Main()** in modo che chiami **UseBlobSAS()** con entrambe le firme di accesso condiviso create nel BLOB:

    static void Main(string[] args)
        {
            string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy); 
        
        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
        }

Eseguire l'applicazione console e osservare l'output per verificare le operazioni consentite in base alle firme. L'output nella finestra della console sarà simile al seguente:

![sas-console-output-2](./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG)

Passaggi successivi
===================

[Firme di accesso condiviso, parte 1: informazioni sul modello di firma di accesso condiviso](../storage-dotnet-shared-access-signature-part-1/)

[Gestire l'accesso alle risorse di archiviazione di Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee393343.aspx)

[Delega dell'accesso con una firma di accesso condiviso (API REST)](http://msdn.microsoft.com/en-us/library/windowsazure/ee395415.aspx)

[Introduzione alla firma di accesso condiviso per tabelle e code](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

