---

title: Preparing hard drives for an import job (Preparazione dei dischi rigidi per il processo di importazione) | Microsoft Docs
description: "Informazioni su come preparare uno o più dischi rigidi per il processo di importazione per il servizio di importazione/esportazione di Microsoft Azure"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 3d818245-8b1b-4435-a41f-8e5ec1f194b2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: d4894b4168a524d2378048f2da091298fb010d67
ms.openlocfilehash: ec7a6df6a39cd1ee7a452439e3580496cfb98adc


---

# <a name="preparing-hard-drives-for-an-import-job"></a>Preparing hard drives for an import job (Preparazione dei dischi rigidi per il processo di importazione)
Per preparare uno o più dischi rigidi per il processo di importazione, seguire questi passaggi:

-   identificare i dati da importare nel servizio BLOB

-   identificare le directory virtuali di destinazione e i BLOB nel servizio BLOB

-   determinare il numero di unità necessarie

-   copiare i dati su ogni disco rigido

 Per un flusso di lavoro di esempio vedere [Sample workflow to prepare hard drives for an import job](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md) (Flusso di lavoro di esempio per preparare i dischi rigidi al processo di importazione).

##  <a name="a-nameprepareharddrivesidentifythedatatobeimporteda-identify-the-data-to-be-imported"></a><a name="PrepareHardDrives_IdentifytheDatatoBeImported"></a> Identificare i dati da importare
 Il primo passaggio per creare un processo di importazione è determinare le directory e i file da importare. Può trattarsi di un elenco di directory, un elenco di file univoci o una combinazione di entrambi. Quando viene inclusa una directory, tutti i file nella directory e nelle relative sottodirectory faranno parte del processo di importazione.

> [!NOTE]
>  Poiché le sottodirectory sono incluse in modo ricorsivo quando viene inclusa una directory principale, specificare solo la directory principale. Non è necessario specificare anche le relative sottodirectory.
>
>  Attualmente, lo strumento di importazione/esportazione di Microsoft Azure presenta le limitazioni seguenti: se una directory contiene più dati di quelli che può contenere un disco rigido, la directory deve essere suddivisa in directory più piccole. Ad esempio, se una directory contiene 2,5 TB di dati e la capacità del disco rigido è pari a 2 TB, è necessario suddividere la directory da 2,5 TB in directory più piccole. Questa limitazione verrà risolta in una versione successiva dello strumento.

##  <a name="a-nameprepareharddrivesidentifythedestinationlocationsintheblobservicea-identify-the-destination-locations-in-the-blob-service"></a><a name="PrepareHardDrives_IdentifytheDestinationLocationsintheBlobService"></a> Identificare i percorsi di destinazione nel servizio BLOB
 Per ogni directory o file che verrà importato, è necessario identificare una directory virtuale di destinazione o un BLOB nel servizio BLOB di Azure. Usare queste destinazioni come input per lo strumento di importazione/esportazione di Azure. Si noti che le directory devono essere delimitate con il carattere barra "/".

 La tabella seguente illustra alcuni esempi di destinazioni BLOB:

|File o directory di origine|BLOB o directory virtuale di destinazione|
|------------------------------|-------------------------------------------|
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|
|K:\Temp\FavoriteVideo.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https://mystorageaccount.blob.core.windows.net/music|

##  <a name="a-nameprepareharddrivesdeterminehowmanydrivesareneededa-determine-how-many-drives-are-needed"></a><a name="PrepareHardDrives_DetermineHowManyDrivesAreNeeded"></a> Determinare il numero di unità necessarie
 Successivamente, è necessario determinare:

-   Il numero di unità disco rigido necessarie per archiviare i dati.

-   Le directory e/o i file autonomi che verranno copiati in ciascun disco rigido.

 Assicurarsi di avere il numero di dischi rigidi necessari per archiviare i dati da trasferire.

