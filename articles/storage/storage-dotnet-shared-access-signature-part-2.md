<properties
	pageTitle="Creare e usare una firma di accesso condiviso con l'archiviazione BLOB | Microsoft Azure"
	description="Questa esercitazione illustra come creare firme di accesso condiviso da usare con l'archiviazione BLOB e come usarle dalle applicazioni client."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="tamram"/>


# Firme di accesso condiviso, parte 2: creare e usare una firma di accesso condiviso con l'archiviazione BLOB

## Panoramica

Nella [parte 1](storage-dotnet-shared-access-signature-part-1.md) di questa esercitazione è stata fornita una descrizione dettagliata delle firme di accesso condiviso e sono state illustrate le procedure consigliate per utilizzarle. La parte 2 illustra come generare e poi usare le firme di accesso condiviso con l'archiviazione BLOB. Negli esempi, scritti in C#, viene utilizzata la libreria client di archiviazione di Azure per .NET. Gli scenari presentati includono i seguenti aspetti relativi all'utilizzo delle firme di accesso condiviso:

- Generazione di una firma di accesso condiviso per un contenitore
- Generazione di una firma di accesso condiviso per un BLOB
- Creazione di criteri di accesso archiviati per gestire le firme per le risorse di un contenitore
- Test delle firme di accesso condiviso da un'applicazione client

## Informazioni sull'esercitazione

In questa esercitazione verrà illustrata in dettaglio la procedura di creazione delle firme di accesso condiviso per contenitori e BLOB e verranno create due applicazioni console. La prima applicazione console genera firme di accesso condiviso per un contenitore e per un BLOB. Tale applicazione conosce le chiavi dell'account di archiviazione. La seconda applicazione console, che fungerà da applicazione client, accede alle risorse di contenitore e BLOB utilizzando le firme di accesso condiviso create con la prima applicazione. Tale applicazione usa le firme di accesso condiviso solo per autenticare l'accesso alle risorse di contenitore e BLOB, pertanto non conosce le chiavi dell'account.

## Parte 1: creare un'applicazione console per generare firme di accesso condiviso

In primo luogo verificare che la libreria client di archiviazione di Azure per .NET sia installata. È possibile installare il [pacchetto NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "Pacchetto NuGet") contenente gli assembly più aggiornati per la libreria client. Questa è la procedura consigliata per essere certi di disporre delle correzioni più recenti. È inoltre possibile scaricare la libreria client inclusa nella versione più recente di [Azure SDK per .NET](https://azure.microsoft.com/downloads/).

In Visual Studio creare una nuova applicazione console Windows e assegnare ad essa il nome **GenerateSharedAccessSignatures**. Aggiungere i riferimenti a **Microsoft.WindowsAzure.Configuration.dll** e **Microsoft.WindowsAzure.Storage.dll**, utilizzando uno dei seguenti approcci:

- 	Se si desidera installare il pacchetto NuGet, installare prima l'[estensione Gestione pacchetti NuGet per Visual Studio](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). In Visual Studio selezionare **Progetto | Manage NuGet Packages**, cercare online **Archiviazione di Azure** e attenersi alle istruzioni per l'installazione.
- 	In alternativa, individuare gli assembly nell'installazione di Azure SDK e aggiungervi i riferimenti.

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

### Generazione dell'URI di una firma di accesso condiviso per un contenitore

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
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
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

Dopo avere eseguito il codice, la firma di accesso condiviso creata per il contenitore rimarrà valida per le ventiquattro ore successive. La firma concede l'autorizzazione per elencare i BLOB e scrivere un nuovo BLOB nel contenitore.

### Generazione dell'URI di una firma di accesso condiviso per un BLOB

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
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
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

### Generazione dei criteri di accesso archiviati per il contenitore

A questo punto verranno creati criteri di accesso archiviati per il contenitore che consentiranno di definire i vincoli per le eventuali firme di accesso condiviso ad essi associate.

Negli esempi precedenti l'ora di inizio (implicitamente o esplicitamente), la scadenza e le autorizzazioni sono state specificate nell'URI stesso della firma di accesso condiviso. Negli esempi seguenti questi parametri verranno specificati nei criteri di accesso archiviati e non nella firma di accesso condiviso. In tal modo sarà possibile modificare questi vincoli senza creare nuovamente la firma di accesso condiviso.

È possibile specificare uno più vincoli nella firma di accesso condiviso e quelli rimanenti nei criteri di accesso archiviati. È tuttavia possibile specificare ora di inizio, scadenza e autorizzazioni solo in una delle due posizioni, pertanto non è consentito specificare le autorizzazioni sia nella firma di accesso condiviso che nei criteri di accesso archiviati.

Si noti che quando si aggiunge un criterio di accesso a un contenitore, è necessario ottenere le autorizzazioni del contenitore esistente, aggiungere il nuovo criterio di accesso e quindi impostare le autorizzazioni del contenitore.

Aggiungere un nuovo metodo che crea nuovi criteri di accesso archiviati su un contenitore e restituisce il nome dei criteri:

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
		string policyName)
    {
        //Create a new shared access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
        };

        //Get the container's existing permissions.
        BlobContainerPermissions permissions = container.GetPermissions();

        //Add the new policy to the container's permissions, and set the container's permissions.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

