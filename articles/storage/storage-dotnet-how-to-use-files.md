<properties
			pageTitle="Come usare l'archiviazione file di Azure con PowerShell e .NET | Microsoft Azure"
	description="Informazioni su come usare l'archiviazione file di Azure per creare condivisioni file nel cloud e gestire il contenuto di file. Archiviazione file consente alle aziende di spostare le applicazioni dipendenti da condivisioni file SMB in Azure. Rendere persistenti le credenziali di account di archiviazione per la macchina virtuale in modo da ristabilire la connessione alla condivisione file al riavvio."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="adinah"
	editor=""/>

<tags ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/04/2015"
	ms.author="tamram"/>

# Come usare l'archiviazione file di Azure con PowerShell e .NET

## Panoramica

Il servizio File di Azure espone condivisioni di file utilizzando il protocollo SMB 2.1 standard. Le applicazioni eseguite in Azure possono usarlo per condividere file tra macchine virtuali tramite API del file system note, quali ReadFile e WriteFile. Inoltre, i file inoltre sono accessibili contemporaneamente tramite un'interfaccia REST, che consente di aprire un'ampia gamma di scenari ibridi. I file di Azure si basano infine sulla stessa tecnologia dei servizi BLOB, tabelle e di accodamento, pertanto i file di Azure sono in grado di usare le caratteristiche esistenti di disponibilità, durabilità, scalabilità e ridondanza geografica integrate nella piattaforma di archiviazione di Azure.

## Informazioni sull'esercitazione

In questa guida introduttiva vengono illustrate le nozioni di base sull'uso dell'archiviazione file di Microsoft Azure. In questa esercitazione si apprenderà come:

- Usare Azure PowerShell per visualizzare le informazioni per creare una nuova condivisione file di Azure, aggiungere una directory, caricare un file locale nella condivisione ed elencare i file nella directory.
- Montare la condivisione file da una macchina virtuale di Azure, con la stessa procedura usata per una condivisione SMB.
- Usare la Libreria del client di archiviazione di Azure per .NET per accedere alla condivisione di file da un'applicazione locale. Creare un'applicazione console ed eseguire queste azioni con la condivisione file:
	- Scrivere il contenuto di un file della condivisione nella finestra della console.
	- Impostare la quota (dimensione massima) per la condivisione file.
	- Creare una firma di accesso condiviso per un file che usa un criterio di accesso condiviso definito nella condivisione.
	- Copiare un file in un altro file nello stesso account di archiviazione.
	- Copiare un file in un BLOB nello stesso account di archiviazione.

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]


## Creare un account di archiviazione di Azure

L'archiviazione file di Azure è attualmente in anteprima. Per richiedere l'accesso all'anteprima, passare al [portale di anteprima di Azure](/services/preview/) e richiedere l'accesso ai **file di Azure**. Dopo l'approvazione della richiesta viene inviato un avviso che indica che è possibile accedere all'anteprima dell'archiviazione file. È quindi possibile creare un account di archiviazione per l'accesso all'archiviazione file.

> [AZURE.NOTE]L'archiviazione file è attualmente disponibile solo per i nuovi account di archiviazione. Dopo aver ottenuto l'accesso all'archiviazione file per la sottoscrizione, creare un nuovo account di archiviazione da usare con questa guida.
>
> L'archiviazione file di Azure attualmente non supporta le firme di accesso condiviso.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Usare PowerShell per gestire una condivisione file

A questo punto si utilizzerà PowerShell per creare una condivisione file. Dopo che la condivisione file è stata creata, è possibile montarla da qualsiasi file system che supporti SMB 2.1.

### Installare i cmdlet di PowerShell per l'archiviazione di Azure

Per prepararsi all'uso di PowerShell, scaricare e installare i cmdlet di Azure PowerShell. Vedere [Come installare e configurare Azure PowerShell](../install-configure-powershell.md) per le istruzioni relative al punto di installazione e all’installazione.

> [AZURE.NOTE]I cmdlet di PowerShell per il servizio file sono disponibili solo nell'ultimo modulo di Azure PowerShell, versione 0.8.5 o successive. Si consiglia di scaricare e installare oppure aggiornare il modulo alla versione di Azure PowerShell più recente.