##  <a name="a-nameprepareharddrivescopydatatoasingleharddrivea-copy-data-to-your-hard-drive"></a><a name="PrepareHardDrives_CopyDatatoaSingleHardDrive"></a> Copiare i dati sul disco rigido
 Questa sezione descrive come chiamare lo strumento di importazione/esportazione di Azure per copiare i dati su uno o più dischi rigidi. Ogni volta che si chiama lo strumento di importazione/esportazione di Azure, si crea una nuova *sessione di copia*. Creare almeno una sessione di copia per ogni unità su cui si copiano dati; in alcuni casi, potrebbe essere necessaria più di una sessione di copia per copiare tutti i dati su una sola unità. Ecco alcuni motivi per cui potrebbero essere necessarie più sessioni di copia:

-   È necessario creare una sessione di copia separata per ogni unità su cui si copia.

-   Una sessione di copia può copiare sull'unità una sola directory o un solo BLOB. Se si copiano più directory, più BLOB o una combinazione di entrambi, è necessario creare più sessioni di copia.

-   È possibile specificare le proprietà e i metadati che verranno impostati nei BLOB importati come parte del processo di importazione. Le proprietà o i metadati specificati per una sessione di copia verranno applicati a tutti i BLOB specificati in quella sessione di copia. Se si desidera specificare altre proprietà o metadati per alcuni BLOB, è necessario creare una sessione di copia separata. Per maggiori informazioni, vedere [Setting Properties and Metadata during the import process](storage-import-export-tool-setting-properties-metadata-import-v1.md) (Impostazione di proprietà e metadati durante il processo di importazione)

