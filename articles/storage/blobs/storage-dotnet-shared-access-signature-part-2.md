---
title: Creare e usare una firma di accesso condiviso (SAS) con Archiviazione BLOB di Azure | Microsoft Docs
description: Questa esercitazione illustra come creare firme di accesso condiviso da usare con l'archiviazione BLOB e come usarle nelle applicazioni client.
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 491e0b3c-76d4-4149-9a80-bbbd683b1f3e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: marsma
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 0d7bede352667931527c8583cb172a46a37b5aa8
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Firme di accesso condiviso, parte 2: creare e usare una firma di accesso condiviso con l'archiviazione BLOB

[parte 1](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) di questa esercitazione è stata fornita una descrizione dettagliata delle firme di accesso condiviso e sono state illustrate le procedure consigliate per utilizzarle. La parte 2 illustra come generare e poi usare le firme di accesso condiviso con l'archiviazione BLOB. Negli esempi, scritti in C#, viene utilizzata la libreria client di archiviazione di Azure per .NET. Gli esempi inclusi in questa esercitazione:

* Generare una firma di accesso condiviso per un contenitore
* Generare una firma di accesso condiviso per un BLOB
* Creare criteri di accesso archiviati per gestire le firme per le risorse di un contenitore
* Testare le firme di accesso condiviso in un'applicazione client

## <a name="about-this-tutorial"></a>Informazioni sull'esercitazione
In questa esercitazione vengono create due applicazioni console che illustrano la creazione e l'uso delle firme di accesso condiviso per contenitori e BLOB:

**Applicazione 1**: applicazione di gestione. Genera una firma di accesso condiviso per un contenitore e un BLOB. Include la chiave di accesso dell'account di archiviazione nel codice sorgente.

