<properties
			pageTitle="Come usare l'archiviazione file di Azure con PowerShell e .NET | Microsoft Azure"
            description="Informazioni su come usare l'archiviazione file di Azure per creare condivisioni file nel cloud e gestire il contenuto di file. Archiviazione file consente alle aziende di spostare le applicazioni dipendenti da condivisioni file SMB in Azure. Gli esempi sono scritti in PowerShell e C#."
            services="storage"
            documentationCenter=".net"
            authors="tamram"
            manager="adinah"
            editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="dotnet"
      ms.topic="hero-article"
      ms.date="06/22/2015"
      ms.author="tamram" />

# Come usare l'archiviazione file di Azure con PowerShell e .NET

## Panoramica

In questa guida introduttiva vengono illustrate le nozioni di base sull'uso dell'archiviazione file di Microsoft Azure. In questa esercitazione si apprenderà come:

- Usare PowerShell per visualizzare le informazioni per creare una nuova condivisione file di Azure, aggiungere una directory, caricare un file locale nella condivisione ed elencare i file nella directory.
- Montare la condivisione file da una macchina virtuale di Azure, con la stessa procedura usata per una condivisione SMB.

Agli utenti che desiderano accedere ai file in una condivisione da un'applicazione locale, da una macchina virtuale o da un servizio cloud di Azure viene spiegato come usare la libreria client di archiviazione .NET di Azure insieme alla condivisione file da un'applicazione desktop.

> [AZURE.NOTE]L'esecuzione degli esempi di codice .NET presenti in questa guida richiede la libreria client di archiviazione .NET di Azure 4.x o versione successiva. La libreria client di archiviazione è disponibile in [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/).

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]


## Creare un account di archiviazione di Azure

L'archiviazione file di Azure è attualmente in anteprima. Per richiedere l'accesso all'anteprima, andare alla [pagina di anteprima di Microsoft Azure](/services/preview/) e richiedere l'accesso ai **file di Azure**. Dopo l'approvazione della richiesta viene inviato un avviso che indica che è possibile accedere all'anteprima dell'archiviazione file. È quindi possibile creare un account di archiviazione per l'accesso all'archiviazione file.

> [AZURE.NOTE]L'archiviazione file è attualmente disponibile solo per i nuovi account di archiviazione. Dopo aver ottenuto l'accesso all'archiviazione file per la sottoscrizione, creare un nuovo account di archiviazione da usare con questa guida.
>
> L'archiviazione file di Azure attualmente non supporta le firme di accesso condiviso.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Usare PowerShell per creare una condivisione file

A questo punto si utilizzerà PowerShell per creare una condivisione file. Dopo che la condivisione file è stata creata, è possibile montarla da qualsiasi file system che supporti SMB 2.1.

### Installare i cmdlet di PowerShell per l'archiviazione di Azure

Per prepararsi all'uso di PowerShell, scaricare e installare i cmdlet di Azure PowerShell. Vedere [Come installare e configurare Azure PowerShell](../install-configure-powershell.md) per le istruzioni relative al punto di installazione e all’installazione.

> [AZURE.NOTE]I cmdlet di PowerShell per il servizio file sono disponibili solo nell'ultimo modulo di Azure PowerShell, versione 0.8.5 o successive. Si consiglia di scaricare e installare oppure aggiornare il modulo alla versione di Azure PowerShell più recente.

Aprire una finestra di Azure PowerShell facendo clic su **Start** e digitando **Azure PowerShell**. La finestra di Azure PowerShell carica automaticamente il modulo di Azure PowerShell.

### Creare un contesto per l'account e la chiave di archiviazione

A questo punto, creare il contesto dell'account di archiviazione. Il contesto incapsula il nome e la chiave dell'account di archiviazione. Per informazioni sulla copia della chiave dell’account dal portale di Azure, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

Sostituire `storage-account-name` e `storage-account-key` con il nome e la chiave dell'account di archiviazione nel seguente esempio:

	# create a context for account and key
	$ctx=New-AzureStorageContext storage-account-name storage-account-key

### Creare una nuova condivisione file

Creare quindi la nuova condivisione, denominata `logs` in questo esempio:

	# create a new share
	$s = New-AzureStorageShare logs -Context $ctx

A questo punto si ha una condivisione file nell'archiviazione file. Vengono quindi aggiunti un file e una directory.