> [!NOTE]
>  Se si dispone di più computer che soddisfano i requisiti descritti in [Setting Up the Azure Import-Export Tool](storage-import-export-tool-setup-v1.md) (Configurazione dello strumento di importazione/esportazione di Azure), è possibile copiare i dati su più dischi rigidi in parallelo eseguendo un'istanza di questo strumento su ogni computer.

 Per ogni disco rigido preparato con lo strumento di importazione/esportazione di Azure, lo strumento creerà un singolo file journal. I file journal di tutte le unità sono necessari per creare il processo di importazione. Il file journal può inoltre essere usato per riprendere la preparazione dell'unità se lo strumento si blocca.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Sintassi dello strumento di importazione/esportazione di Azure per il processo di importazione
 Per preparare le unità al processo di importazione, chiamare lo strumento di importazione/esportazione di Azure con il comando **PrepImport** (PrepImport). I parametri da includere dipendono dal fatto che si tratti della prima sessione di copia o di una sessione di copia successiva.

 La prima sessione di copia per un'unità richiede parametri aggiuntivi per specificare la chiave dell'account di archiviazione, la lettera dell'unità di destinazione, se l'unità deve essere formattata, se l'unità deve essere crittografata e in tal caso, la chiave BitLocker e la directory log. Di seguito è riportata la sintassi di una prima sessione di copia per copiare una directory o un singolo file:

 **Prima sessione di copia per copiare una singola directory**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Prima sessione di copia per copiare un singolo file**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 Nelle sessioni di copia successive, non è necessario specificare i parametri iniziali. Di seguito è riportata la sintassi di una sessione di copia successiva per copiare una directory o un singolo file:

 **Sessione di copia successiva per copiare una singola directory**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Sessione di copia successiva per copiare un singolo file**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Parametri della prima sessione di copia per un disco rigido
 Ogni volta che si esegue lo strumento di importazione/esportazione di Azure per copiare i file sul disco rigido, lo strumento crea una sessione di copia. Ogni sessione di copia esegue la copia di una sola directory o un solo file su un disco rigido. Lo stato della sessione di copia viene scritto nel file journal. Se una sessione di copia viene interrotta (ad esempio a causa di un'interruzione di corrente del sistema), può essere ripresa eseguendo di nuovo lo strumento e specificando il file journal nella riga di comando.

> [!WARNING]
>  Se si specifica il parametro **/format** per la prima sessione di copia, l'unità verrà formattata e tutti i dati sull'unità verranno cancellati. È consigliabile usare unità vuote solo per la sessione di copia.

 Il comando usato per la prima sessione di copia per ogni unità richiede parametri diversi rispetto i comandi per le sessioni di copia successive. Nella tabella seguente sono elencati i parametri aggiuntivi disponibili per la prima sessione di copia:

|Parametro della riga di comando|Descrizione|
|-----------------------------|-----------------|
|**/sk:**<StorageAccountKey\>|`Optional.` Chiave dell'account di archiviazione per l'account di archiviazione in cui verranno importati i dati. È necessario includere **/sk:**<StorageAccountKey\> oppure **/csas:**<ContainerSas\> in the command.|
|**/csas:**<ContainerSas\>|`Optional`. La firma di accesso condiviso del contenitore da usare per importare i dati sull'account di archiviazione. È necessario includere **/sk:**<StorageAccountKey\> oppure **/csas:**<ContainerSas\> in the command.<br /><br /> Il valore per questo parametro deve iniziare con il nome del contenitore, seguito da un punto interrogativo (?) e dal token della firma di accesso condiviso. Ad esempio:<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> Le autorizzazioni, laddove specificato nell'URL o in un criterio di accesso archiviato, devono includere Lettura, Scrittura ed Eliminazione per i processi di importazione e Lettura, Scrittura ed Elenco per i processi di esportazione.<br /><br /> Quando viene specificato questo parametro, tutti i BLOB da importare o esportare devono trovarsi all'interno del contenitore specificato nella firma di accesso condiviso.|
|**/t:**<TargetDriveLetter\>|`Required.` Lettera di unità del disco rigido di destinazione per la sessione di copia corrente, senza i due punti finali.|
|**/format**|`Optional.` Specificare questo parametro se è necessario formattare l'unità. In caso contrario, ometterlo. Prima di formattare l'unità, verrà chiesta una conferma dalla console. Per evitare questa richiesta di conferma, specificare il parametro /silentmode.|
|**/silentmode**|`Optional.` Specificare questo parametro per eliminare la richiesta di conferma per la formattazione dell'unità.|
|**/encrypt**|`Optional.` Specificare questo parametro se l'unità non è ancora stata crittografata con BitLocker e deve essere crittografata tramite lo strumento. Se l'unità è già stata crittografata con BitLocker, omettere questo parametro e specificare il parametro `/bk`, fornendo la chiave BitLocker esistente.<br /><br /> Se si specifica il parametro `/format`, sarà necessario specificare anche il parametro `/encrypt`.|
|**/bk:**<BitLockerKey\>|`Optional.`Se `/encrypt` viene specificato, omettere questo parametro. Se `/encrypt` viene omesso, è necessario aver già crittografato l'unità con BitLocker. Usare questo parametro per specificare la chiave di BitLocker. La crittografia di BitLocker è necessaria per tutti i dischi rigidi destinati ai processi di importazione.|
|**/logdir:**<LogDirectory\>|`Optional.` La directory log specifica la directory da usare per archiviare i log dettagliati, nonché i file manifesti temporanei. Se non specificato, la directory corrente verrà usata come directory log.|

### <a name="parameters-required-for-all-copy-sessions"></a>Parametri necessari per tutte le sessioni di copia
 Il file journal contiene lo stato di tutte le sessioni di copia per un disco rigido. Include inoltre le informazioni necessarie per creare il processo di importazione. È sempre necessario specificare un file journal quando si esegue lo strumento di importazione/esportazione di Azure, nonché l'ID della sessione di copia:

|||
|-|-|
|Parametro della riga di comando|Descrizione|
|**/j:**<JournalFile\>|`Required.` Percorso del file journal. Ogni unità deve contenere esattamente un file journal. Si noti che il file journal non deve trovarsi nell'unità di destinazione. L'estensione del file journal è `.jrn`.|
|**/id:**<SessionId\>|`Required.` L'ID di sessione identifica una sessione di copia. Viene usato per garantire il recupero accurato di una sessione di copia interrotta. I file copiati in una sessione di copia vengono archiviati in una directory denominata in base all'ID di sessione nell'unità di destinazione.|

### <a name="parameters-for-copying-a-single-directory"></a>Parametri per copiare una singola directory
 Quando si copia una singola directory, si applicano i parametri obbligatori e facoltativi seguenti:

|Parametro della riga di comando|Descrizione|
|----------------------------|-----------------|
|**/srcdir:**<SourceDirectory\>|`Required.` Directory di origine contenente i file da copiare nell'unità di destinazione. Il percorso di directory deve essere un percorso assoluto, non un percorso relativo.|
|**/dstdir:**<DestinationBlobVirtualDirectory\>|`Required.` Percorso alla directory virtuale di destinazione nell'account di archiviazione di Windows Azure. La directory virtuale può esistere già o meno.<br /><br /> È possibile specificare un contenitore o un prefisso di BLOB come `music/70s/`. La directory di destinazione deve iniziare con il nome del contenitore, seguito da una barra "/" e può facoltativamente includere una directory di BLOB virtuale che termina con "/".<br /><br /> Quando il contenitore di destinazione è il contenitore radice, è necessario specificare in modo esplicito il contenitore radice, inclusa la barra, ad esempio `$root/`. Poiché i BLOB nel contenitore radice non possono includere "/" nel nome, quando la directory di destinazione è il contenitore radice tutte le sottodirectory nella directory di origine non verranno copiate.<br /><br /> Assicurarsi di usare nomi di contenitore validi quando si specificano BLOB o directory virtuali di destinazione. Tenere presente che i nomi di contenitore devono essere costituiti da lettere minuscole. Per le regole sulla denominazione dei contenitori, vedere [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/fileservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nome e riferimento a contenitori, BLOB e metadati).|
|**/Disposition:**<rename&#124;no-overwrite&#124;overwrite>|`Optional.` Specifica il comportamento quando esiste già un BLOB con l'indirizzo specificato. I valori validi per questo parametro sono: `rename`, `no-overwrite` e `overwrite`. Si noti che questi valori distinguono tra maiuscole e minuscole. Se non si specifica alcun valore, il valore predefinito è `rename`.<br /><br /> Il valore specificato per questo parametro ha effetto su tutti i file nella directory specificati dal parametro `/srcdir`.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` Specifica il tipo di BLOB per i BLOB di destinazione. I valori validi sono: `BlockBlob` e `PageBlob`. Si noti che questi valori distinguono tra maiuscole e minuscole. Se non si specifica alcun valore, il valore predefinito è `BlockBlob`.<br /><br /> Nella maggior parte dei casi, è consigliato `BlockBlob`. Se si specifica `PageBlob`, la lunghezza di ogni file nella directory deve essere un multiplo di 512, le dimensioni di una pagina per i BLOB di pagine.|
|**/PropertyFile:**<PropertyFile\>|`Optional.` Percorso del file delle proprietà per i BLOB di destinazione. Per altre informazioni, vedere [Import-Export Service Metadata and Properties File format](storage-import-export-file-format-metadata-and-properties.md) (Formato del file delle proprietà e dei metadati del servizio di importazione/esportazione)|
|**/MetadataFile:**<MetadataFile\>|`Optional.` Percorso del file dei metadati per i BLOB di destinazione. Per altre informazioni, vedere [Import-Export Service Metadata and Properties File format](storage-import-export-file-format-metadata-and-properties.md) (Formato del file delle proprietà e dei metadati del servizio di importazione/esportazione)|

### <a name="parameters-for-copying-a-single-file"></a>Parametri per copiare un singolo file
 Quando si copia un singolo file, si applicano i parametri obbligatori e facoltativi seguenti:

|Parametro della riga di comando|Descrizione|
|----------------------------|-----------------|
|**/srcfile:**<SourceFile\>|`Required.` Percorso completo del file da copiare. Il percorso di directory deve essere un percorso assoluto, non un percorso relativo.|
|**/dstblob:**<DestinationBlobPath\>|`Required.` Percorso del BLOB di destinazione nell'account di archiviazione di Windows Azure. Il BLOB può esistere già o meno.<br /><br /> Specificare il nome del BLOB che inizia con il nome del contenitore. Il nome del BLOB non può iniziare con "/" o con il nome dell'account di archiviazione. Per le regole sulla denominazione dei BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/fileservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nome e riferimento a contenitori, BLOB e metadati).<br /><br /> Quando il contenitore di destinazione è il contenitore radice, è necessario specificare in modo esplicito `$root` come contenitore, ad esempio `$root/sample.txt`. Si noti che i BLOB nel contenitore radice non possono includere "/" nei nomi.|
|**/Disposition:**<rename&#124;no-overwrite&#124;overwrite>|`Optional.` Specifica il comportamento quando esiste già un BLOB con l'indirizzo specificato. I valori validi per questo parametro sono: `rename`, `no-overwrite` e `overwrite`. Si noti che questi valori distinguono tra maiuscole e minuscole. Se non si specifica alcun valore, il valore predefinito è `rename`.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` Specifica il tipo di BLOB per i BLOB di destinazione. I valori validi sono: `BlockBlob` e `PageBlob`. Si noti che questi valori distinguono tra maiuscole e minuscole. Se non si specifica alcun valore, il valore predefinito è `BlockBlob`.<br /><br /> Nella maggior parte dei casi, è consigliato `BlockBlob`. Se si specifica `PageBlob`, la lunghezza di ogni file nella directory deve essere un multiplo di 512, le dimensioni di una pagina per i BLOB di pagine.|
|**/PropertyFile:**<PropertyFile\>|`Optional.` Percorso del file delle proprietà per i BLOB di destinazione. Per altre informazioni, vedere [Import-Export Service Metadata and Properties File format](storage-import-export-file-format-metadata-and-properties.md) (Formato del file delle proprietà e dei metadati del servizio di importazione/esportazione)|
|**/MetadataFile:**<MetadataFile\>|`Optional.` Percorso del file dei metadati per i BLOB di destinazione. Per altre informazioni, vedere [Import-Export Service Metadata and Properties File format](storage-import-export-file-format-metadata-and-properties.md) (Formato del file delle proprietà e dei metadati del servizio di importazione/esportazione)|

