<properties urlDisplayName="File Service" pageTitle="Come usare l'archiviazione file di Azure | Microsoft Azure" metaKeywords="Get started Azure file  Azure file share  Azure file shares  Azure file   Azure file storage   Azure file .NET   Azure file C#   Azure file PowerShell" description="Informazioni su come usare l'archiviazione file di Microsoft Azure per creare condivisioni file e gestire il contenuto di file. Gli esempi sono scritti in PowerShell e C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="Come usare il servizio di archiviazione file di Microsoft Azure in .NET" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Come usare l'archiviazione file di Azure

In questa guida introduttiva vengono illustrate le nozioni di base sull'uso dell'archiviazione file di Microsoft Azure. Prima viene spiegato come usare PowerShell per creare una nuova condivisione file di Azure, aggiungere una directory, caricare un file locale nella condivisione ed elencare i file nella directory. Quindi viene illustrato come montare la condivisione file da una macchina virtuale di Azure, con la stessa procedura usata per una condivisione SMB.

Agli utenti che vogliono accedere ai file in una condivisione da un'applicazione locale, da una macchina virtuale o da un servizio cloud di Azure viene spiegato come usare la libreria client di archiviazione .NET di Azure insieme alla condivisione file da un'applicazione desktop.

> [WACOM.NOTE] L'esecuzione di esempi di codice .NET in questa guida richiede la libreria client di archiviazione .NET di Azure 4.x o versione successiva. La libreria client di archiviazione è disponibile in [NuGet][NuGet].

## Sommario

-   [Che cos'è l'archiviazione file?][Che cos'è l'archiviazione file?]
-   [Concetti relativi all'archiviazione file][Concetti relativi all'archiviazione file]
-   [Creare un account di archiviazione di Azure][Creare un account di archiviazione di Azure]
-   [Usare PowerShell per creare una condivisione file][Usare PowerShell per creare una condivisione file]
-   [Montare la condivisione da una macchina virtuale di Azure][Montare la condivisione da una macchina virtuale di Azure]
-   [Creare un'applicazione locale per accedere all'archiviazione file][Creare un'applicazione locale per accedere all'archiviazione file]
-   [Passaggi successivi][Passaggi successivi]

## <a name="what-is-file-storage"></a><span class="short-header">Che cos'è l'archiviazione file di Azure?</span>Che cos'è l'archiviazione file di Azure?

L'archiviazione file offre un'archiviazione condivisa per le applicazioni che usano il protocollo SMB 2.1 standard. Le macchine virtuali e i servizi cloud di Microsoft Azure possono condividere dati file tra componenti delle applicazioni tramite le condivisioni montate e le applicazioni locali possono accedere ai dati file in una condivisione tramite l'API dell'archiviazione file.

Le applicazioni in esecuzione nelle macchine virtuali o nei servizi cloud di Azure possono montare una condivisione di archiviazione file per accedere a dati file, come se si montasse una condivisione SMB tipica per un'applicazione desktop. Non ci sono limiti per le macchine virtuali o i servizi cloud di Azure che possono montare e accedere contemporaneamente alla condivisione di archiviazione file.

Poiché una condivisione di archiviazione file è una condivisione file SMB 2.1 standard, le applicazioni in esecuzione in Azure possono accedere ai dati nella condivisione tramite le API di I/O del file. Gli sviluppatori possono quindi riusare il codice esistente e le competenze acquisite per eseguire la migrazione delle applicazioni esistenti. I professionisti IT possono usare i cmdlet di PowerShell per creare, montare e gestire condivisioni di archiviazione file nell'ambito delle attività di amministrazione per le applicazioni Azure. In questa guida vengono illustrati esempi per entrambi i casi.

Di seguito sono riportati gli usi più comuni per il servizio di archiviazione file:

-   Migrazione di applicazioni locali basata sui file di condivisione per l'esecuzione in macchine virtuali o servizi cloud di Azure, senza costi di riscrittura
-   Archiviazione di impostazioni delle applicazioni condivise, ad esempio nei file di configurazione
-   Archiviazione di dati di diagnostica, ad esempio log, metriche e dump di arresto anomalo del sistema, in un percorso condiviso
-   Archiviazione di strumenti e utilità necessari per lo sviluppo o la gestione di macchine virtuali o servizi cloud di Azure

## <a name="file-storage-concepts"></a><span class="short-header">Concetti relativi all'archiviazione file</span>Concetti relativi all'archiviazione file

L'archiviazione file contiene i seguenti componenti:

![files-concepts][files-concepts]

-   **Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito
     esclusivamente tramite un account di archiviazione. Per informazioni sulla capacità dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure][Obiettivi di scalabilità e prestazioni per Archiviazione di Azure].

-   **Condivisione:** una condivisione di archiviazione file è una condivisione file SMB 2.1 di Azure.
    Tutte le directory e i file devono essere creati in una condivisione padre. Un account può contenere un
    numero illimitato di condivisioni e una condivisione può archiviare un numero illimitato
    di file, fino ai limiti di capacità dell'account di archiviazione.

-   **Directory:** una gerarchia di directory facoltativa.

-   **File:** un file nella condivisione. Le dimensioni massime di un file possono estendersi fino a 1 TB.

-   **Formato dell'URL:** i file sono indirizzabili con il seguente formato
    di URL:
    https://`<storage account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`

    L'URL di esempio seguente può essere usato per indirizzare uno dei file nel
    diagramma precedente:
    `http://acmecorp.file.core.windows.net/cloudfiles/diagnostics/log.txt`

Per dettagli su come denominare condivisioni, directory e file, vedere [Denominazione e riferimento a condivisioni, directory, file e metadati][Denominazione e riferimento a condivisioni, directory, file e metadati].

## <a name="create-account"></a><span class="short-header">Creare un account di archiviazione di Azure</span>Creare un account di archiviazione di Azure

L'archiviazione file di Azure è attualmente in anteprima. Per richiedere l'accesso all'anteprima, andare alla [pagina di anteprima di Microsoft Azure][pagina di anteprima di Microsoft Azure] e richiedere l'accesso ai **file di Azure**. Dopo l'approvazione della richiesta viene inviato un avviso che indica che è possibile accedere all'anteprima dell'archiviazione file. È quindi possibile creare un account di archiviazione per l'accesso all'archiviazione file.

> [WACOM.NOTE] L'archiviazione file è attualmente disponibile solo per i nuovi account di archiviazione. Dopo aver ottenuto l'accesso all'archiviazione file per la sottoscrizione, creare un nuovo account di archiviazione da usare con questa guida.

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="use-cmdlets"></a><span class="short-header">Usare PowerShell per creare una condivisione file</span>Usare PowerShell per creare una condivisione file

### Installare i cmdlet di PowerShell per l'archiviazione di Azure

Per prepararsi all'uso di PowerShell, scaricare e installare i cmdlet di Azure PowerShell. Vedere [Come installare e configurare Azure PowerShell][Come installare e configurare Azure PowerShell] per conoscere il punto e le istruzioni di installazione.

> [WACOM.NOTE] I cmdlet di PowerShell per il servizio file sono disponibili solo nell'ultimo modulo di Azure PowerShell, versione 0.8.5 o successive. Si consiglia di scaricare e installare oppure di aggiornare il modulo alla versione di Azure PowerShell più recente.

Aprire una finestra di Azure PowerShell facendo clic su **Start** e digitando **Windows Azure PowerShell**. La finestra di Azure PowerShell carica automaticamente il modulo di Azure PowerShell.

### Creare un contesto per l'account e la chiave di archiviazione

A questo punto, creare il contesto dell'account di archiviazione. Il contesto incapsula il nome e la chiave dell'account. Sostituire `account-name` e `account-key` con il nome e la chiave dell'account nel seguente esempio:

    # create a context for account and key
    $ctx=New-AzureStorageContext account-name account-key