> [AZURE.IMPORTANT]Il nome della condivisione file deve essere composto solo da caratteri minuscoli. Per dettagli su come denominare condivisioni e file, vedere [Denominazione e riferimento a condivisioni, directory, file e metadati](https://msdn.microsoft.com/library/azure/dn167011.aspx).

### Creare una directory nella condivisione file

A questo punto, si crea una directory nella condivisione. Nel seguente esempio, la directory viene denominata `CustomLogs`:

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path CustomLogs

### Caricare un file locale nella directory

Caricare un file locale nella directory. Nel seguente esempio viene caricato un file da `C:\temp\Log1.txt`. Modificare il percorso del file in modo che punti a un file valido nel computer locale:

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs

### Elencare i file nella directory

Per visualizzare un file nella directory, è possibile elencare i file della directory. Questo comando elenca anche le sottodirectory, ma in questo esempio non ci sono sottodirectory, quindi viene elencato solo il file.

	# list files in the new directory
	Get-AzureStorageFile -Share $s -Path CustomLogs

## Montare la condivisione da una macchina virtuale di Azure che esegue Windows

Per illustrare come si monta una condivisione file di Azure, viene creata una macchina virtuale di Azure che esegue Windows e viene eseguito l'accesso remoto per montare la condivisione.

1. Prima di tutto creare una nuova macchina virtuale di Azure seguendo le istruzioni in [Creare una macchina virtuale che esegue Windows Server](../virtual-machines-windows-tutorial.md).
2. Quindi, accedere in remoto alla macchina virtuale seguendo le istruzioni in [Come accedere a una macchina virtuale che esegue Windows Server](../virtual-machines-log-on-windows-server.md).
3. Aprire una finestra di PowerShell nella macchina virtuale.

### Mantenere le credenziali dell'account di archiviazione per la macchina virtuale

Prima di eseguire il montaggio nella condivisione file, mantenere le credenziali dell'account di archiviazione nella macchina virtuale. Questo passaggio consente a Windows di riconnettere automaticamente la condivisione file quando la macchina virtuale viene riavviata. Per mantenere le credenziali dell'account, eseguire il comando `cmdkey` dalla finestra di PowerShell nella macchina virtuale. Sostituire `<storage-account-name>` con il nome dell'account di archiviazione e `<storage-account-key>` con la chiave dell'account di archiviazione:

	cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>

Windows esegue la riconnessione alla condivisione file quando la macchina virtuale viene riavviata. È possibile verificare che la condivisione sia stata riconnessa eseguendo il comando `net use` da una finestra di PowerShell.

### Montare la condivisione file usando le credenziali mantenute

Dopo aver stabilito una connessione remota alla macchina virtuale, è possibile eseguire il comando `net use` per montare la condivisione file usando la seguente sintassi. Sostituire `<storage-account-name>` con il nome dell'account di archiviazione e `<share-name>` con il nome della condivisione di archiviazione file.

    net use <drive-letter>: <storage-account-name>.file.core.windows.net<share-name>

	example :
	net use z: \samples.file.core.windows.net\logs

> [AZURE.NOTE]Poiché sono state mantenute le credenziali dell'account di archiviazione nel passaggio precedente, non è necessario inserirle con il comando `net use`. Se le credenziali non sono state mantenute, inserirle come parametro passato al comando `net use`.

    net use <drive-letter>: <storage-account-name>.file.core.windows.net<share-name> /u:<storage-account-name> <storage-account-key>

	example :
	net use z: \samples.file.core.windows.net\logs /u:samples <storage-account-key>

A questo punto è possibile usare la condivisione di archiviazione file dalla macchina virtuale come si farebbe con qualsiasi altra unità. È possibile eseguire i comandi file standard dal prompt dei comandi o visualizzare la condivisione montata e i relativi contenuti da Esplora file. È anche possibile eseguire il codice dalla macchina virtuale che accede alla condivisione file usando le API I/O del file Windows standard, ad esempio quelle fornite dagli spazi dei nomi [System.IO] (http://msdn.microsoft.com/library/gg145019(v=vs.110).aspx) in .NET Framework.

La condivisione file può essere montata anche da un ruolo in esecuzione in un servizio cloud di Azure eseguendo l'accesso remoto al ruolo.

## Creare un'applicazione locale per usare l'archiviazione file

È possibile montare una condivisione file da una macchina virtuale o da un servizio cloud in esecuzione in Azure, come mostrato precedentemente. Tuttavia, non è possibile montare una condivisione file da un'applicazione locale. Per accedere ai file della condivisione da un'applicazione locale è necessario usare l'API di archiviazione file. Questo esempio dimostra come usare una condivisione file mediante la [libreria client di archiviazione .NET di Azure](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409).

Per mostrare come usare l'API da un'applicazione locale, verrà creata un'applicazione console semplice in esecuzione sul desktop.

### Creare l'applicazione console e ottenere l'assembly

Per creare una nuova applicazione console in Visual Studio e installare il pacchetto NuGet di archiviazione Azure:

1. In Visual Studio scegliere **File** -> **Nuovo progetto**, quindi **Windows** -> **Applicazione console** dall'elenco di modelli di Visual C#.
2. Inserire un nome per l'applicazione console, quindi fare clic su **OK**.
3. Dopo aver creato il progetto, fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**. Cercare online "WindowsAzure.Storage" e fare clic su **Installa** per installare il pacchetto Archiviazione di Azure e le dipendenze.

### Salvare le credenziali dell'account di archiviazione nel file app.config

A questo punto salvare le credenziali nel file app.config del progetto. Modificare il file app.config in modo che somigli all'esempio seguente, sostituendo `myaccount` con il nome dell'account di archiviazione e `mykey` con la chiave dell'account di archiviazione:

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

> [AZURE.NOTE]L'ultima versione dell'emulatore di archiviazione di Azure non supporta l'archiviazione file. La stringa di connessione deve usare un account di archiviazione di Azure come destinazione nel cloud con l'accesso all'anteprima dei file.


### Aggiungere le dichiarazioni dello spazio dei nomi

Aprire il file program.cs da Esplora soluzioni e aggiungere le seguenti dichiarazioni dello spazio dei nomi all'inizio del file:

	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.File;

### Recuperare la stringa di connessione a livello di programmazione

È possibile recuperare le credenziali salvate dal file app.config usando la classe `Microsoft.WindowsAzure.CloudConfigurationManager` o `System.Configuration.ConfigurationManager `. L'esempio seguente illustra come recuperare le credenziali usando la classe `CloudConfigurationManager` e incapsularle con la classe `CloudStorageAccount`. Aggiungere il codice seguente al metodo `Main()` in program.cs:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString"));

### Accedere alla condivisione di archiviazione file a livello di programmazione

A questo punto aggiungere il seguente codice nel metodo `Main()`, dopo il codice mostrato in precedenza, per recuperare la stringa di connessione. Questo codice ottiene un riferimento al file creato in precedenza e genera i contenuti nella finestra della console.

	//Create a CloudFileClient object for credentialed access to File storage.
	CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	//Get a reference to the file share we created previously.
	CloudFileShare share = fileClient.GetShareReference("logs");

	//Ensure that the share exists.
	if (share.Exists())
	{
	    //Get a reference to the root directory for the share.
	    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

	    //Get a reference to the directory we created previously.
	    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

	    //Ensure that the directory exists.
	    if (sampleDir.Exists())
	    {
	        //Get a reference to the file we created previously.
	        CloudFile file = sampleDir.GetFileReference("Log1.txt");

	        //Ensure that the file exists.
	        if (file.Exists())
	        {
	            //Write the contents of the file to the console window.
	            Console.WriteLine(file.DownloadTextAsync().Result);
	        }
	    }
	}

Eseguire l'applicazione console per visualizzare l'output.

## Montare la condivisione da una macchina virtuale di Azure che esegue Linux

Quando si crea una macchina virtuale di Azure è possibile specificare un'immagine Ubuntu dalla raccolta delle immagini del disco per garantire il supporto per SMB 2.1. Tuttavia, qualsiasi distribuzione Linux che supporti SMB 2.1 non può montare una condivisione file di Azure.

Per una dimostrazione di come montare una condivisione file di Azure su Linux, vedere [Archiviazione condivisa in Linux mediante l'anteprima file di Azure -- Parte 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1) su Channel 9.

## Passaggi successivi

Vedere i collegamenti seguenti per ulteriori informazioni sull'archiviazione file di Azure.

### Riferimento

- [Informazioni di riferimento sulla libreria client di archiviazione per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
- [Riferimento API REST del servizio File](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### Post di BLOG

- [Introduzione al servizio File di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Mantenimento delle connessioni ai file di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
 

<!---HONumber=62-->