Aprire una finestra di Azure PowerShell facendo clic su **Start** e digitando **Azure PowerShell**. La finestra di Azure PowerShell carica automaticamente il modulo di Azure PowerShell.

### Creare un contesto per l'account e la chiave di archiviazione

A questo punto, creare il contesto dell'account di archiviazione. Il contesto incapsula il nome e la chiave dell'account di archiviazione. Per informazioni sulla copia della chiave dell’account dal portale di Azure, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

Sostituire `storage-account-name` e `storage-account-key` con il nome e la chiave dell'account di archiviazione nel seguente esempio.

	# create a context for account and key
	$ctx=New-AzureStorageContext storage-account-name storage-account-key

### Creare una nuova condivisione file

Creare quindi la nuova condivisione, denominata `logs`.

	# create a new share
	$s = New-AzureStorageShare logs -Context $ctx

A questo punto si ha una condivisione file nell'archiviazione file. Vengono quindi aggiunti un file e una directory.

> [AZURE.IMPORTANT]Il nome della condivisione file deve essere composto solo da caratteri minuscoli. Per dettagli su come denominare condivisioni e file, vedere [Denominazione e riferimento a condivisioni, directory, file e metadati](https://msdn.microsoft.com/library/azure/dn167011.aspx).

### Creare una directory nella condivisione file

A questo punto, si crea una directory nella condivisione. Nell'esempio seguente la directory viene denominata `CustomLogs`.

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path CustomLogs

### Caricare un file locale nella directory

Caricare un file locale nella directory. Nel seguente esempio viene caricato un file da `C:\temp\Log1.txt`. Modificare il percorso del file in modo che punti a un file valido nel computer locale.

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs

### Elencare i file nella directory

Per visualizzare un file nella directory, è possibile elencare i file della directory. Questo comando elenca anche le sottodirectory, ma in questo esempio non ci sono sottodirectory, quindi viene elencato solo il file.

	# list files in the new directory
	Get-AzureStorageFile -Share $s -Path CustomLogs

### Copiare i file

A partire dalla versione 0.9.7 di Azure PowerShell, è possibile copiare un file in un altro file, un file in un BLOB o un BLOB in un file. Di seguito viene illustrato come eseguire queste operazioni di copia mediante i cmdlet.

	# copy a file to the new directory
    Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx
    # copy a blob to a file directory
    Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx


## Montare la condivisione da una macchina virtuale di Azure che esegue Windows

Per illustrare come si monta una condivisione file di Azure, viene creata una macchina virtuale di Azure che esegue Windows e viene eseguito l'accesso remoto per montare la condivisione.

1. Creare innanzitutto una nuova macchina virtuale di Azure seguendo le istruzioni in [Creare una macchina virtuale che esegue Windows nel portale di anteprima di Azure](../virtual-machines-windows-tutorial.md).
2. Accedere quindi in remoto alla macchina virtuale seguendo le istruzioni in [Come accedere a una macchina virtuale che esegue Windows Server](../virtual-machines-log-on-windows-server.md).
3. Aprire una finestra di PowerShell nella macchina virtuale.

### Mantenere le credenziali dell'account di archiviazione per la macchina virtuale

Prima di eseguire il montaggio nella condivisione file, mantenere le credenziali dell'account di archiviazione nella macchina virtuale. Questo passaggio consente a Windows di riconnettere automaticamente la condivisione file quando la macchina virtuale viene riavviata. Per mantenere le credenziali dell'account, eseguire il comando `cmdkey` dalla finestra di PowerShell nella macchina virtuale. Sostituire `<storage-account-name>` con il nome dell'account di archiviazione e `<storage-account-key>` con la chiave dell'account di archiviazione.

	cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>

Windows esegue la riconnessione alla condivisione file quando la macchina virtuale viene riavviata. È possibile verificare che la condivisione sia stata riconnessa eseguendo il comando `net use` da una finestra di PowerShell.

### Montare la condivisione file usando le credenziali mantenute

Dopo aver stabilito una connessione remota alla macchina virtuale, è possibile eseguire il comando `net use` per montare la condivisione file usando la sintassi seguente. Sostituire `<storage-account-name>` con il nome dell'account di archiviazione e `<share-name>` con il nome della condivisione di archiviazione file.

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name>

	example :
	net use z: \\samples.file.core.windows.net\logs

Poiché sono state mantenute le credenziali dell'account di archiviazione nel passaggio precedente, non è necessario inserirle con il comando `net use`. Se le credenziali non sono state mantenute, inserirle come parametro passato al comando `net use`, come indicato nell'esempio seguente.

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name> /u:<storage-account-name> <storage-account-key>

	example :
	net use z: \\samples.file.core.windows.net\logs /u:samples <storage-account-key>

A questo punto è possibile usare la condivisione di archiviazione file dalla macchina virtuale come si farebbe con qualsiasi altra unità. È possibile eseguire i comandi file standard dal prompt dei comandi o visualizzare la condivisione montata e i relativi contenuti da Esplora file. È anche possibile eseguire il codice dalla macchina virtuale che accede alla condivisione file usando le API I/O del file Windows standard, ad esempio quelle fornite dagli [spazi dei nomi System.IO](http://msdn.microsoft.com/library/gg145019.aspx) in .NET Framework.

La condivisione file può essere montata anche da un ruolo in esecuzione in un servizio cloud di Azure eseguendo l'accesso remoto al ruolo.

## Creare un'applicazione locale per usare l'archiviazione file

È possibile montare una condivisione file da una macchina virtuale o da un servizio cloud in esecuzione in Azure, come mostrato precedentemente. Tuttavia, non è possibile montare una condivisione file da un'applicazione locale. Per accedere ai file della condivisione da un'applicazione locale è necessario usare l'API di archiviazione file. Questo esempio dimostra come usare una condivisione file mediante la [libreria client di archiviazione .NET di Azure](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409).

Per mostrare come usare l'API da un'applicazione locale, verrà creata un'applicazione console semplice in esecuzione sul desktop.

### Creare l'applicazione console e ottenere l'assembly

Per creare una nuova applicazione console in Visual Studio e installare il pacchetto NuGet di archiviazione Azure:

1. In Visual Studio scegliere **File > Nuovo progetto** e quindi **Windows > Applicazione console** dall'elenco di modelli di Visual C#.
2. Specificare un nome per l'applicazione console e quindi fare clic su **OK**.
3. Dopo aver creato il progetto, fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**. Cercare online "WindowsAzure.Storage" e fare clic su **Installa** per installare il pacchetto Archiviazione di Azure e le dipendenze.

### Salvare le credenziali dell'account di archiviazione nel file app.config

A questo punto salvare le credenziali nel file app.config del progetto. Modificare il file app.config in modo che somigli all'esempio seguente, sostituendo `myaccount` con il nome dell'account di archiviazione e `mykey` con la chiave dell'account di archiviazione.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

> [AZURE.NOTE]L'ultima versione dell'emulatore di archiviazione di Azure non supporta l'archiviazione file. La stringa di connessione deve usare un account di archiviazione di Azure come destinazione nel cloud con l'accesso all'anteprima dei file di archiviazione.


### Aggiungere le dichiarazioni dello spazio dei nomi

Aprire il file program.cs da Esplora soluzioni e aggiungere le dichiarazioni dello spazio dei nomi seguenti all'inizio del file.

	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.WindowsAzure.Storage.File;

### Recuperare la stringa di connessione a livello di programmazione

È possibile recuperare le credenziali salvate dal file app.config usando la classe `Microsoft.WindowsAzure.CloudConfigurationManager` o `System.Configuration.ConfigurationManager `. Il pacchetto Gestione configurazione di Microsoft Azure, che comprende la classe `Microsoft.WindowsAzure.CloudConfigurationManager`, è disponibile in [NuGet](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager).

L'esempio seguente illustra come recuperare le credenziali usando la classe `CloudConfigurationManager` e incapsularle con la classe `CloudStorageAccount`. Aggiungere il codice seguente al metodo `Main()` in program.cs.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString")); 

### Accedere alla condivisione file a livello di programmazione

A questo punto aggiungere il codice seguente nel metodo `Main()`, dopo il codice mostrato in precedenza, per recuperare la stringa di connessione. Questo codice ottiene un riferimento al file creato in precedenza e genera i contenuti nella finestra della console.

	// Create a CloudFileClient object for credentialed access to File storage.
	CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	// Get a reference to the file share we created previously.
	CloudFileShare share = fileClient.GetShareReference("logs");

	// Ensure that the share exists.
	if (share.Exists())
	{
	    // Get a reference to the root directory for the share.
	    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

	    // Get a reference to the directory we created previously.
	    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

	    // Ensure that the directory exists.
	    if (sampleDir.Exists())
	    {
	        // Get a reference to the file we created previously.
	        CloudFile file = sampleDir.GetFileReference("Log1.txt");

	        // Ensure that the file exists.
	        if (file.Exists())
	        {
	            // Write the contents of the file to the console window.
	            Console.WriteLine(file.DownloadTextAsync().Result);
	        }
	    }
	}

Eseguire l'applicazione console per visualizzare l'output.

## Impostare la dimensione massima per una condivisione file

A partire dalla versione 5.x della libreria del client di archiviazione di Azure, è possibile impostare la quota (o dimensione massima) per una condivisione file, in gigabyte. È anche possibile controllare la quantità di dati archiviata attualmente nella condivisione.

Impostando la quota per una condivisione, è possibile limitare la dimensione totale dei file archiviati nella condivisione. Se la dimensione totale dei file nella condivisione supera la quota impostata per la condivisione, i client non saranno in grado di aumentare le dimensioni dei file esistenti o creare nuovi file, a meno che tali file non siano vuoti.

L'esempio seguente illustra come controllare l'uso corrente per una condivisione e come impostare la quota per la condivisione.

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    // Ensure that the share exists.
    if (share.Exists())
    {
        // Check current usage stats for the share.
		// Note that the ShareStats object is part of the protocol layer for the File service.
        Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
        Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

        // Specify the maximum size of the share, in GB.
        // This line sets the quota to be 10 GB greater than the current usage of the share.
        share.Properties.Quota = 10 + stats.Usage;
        share.SetProperties();

        // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
        share.FetchAttributes();
        Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
    }

## Generare la firma di accesso condiviso per un file o una condivisione file

A partire dalla versione 5.x della Libreria del client di archiviazione di Azure, è possibile generare una firma di accesso condiviso (SAS) per una condivisione file o per un singolo file. È inoltre possibile creare un criterio di accesso condiviso in una condivisione file per gestire le firme di accesso condiviso. È consigliabile creare un criterio di accesso condiviso, in quanto fornisce un modo per revocare la firma SAS se necessario.

Nell'esempio seguente viene creato un criterio di accesso condiviso in una condivisione e quindi viene usato tale criterio per fornire i vincoli per una firma di accesso condiviso su un file della condivisione.

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    // Ensure that the share exists.
    if (share.Exists())
    {
        string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

        // Create a new shared access policy and define its constraints.
        SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
            };

        // Get existing permissions for the share.
        FileSharePermissions permissions = share.GetPermissions();

        // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        share.SetPermissions(permissions);

        // Generate a SAS for a file in the share and associate this access policy with it.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
        CloudFile file = sampleDir.GetFileReference("Log1.txt");
        string sasToken = file.GetSharedAccessSignature(null, policyName);
        Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

        // Create a new CloudFile object from the SAS, and write some text to the file.
        CloudFile fileSas = new CloudFile(fileSasUri);
        fileSas.UploadText("This write operation is authenticated via SAS.");
        Console.WriteLine(fileSas.DownloadText());
    }

Per altre informazioni sulla creazione e sull'uso di firme di accesso condiviso, vedere gli argomenti relativi alla [comprensione del modello di firma di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md) e alla [creazione e all'uso di una firma di accesso condiviso con il servizio BLOB](storage-dotnet-shared-access-signature-part-2.md).