Alla fine del metodo **Main()**, prima della chiamata a **Console.ReadLine()**, aggiungere le righe seguenti per cancellare prima di tutto eventuali criteri di accesso esistenti, quindi per chiamare il metodo **CreateSharedAccessPolicy()**:

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

Si noti che quando si cancella un criterio di accesso in un contenitore, è necessario prima ottenere le autorizzazioni del contenitore esistente, quindi cancellare le autorizzazioni e infine reimpostarle.

### Generazione dell'URI di una firma di accesso condiviso per un contenitore che utilizza criteri di accesso

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

### Generazione dell'URI di una firma di accesso condiviso per un BLOB che utilizza criteri di accesso

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

        //Clear any existing access policies on container.
        BlobContainerPermissions perms = container.GetPermissions();
        perms.SharedAccessPolicies.Clear();
        container.SetPermissions(perms);

        //Create a new access policy on the container, which may be optionally used to provide constraints for
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

Quando si esegue l'applicazione console GenerateSharedAccessSignatures, l'output della finestra della console sarà simile a quello seguente. Queste sono le firme di accesso condiviso che verranno usate nella parte 2 dell'esercitazione.

![sas-console-output-1][sas-console-output-1]

## Parte 2: creare un'applicazione console per testare le firme di accesso condiviso

Per testare le firme di accesso condiviso create negli esempi precedenti, verrà creata una seconda applicazione console che usa le firme per eseguire operazioni sul contenitore e su un BLOB.

> [AZURE.NOTE] Se sono passate più di 24 ore da quando è stata completata la prima parte dell'esercitazione, le firme generate non saranno più valide. In questo caso, è necessario eseguire il codice nella prima applicazione console per generare nuove firme di accesso condiviso da usare nella seconda parte dell'esercitazione.

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

### Aggiunta di un metodo per testare le operazioni su contenitore con una firma di accesso condiviso

Verrà ora aggiunto un metodo per testare alcune operazioni rappresentative sul contenitore utilizzando una firma di accesso condiviso. Si noti che la firma di accesso condiviso viene utilizzata per restituire un riferimento al contenitore, autenticando l'accesso al contenitore sulla base della sola firma.

Aggiungere il metodo seguente a Program.cs:

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<ICloudBlob> blobList = new List<ICloudBlob>();

        //Write operation: write a new blob to the container.
        try
        {
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

        //List operation: List the blobs in the container.
        try
        {
            foreach (ICloudBlob blob in container.ListBlobs())
            {
                blobList.Add(blob);
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

        //Read operation: Get a reference to one of the blobs in the container and read it.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
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

        //Delete operation: Delete a blob in the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
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

	    Console.ReadLine();
	}


### Aggiunta di un metodo per testare le operazioni su BLOB con una firma di accesso condiviso

Verrà infine aggiunto un metodo per testare alcune operazioni rappresentative sul BLOB utilizzando una firma di accesso condiviso. In questo caso verrà utilizzato il costruttore **CloudBlockBlob(String)**, passandolo nella firma di accesso condiviso, per restituire un riferimento al BLOB. Non è richiesto un altro tipo di autenticazione; è basato esclusivamente sulla firma.

Aggiungere il metodo seguente a Program.cs:

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        //Write operation: Write a new blob to the container.
        try
        {
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

        //Read operation: Read the contents of the blob.
        try
        {
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

        //Delete operation: Delete the blob.
        try
        {
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

![sas-console-output-2][sas-console-output-2]

## Passaggi successivi

[Firme di accesso condiviso, parte 1: informazioni sul modello di firma di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md)

[Gestire l'accesso in lettura anonimo a contenitori e BLOB](storage-manage-access-to-resources.md)

[Delega dell'accesso con una firma di accesso condiviso (API REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[Introduzione alla firma di accesso condiviso per tabelle e code](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG

<!---HONumber=AcomDC_0525_2016-->