### Creare una nuova condivisione file

Creare quindi la nuova condivisione, denominata `sampleshare` in questo esempio:

    # create a new share
    $s = New-AzureStorageShare sampleshare -Context $ctx

A questo punto si ha una condivisione file nell'archiviazione file. Vengono quindi aggiunti un file e una directory.

### Creare una directory nella condivisione file

Procedere creando una directory nella condivisione. Nel seguente esempio la directory viene denominata `sampledir`:

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path sampledir

### Caricare un file locale nella directory

Caricare un file locale nella directory. Nel seguente esempio viene caricato un file da `C:\temp\samplefile.txt`. Modificare il percorso del file in modo che punti a un file valido nel computer locale:

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\samplefile.txt -Path sampledir

### Elencare i file nella directory

Per visualizzare un file nella directory, è possibile elencare i file della directory. Questo comando elenca anche le sottodirectory, ma in questo esempio non ci sono sottodirectory, quindi viene elencato solo il file.

    # list files in the new directory
    Get-AzureStorageFile -Share $s -Path sampledir

## <a name="mount-share"></a><span class="short-header">Montare la condivisione da una macchina virtuale di Azure</span>Montare la condivisione da una macchina virtuale di Azure

Per mostrare come montare una condivisione file di Azure viene creata una macchina virtuale di Azure e viene eseguito l'accesso remoto per montare la condivisione.

1.  Prima di tutto creare una nuova macchina virtuale di Azure seguendo le istruzioni in [Creare una macchina virtuale che esegue Windows Server][Creare una macchina virtuale che esegue Windows Server].
2.  Quindi, accedere in remoto alla macchina virtuale seguendo le istruzioni in [Come accedere a una macchina virtuale che esegue Windows Server][Come accedere a una macchina virtuale che esegue Windows Server].
3.  Aprire una finestra di PowerShell nella macchina virtuale.

### Mantenere le credenziali dell'account di archiviazione per la macchina virtuale

Prima di eseguire il montaggio nella condivisione file, mantenere le credenziali dell'account di archiviazione nella macchina virtuale. Questo passaggio consente a Windows di riconnettere automaticamente la condivisione file quando la macchina virtuale viene riavviata. Per mantenere le credenziali dell'account, eseguire il comando `cmdkey` dalla finestra di PowerShell nella macchina virtuale. Sostituire `<storage-account>` con il nome dell'account di archiviazione e `<account-key>` con la chiave dell'account di archiviazione:

    cmdkey /add:<storage-account>.file.core.windows.net /user:<storage-account> /pass:<account-key>

Windows esegue la riconnessione alla condivisione file quando la macchina virtuale viene riavviata. È possibile verificare che la condivisione sia stata riconnessa eseguendo il comando `net use` da una finestra di PowerShell.

### Montare la condivisione file usando le credenziali mantenute

Dopo aver stabilito una connessione remota alla macchina virtuale, è possibile eseguire il comando `net use` per montare la condivisione file usando la seguente sintassi. Sostituire `<storage-account>` con il nome dell'account di archiviazione e `<share-name>` con il nome della condivisione di archiviazione file.

    net use z: \<storage-account>.file.core.windows.netnet use z: \\<storage-account>.file.core.windows.net\<share-name>lt;share-name>

> [WACOM.NOTE] Poiché sono state mantenute le credenziali dell'account di archiviazione nel passaggio precedente, non è necessario inserirle con il comando `net use`. Se le credenziali non sono state mantenute, inserirle come parametro passato al comando `net use`. Sostituire `<storage-account>` con il nome dell'account di archiviazione, `<share-name>` con il nome della condivisione di archiviazione file e `<account-key>` con la chiave dell'account di archiviazione:

    net use z: \<storage-account>.file.core.windows.netnet use z: \\<storage-account>.file.core.windows.net\<share-name> /u:<storage-account> <account-key>lt;share-name> /u:<storage-account> <account-key>