**Applicazione 2**: applicazione client. Accede alle risorse di contenitore e BLOB usando le firme di accesso condiviso create con la prima applicazione. Usa solo le firme di accesso condiviso per accedere alle risorse di contenitore e BLOB. *Non* include la chiave di accesso dell'account di archiviazione.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Parte 1: creare un'applicazione console per generare firme di accesso condiviso
In primo luogo verificare che la libreria client di archiviazione di Azure per .NET sia installata. È possibile installare il [pacchetto NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "pacchetto NuGet") contenente gli assembly più aggiornati per la libreria client. Questo è il metodo consigliato per verificare se si dispone delle correzioni più recenti. È anche possibile scaricare la libreria client inclusa nella versione più recente di [Azure SDK per .NET](https://azure.microsoft.com/downloads/).

In Visual Studio creare una nuova applicazione console Windows e assegnare ad essa il nome **GenerateSharedAccessSignatures**. Aggiungere i riferimenti a [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) e [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) usando uno degli approcci seguenti:

* Usare la [Gestione pacchetti NuGet](https://docs.nuget.org/consume/installing-nuget) in Visual Studio. Selezionare **Progetto** > **Gestisci pacchetti NuGet**, eseguire la ricerca online per ogni pacchetto (Microsoft.WindowsAzure.ConfigurationManager e WindowsAzure.Storage) e installarli.
* In alternativa, individuare gli assembly nell'installazione di Azure SDK e aggiungervi i riferimenti:
  * Microsoft.WindowsAzure.Configuration.dll
  * Microsoft.WindowsAzure.Storage.dll

All'inizio del file Program.cs aggiungere le direttive **Using** seguenti:

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Modificare il file app.config in modo che contenga un'impostazione di configurazione con una stringa di connessione che punta all'account di archiviazione. Il file app.config dovrebbe essere simile al seguente:

```xml
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
  </appSettings>
</configuration>
```

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Generare l'URI di una firma di accesso condiviso per un contenitore
Per iniziare, viene aggiunto un metodo per generare una firma di accesso condiviso per un nuovo contenitore. In questo caso la firma non è associata a criteri di accesso archiviati, pertanto include nell'URI le informazioni relative alla scadenza e alle autorizzazioni concesse.

In primo luogo, aggiungere al metodo **Main()** il codice per autenticare l'accesso all'account di archiviazione e creare un nuovo contenitore:

```csharp
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
```

Aggiungere quindi un metodo che genera la firma di accesso condiviso per il contenitore e restituisce l'URI della firma:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
    //Set the expiry time and permissions for the container.
    //In this case no start time is specified, so the shared access signature becomes valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Aggiungere le righe seguenti alla fine del metodo **Main()**, prima della chiamata a **Console.ReadLine()**, per chiamare **GetContainerSasUri()** e scrivere l'URI della firma nella finestra della console:

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

Compilare ed eseguire nell'output l'URI della firma di accesso condiviso per il nuovo contenitore. L'URI sarà simile al seguente:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

Dopo avere eseguito il codice, la firma di accesso condiviso creata per il contenitore rimarrà valida per le 24 ore successive. La firma concede l'autorizzazione per elencare i BLOB e scrivere nuovi BLOB nel contenitore.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Generare l'URI di una firma di accesso condiviso per un BLOB
A questo punto viene scritto codice simile per creare un nuovo BLOB all'interno del contenitore e per generarvi una firma di accesso condiviso. Tale firma non è associata a criteri di accesso archiviati, pertanto include nell'URI le informazioni relative all'ora di inizio, alla scadenza e alle autorizzazioni.

Aggiungere un nuovo metodo che crea un nuovo BLOB e vi scrive del testo, quindi genera una firma di accesso condiviso e restituisce l'URI della firma:

```csharp
static string GetBlobSasUri(CloudBlobContainer container)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature (SAS).";
    blob.UploadText(blobContent);

    //Set the expiry time and permissions for the blob.
    //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
    //The shared access signature will be valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

Alla fine del metodo **Main()**, prima della chiamata a **Console.ReadLine()**, aggiungere le righe seguenti per chiamare **GetBlobSasUri()** e scrivere l'URI della firma di accesso condiviso nella finestra della console:

```csharp
//Generate a SAS URI for a blob within the container, without a stored access policy.
Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
Console.WriteLine();
```

Compilare ed eseguire nell'output l'URI della firma di accesso condiviso per il nuovo BLOB. L'URI sarà simile al seguente:

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>Creare criteri di accesso archiviati per il contenitore
A questo punto verranno creati criteri di accesso archiviati per il contenitore che consentiranno di definire i vincoli per le eventuali firme di accesso condiviso ad essi associate.

Negli esempi precedenti l'ora di inizio (implicitamente o esplicitamente), la scadenza e le autorizzazioni sono state specificate nell'URI stesso della firma di accesso condiviso. Negli esempi seguenti questi parametri vengono specificati nei criteri di accesso archiviati, non nella firma di accesso condiviso. In tal modo sarà possibile modificare questi vincoli senza creare nuovamente la firma di accesso condiviso.

È possibile specificare uno o più vincoli nella firma di accesso condiviso e quelli rimanenti nei criteri di accesso archiviati. Tuttavia, non è possibile specificare l'ora di inizio, la scadenza e le autorizzazioni in entrambe le posizioni. Ad esempio, non è possibile specificare le autorizzazioni nella firma di accesso condiviso e specificarli anche nei criteri di accesso archiviati.

Quando si aggiunge un criterio di accesso archiviato a un contenitore, è necessario ottenere le autorizzazioni del contenitore esistente, aggiungere il nuovo criterio di accesso e quindi impostare le autorizzazioni del contenitore.

Aggiungere un nuovo metodo che crea nuovi criteri di accesso archiviati su un contenitore e restituisce il nome dei criteri:

```csharp
static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
    string policyName)
{
    //Get the container's existing permissions.
    BlobContainerPermissions permissions = container.GetPermissions();

    //Create a new shared access policy and define its constraints.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    //Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    container.SetPermissions(permissions);
}
```

Alla fine del metodo **Main()**, prima della chiamata a **Console.ReadLine()**, aggiungere le righe seguenti per cancellare prima di tutto eventuali criteri di accesso esistenti e quindi per chiamare il metodo **CreateSharedAccessPolicy()**:

```csharp
//Clear any existing access policies on container.
BlobContainerPermissions perms = container.GetPermissions();
perms.SharedAccessPolicies.Clear();
container.SetPermissions(perms);

//Create a new access policy on the container, which may be optionally used to provide constraints for
//shared access signatures on the container and the blob.
string sharedAccessPolicyName = "tutorialpolicy";
CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
```

Quando si cancella un criterio di accesso in un contenitore, è necessario prima ottenere le autorizzazioni del contenitore esistente, quindi cancellare le autorizzazioni e infine reimpostarle.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Generare l'URI di una firma di accesso condiviso per un contenitore che usa criteri di accesso
Viene quindi creata un'altra firma di accesso condiviso per il contenitore creato in precedenza, ma questa volta la firma viene associata ai criteri di accesso archiviati creati nell'esempio precedente.

Aggiungere un nuovo metodo per generare un'altra firma di accesso condiviso per il contenitore:

```csharp
static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Generate the shared access signature on the container. In this case, all of the constraints for the
    //shared access signature are specified on the stored access policy.
    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Alla fine del metodo **Main()**, prima della chiamata a **Console.ReadLine()**, aggiungere le righe seguenti per chiamare il metodo **GetContainerSasUriWithPolicy**:

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Generazione dell'URI di una firma di accesso condiviso per un BLOB che utilizza criteri di accesso
Viene infine aggiunto un metodo simile per creare un altro BLOB e generare una firma di accesso condiviso associata a criteri di accesso archiviati.

Aggiungere un nuovo metodo per creare un BLOB e generare una firma di accesso condiviso:

```csharp
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
```

Alla fine del metodo **Main()**, prima della chiamata a **Console.ReadLine()**, aggiungere le righe seguenti per chiamare il metodo **GetBlobSasUriWithPolicy**:

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

L'intero metodo **Main()** dovrebbe ora essere simile al seguente. Eseguirlo per scrivere gli URI delle firme di accesso condiviso nella finestra della console, quindi copiarli e incollarli in un file di testo per utilizzarli nella seconda parte dell'esercitazione.

```csharp
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
```

Quando si esegue l'applicazione console GenerateSharedAccessSignatures, l'output sarà simile a quello seguente. Queste sono le firme di accesso condiviso usate nella parte 2 dell'esercitazione.

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Parte 2: creare un'applicazione console per testare le firme di accesso condiviso
Per testare le firme di accesso condiviso create negli esempi precedenti, viene creata una seconda applicazione console che usa le firme per eseguire operazioni sul contenitore e su un BLOB.

> [!NOTE]
> Se sono passate più di 24 ore da quando è stata completata la prima parte dell'esercitazione, le firme generate non saranno più valide. In questo caso, è necessario eseguire il codice nella prima applicazione console per generare nuove firme di accesso condiviso da usare nella seconda parte dell'esercitazione.
>

In Visual Studio creare una nuova applicazione console Windows e assegnare ad essa il nome **ConsumeSharedAccessSignatures**. Aggiungere i riferimenti a [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) e [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/), come è già stato fatto in precedenza.

All'inizio del file Program.cs aggiungere le direttive **Using** seguenti:

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Nel corpo del metodo **Main()** aggiungere le seguenti costanti di stringa, modificando i valori in base alle firme di accesso condiviso generate nella parte 1 dell'esercitazione.

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Aggiungere un metodo per testare le operazioni su contenitore con una firma di accesso condiviso
Viene ora aggiunto un metodo per testare alcune operazioni sul contenitore con una firma di accesso condiviso. La firma di accesso condiviso viene usata per restituire un riferimento al contenitore, autenticando l'accesso al contenitore sulla base della sola firma.

Aggiungere il metodo seguente a Program.cs:

```csharp
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
        blob.UploadText(blobContent);

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
```

Aggiornare il metodo **Main()** in modo che chiami **UseContainerSAS()** con entrambe le firme di accesso condiviso create per il contenitore:

```csharp
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
```

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Aggiungere un metodo per testare le operazioni su BLOB con una firma di accesso condiviso
Viene infine aggiunto un metodo per testare alcune operazioni sul BLOB con una firma di accesso condiviso. In questo caso viene usato il costruttore **CloudBlockBlob(String)**, passando nella firma di accesso condiviso, per restituire un riferimento al BLOB. Non è richiesto un altro tipo di autenticazione; è basato esclusivamente sulla firma.

Aggiungere il metodo seguente a Program.cs:

```csharp
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
```

Aggiornare il metodo **Main()** in modo che chiami **UseBlobSAS()** con entrambe le firme di accesso condiviso create nel BLOB:

```csharp
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
```

Eseguire l'applicazione console e osservare l'output per verificare le operazioni consentite in base alle firme. L'output nella finestra della console sarà simile al seguente:

```
Write operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

List operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

Read operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

Delete operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

...
```

## <a name="next-steps"></a>Passaggi successivi

* [Firme di accesso condiviso, parte 1: informazioni sul modello di firma di accesso condiviso](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Gestire l'accesso in lettura anonimo a contenitori e BLOB](storage-manage-access-to-resources.md)
* [Delega dell'accesso con una firma di accesso condiviso (API REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Introduzione alla firma di accesso condiviso per tabelle e code](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