## Copiare i file

A partire dalla versione 5.x della libreria del client di archiviazione di Azure, è possibile copiare un file in un altro file, un file in un BLOB o un BLOB in un file. Le sezioni seguenti illustrano come eseguire queste operazioni di copia a livello di programmazione.

È inoltre possibile utilizzare AzCopy per copiare un file in un altro o per copiare un blob in un file o viceversa. Per i dettagli sulla copia dei file con AzCopy, vedere l'argomento relativo all'[uso di AzCopy con Archiviazione di Microsoft Azure](storage-use-azcopy.md#copy-files-in-azure-file-storage-with-azcopy-preview-version-only).

> [AZURE.NOTE]Se si copia un BLOB in un file o un file in un BLOB, è necessario utilizzare una firma di accesso condiviso (SAS) per autenticare l'oggetto di origine, anche se si copia nello stesso account di archiviazione.

### Copiare un file in un altro file

Nell'esempio seguente viene copiato un file in un altro file nella stessa condivisione. Poiché questa operazione esegue la copia tra file nello stesso account di archiviazione, è possibile utilizzare l'autenticazione chiave condivisa per eseguire la copia.

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    // Ensure that the share exists.
    if (share.Exists())
    {
        // Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

        // Get a reference to the directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

        // Ensure that the directory exists.
        if (sampleDir.Exists())
        {
            // Get a reference to the file we created previously.
            CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

            // Ensure that the source file exists.
            if (sourceFile.Exists())
            {
                // Get a reference to the destination file.
                CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

                // Start the copy operation.
                destFile.StartCopy(sourceFile);

                // Write the contents of the destination file to the console window.
                Console.WriteLine(destFile.DownloadText());
            }
        }
    }


### Copiare un file in un BLOB

Nell'esempio seguente viene creato un file che viene copiato in un BLOB nello stesso account di archiviazione. Nell'esempio viene creata una firma di accesso condiviso per il file di origine, che il servizio utilizza per autenticare l'accesso al file di origine durante l'operazione di copia.

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Create a new file share, if it does not already exist.
    CloudFileShare share = fileClient.GetShareReference("sample-share");
    share.CreateIfNotExists();

    // Create a new file in the root directory.
    CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
    sourceFile.UploadText("A sample file in the root directory.");

    // Get a reference to the blob to which the file will be copied.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
    CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

    // Create a SAS for the file that's valid for 24 hours.
    // Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
    // to authenticate access to the source object, even if you are copying within the same
    // storage account.
    string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
    {
        // Only read permissions are required for the source file.
        Permissions = SharedAccessFilePermissions.Read,
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
    });

    // Construct the URI to the source file, including the SAS token.
    Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

    // Copy the file to the blob.
    destBlob.StartCopy(fileSasUri);

    // Write the contents of the file to the console window.
    Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
    Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());