### <a name="resuming-an-interrupted-copy-session"></a>Ripresa di una sessione di copia interrotta
 Se una sessione di copia viene interrotta per un qualsiasi motivo, è possibile ripristinarla eseguendo lo strumento e specificando solo il file journal:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 È possibile riprendere solo la sessione di copia più recente, se terminata in modo anomalo.

> [!IMPORTANT]
>  Quando si riprende una sessione di copia, non modificare i file dei dati di origine e le directory aggiungendo o rimuovendo i file.

### <a name="aborting-an-interrupted-copy-session"></a>Abbandono di una sessione di copia interrotta
 Se una sessione di copia è stata interrotta e non è possibile riprenderla (ad esempio se una directory di origine è inaccessibile), è necessario abbandonare la sessione corrente in modo da poter eseguire il rollback e avviare nuove sessioni di copia:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 È possibile abbandonare solo la sessione di copia più recente, se terminata in modo anomalo. Si noti che, per un'unità, non è possibile interrompere la prima sessione di copia. È invece necessario riavviare la sessione di copia con un nuovo file journal.

## <a name="see-also"></a>Vedere anche
 [Setting Up the Azure Import-Export Tool](storage-import-export-tool-setup-v1.md)
(Configurazione dello strumento di importazione/esportazione di Azure) [Setting Properties and Metadata during the Import Process](storage-import-export-tool-setting-properties-metadata-import-v1.md)
(Impostazione di proprietà e metadati durante il processo di importazione) [Sample Workflow to Prepare Hard Drives for an Import Job](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
(Flusso di lavoro di esempio per preparare i dischi rigidi al processo di importazione) [Quick Reference for Frequently Used Commands](storage-import-export-tool-quick-reference-v1.md) 
(Guida di riferimento rapida per i comandi di uso frequente) [Reviewing Job Status with Copy Log Files](storage-import-export-tool-reviewing-job-status-v1.md)
(Revisione dello stato del processo con i file di log di copia) [Repairing an Import Job](storage-import-export-tool-repairing-an-import-job-v1.md)
(Riparazione di un processo di importazione) [Repairing an Export Job](storage-import-export-tool-repairing-an-export-job-v1.md)
(Riparazione di un processo di esportazione) [Troubleshooting the Azure Import-Export Tool](storage-import-export-tool-troubleshooting-v1.md)(Risoluzione dei problemi dello strumento di importazione-esportazione di Azure)



<!--HONumber=Dec16_HO3-->