A questo punto è possibile usare la condivisione di archiviazione file dalla macchina virtuale come si farebbe con qualsiasi altra unità. È possibile eseguire i comandi file standard dal prompt dei comandi o visualizzare la condivisione montata e i relativi contenuti da Esplora file. È anche possibile eseguire il codice dalla macchina virtuale che accede alla condivisione file usando le API I/O del file Windows standard, ad esempio quelle fornite dagli [spazi dei nomi System.IO][spazi dei nomi System.IO] in .NET Framework.

La condivisione file può essere montata anche da un ruolo in esecuzione in un servizio cloud di Azure eseguendo l'accesso remoto al ruolo.

## <a name="create-console-app"></a><span class="short-header">Creare un'applicazione locale per l'uso dell'archiviazione file</span>Creare un'applicazione locale per l'uso dell'archiviazione file

È possibile montare una condivisione di archiviazione file da una macchina virtuale o da un servizio cloud in esecuzione in Azure, come mostrato precedentemente. Tuttavia, non è possibile montare una condivisione di archiviazione file da un'applicazione locale. Per accedere ai file della condivisione da un'applicazione locale è necessario usare l'API di archiviazione file. Questo esempio dimostra come usare una condivisione file mediante la [libreria client di archiviazione .NET di Azure][libreria client di archiviazione .NET di Azure].

Per mostrare come usare l'API da un'applicazione locale, viene creata un'applicazione console semplice in esecuzione sul desktop.

### Creare l'applicazione console e ottenere l'assembly

Per creare una nuova applicazione console in Visual Studio e installare il pacchetto NuGet di archiviazione Azure:

1.  In Visual Studio scegliere **File** -\> **Nuovo progetto**, quindi **Windows** -\> **Applicazione console** dall'elenco di modelli di Visual C#.
2.  Inserire un nome per l'applicazione console, quindi fare clic su **OK**.
3.  Dopo aver creato il progetto, fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**. Cercare online "WindowsAzure.Storage" e fare clic su **Installa** per installare il pacchetto Archiviazione di Azure e le dipendenze.

### Salvare le credenziali dell'account di archiviazione nel file app.config

A questo punto salvare le credenziali nel file app.config del progetto. Modificare il file app.config in modo che somigli all'esempio seguente, sostituendo `myaccount` con il nome dell'account di archiviazione e `mykey` con la chiave dell'account di archiviazione:

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <startup> 
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
        </startup>
        <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey" />
        </appSettings>
    </configuration>

> [WACOM.NOTE] L'ultima versione dell'emulatore di archiviazione di Azure non supporta l'archiviazione file. La stringa di connessione deve usare un account di archiviazione di Azure come destinazione nel cloud con l'accesso all'anteprima dei file.

### Aggiungere le dichiarazioni dello spazio dei nomi

Aprire il file program.cs da Esplora soluzioni e aggiungere le seguenti dichiarazioni dello spazio dei nomi all'inizio del file:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.File;

### Recuperare la stringa di connessione a livello di codice

È possibile recuperare le credenziali salvate dal file app.config usando la classe `Microsoft.WindowsAzure.CloudConfigurationManager` o `System.Configuration.ConfigurationManager`. Nell'esempio seguente viene illustrato come recuperare le credenziali usando la classe `CloudConfigurationManager` e incapsularle con la classe `CloudStorageAccount`. Aggiungere il codice seguente al metodo `Main()` in program.cs:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

### Accedere alla condivisione di archiviazione file a livello di codice