È possibile copiare un BLOB in un file nello stesso modo. Se l'oggetto di origine è un BLOB, creare una firma di accesso condiviso per consentire l'accesso al BLOB durante l'operazione di copia.

## Utilizzare l'archiviazione di file con Linux

Per creare e gestire una condivisione file da Linux, utilizzare l’interfaccia della riga di comando di Azure. Per informazioni sull'uso dell'interfaccia della riga di comando di Azure con l'archiviazione di file, vedere [Utilizzo dell'interfaccia della riga di comando di Azure con archiviazione di Azure](storage-azure-cli.md#create-and-manage-file-shares).

È possibile montare una condivisione di file di Azure da una macchina virtuale che esegue Linux. Quando si crea una macchina virtuale di Azure, è possibile specificare un'immagine Linux che supporta SMB 2.1 dalla raccolta immagini di Azure, ad esempio l'ultima versione di Ubuntu. Tuttavia, in qualsiasi distribuzione di Linux che supporta SMB 2.1 si può montare una condivisione file di Azure.

Per altre informazioni su come montare una condivisione file di Azure su Linux, vedere l'articolo relativo all'[archiviazione condivisa in Linux mediante l'anteprima file di Azure - Parte 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1) su Channel 9.

## Passaggi successivi

Vedere i collegamenti seguenti per ulteriori informazioni sull'archiviazione file di Azure.

### Esercitazioni e riferimento

- [Informazioni di riferimento sulla libreria client di archiviazione per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
- [Riferimento API REST del servizio File](http://msdn.microsoft.com/library/azure/dn167006.aspx)
- [Usare AzCopy con Archiviazione di Microsoft Azure](storage-use-azcopy.md)
- [Uso di Azure PowerShell con Archiviazione di Azure](storage-powershell-guide-full.md)
- [Utilizzo dell'interfaccia della riga di comando di Azure con archiviazione di Azure](storage-azure-cli.md)

### Post di BLOG

- [Introduzione al servizio File di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Mantenimento delle connessioni ai file di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=September15_HO1-->