A questo punto aggiungere il seguente codice nel metodo `Main()`, dopo il codice mostrato in precedenza, per recuperare la stringa di connessione. Questo codice ottiene un riferimento al file creato in precedenza e genera i contenuti nella finestra della console.

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("sampleshare");

    //Ensure that the share exists.
    if (share.Exists())
    {
        //Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

        //Get a reference to the sampledir directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("sampledir");

        //Ensure that the directory exists.
        if (sampleDir.Exists())
        {
            //Get a reference to the file we created previously.
            CloudFile file = sampleDir.GetFileReference("samplefile.txt");

            //Ensure that the file exists.
            if (file.Exists())
            {
                //Write the contents of the file to the console window.
                Console.WriteLine(file.DownloadTextAsync().Result);
            }
        }
    }

Eseguire l'applicazione console per visualizzare l'output.

## <a name="next-steps"></a><span class="short-header">Passaggi successivi</span>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione file, seguire questi collegamenti
per informazioni più dettagliate.

-   Visualizzare la documentazione di riferimento del servizio file per informazioni complete sulle API disponibili:
    -   [Informazioni di riferimento sulla libreria client di archiviazione per .NET][libreria client di archiviazione .NET di Azure]
    -   [Informazioni di riferimento sull'API REST del servizio file][Informazioni di riferimento sull'API REST del servizio file]

-   Visualizzare i post del team di Archiviazione di Azure relativi al servizio file:
    -   [Introduzione al servizio file di Microsoft Azure][Introduzione al servizio file di Microsoft Azure]
    -   [Mantenimento delle connessioni ai file di Microsoft Azure][Mantenimento delle connessioni ai file di Microsoft Azure]

-   Per altre opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.
    -   Per archiviare dati non strutturati, usare [Archiviazione BLOB][Archiviazione BLOB].
    -   Per archiviare dati strutturati, usare [Archiviazione tabelle][Archiviazione tabelle].
    -   Usare [Archiviazione di accodamento][Archiviazione di accodamento] per archiviare i messaggi in modo affidabile.
    -   Per archiviare dati relazionali, usare [Database SQL][Database SQL].

</p>

  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Che cos'è l'archiviazione file?]: #what-is-file-storage
  [Concetti relativi all'archiviazione file]: #file-storage-concepts
  [Creare un account di archiviazione di Azure]: #create-account
  [Usare PowerShell per creare una condivisione file]: #use-cmdlets
  [Montare la condivisione da una macchina virtuale di Azure]: #mount-share
  [Creare un'applicazione locale per accedere all'archiviazione file]: #create-console-app
  [Passaggi successivi]: #next-steps
  [files-concepts]: ./media/storage-dotnet-how-to-use-files/files-concepts.png
  [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure]: http://msdn.microsoft.com/it-it/library/dn249410.aspx
  [Denominazione e riferimento a condivisioni, directory, file e metadati]: http://msdn.microsoft.com/it-it/library/azure/dn167011.aspx
  [pagina di anteprima di Microsoft Azure]: /it-it/services/preview/
  [Come installare e configurare Azure PowerShell]: /it-it/documentation/articles/install-configure-powershell/
  [Creare una macchina virtuale che esegue Windows Server]: /it-it/documentation/articles/virtual-machines-windows-tutorial/
  [Come accedere a una macchina virtuale che esegue Windows Server]: /it-it/documentation/articles/virtual-machines-log-on-windows-server/
  [spazi dei nomi System.IO]: http://msdn.microsoft.com/it-it/library/gg145019(v=vs.110).aspx
  [libreria client di archiviazione .NET di Azure]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Informazioni di riferimento sull'API REST del servizio file]: http://msdn.microsoft.com/it-it/library/azure/dn167006.aspx
  [Introduzione al servizio file di Microsoft Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx
  [Mantenimento delle connessioni ai file di Microsoft Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx
  [Archiviazione BLOB]: /it-it/documentation/articles/storage-dotnet-how-to-use-blobs/
  [Archiviazione tabelle]: /it-it/documentation/articles/storage-dotnet-how-to-use-tables/
  [Archiviazione di accodamento]: /it-it/documentation/articles/storage-dotnet-how-to-use-queues/
  [Database SQL]: /it-it/documentation/articles/sql-database-dotnet-how-to-use/
