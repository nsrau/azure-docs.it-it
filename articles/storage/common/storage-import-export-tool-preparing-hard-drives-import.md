---
title: Preparazione dei dischi rigidi per un processo di importazione di Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su come preparare i dischi rigidi usando lo strumento WAImportExport per creare un processo di importazione per il servizio Importazione/Esportazione di Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 23f2640bc71bc4eba0f3fc76014cce4a298bfcfa
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="preparing-hard-drives-for-an-import-job"></a>Preparazione dei dischi rigidi per un processo di importazione

WAImportExport è lo strumento di preparazione e ripristino delle unità che è possibile usare con il [servizio Importazione/Esportazione di Microsoft Azure](../storage-import-export-service.md). Questo strumento consente di copiare dati nei dischi rigidi da spedire a un data center di Azure. Al termine di un processo di importazione, è possibile usare lo strumento per ripristinare gli eventuali BLOB danneggiati, mancanti o in conflitto con altri BLOB. Dopo aver ricevuto le unità da un processo di esportazione completato, lo strumento consente di ripristinare gli eventuali file danneggiati o mancanti nelle unità. In questo articolo viene esaminato il funzionamento di questo strumento.

## <a name="prerequisites"></a>Prerequisiti

### <a name="requirements-for-waimportexportexe"></a>Requisiti per WAImportExport.exe

- **Configurazione del computer**
  - Windows 7, Windows Server 2008 R2 o un sistema operativo Windows più recente
  - Deve essere installato .NET Framework 4. Vedere le [domande frequenti](#faq) per informazioni su come verificare se .Net Framework è installato nel computer.
- **Chiave dell'account di archiviazione:** è necessaria almeno una delle chiavi dell'account di archiviazione.

### <a name="preparing-disk-for-import-job"></a>Preparazione del disco per il processo di importazione

- **BitLocker:** BitLocker deve essere abilitato nel computer che esegue lo strumento WAImportExport. Vedere le [domande frequenti](#faq) per informazioni su come abilitare BitLocker.
- **Dischi:** accessibili dal computer in cui viene eseguito lo strumento WAImportExport. Vedere le [domande frequenti](#faq) per le specifiche del disco.
- **File di origine:** i file che si intende importare devono essere accessibili dal computer di copia, a prescindere dal fatto che si trovino in una condivisione di rete o in un disco rigido locale.

### <a name="repairing-a-partially-failed-import-job"></a>Ripristino di un processo di importazione parzialmente non riuscito

- **Copiare il file di log** generato quando il servizio Importazione/Esportazione di Azure copia i dati tra l'account di archiviazione e il disco. Il file si trova nell'account di archiviazione di destinazione.

### <a name="repairing-a-partially-failed-export-job"></a>Ripristino di un processo di esportazione parzialmente non riuscito

- **Copiare il file di log** generato quando il servizio Importazione/Esportazione di Azure copia i dati tra l'account di archiviazione e il disco. Il file si trova nell'account di archiviazione di origine.
- **File manifesto**: [facoltativo] si trova nell'unità esportata restituita da Microsoft.

## <a name="download-and-install-waimportexport"></a>Scaricare e installare WAImportExport

Scaricare la [versione più recente di WAImportExport.exe](https://www.microsoft.com/download/details.aspx?id=55280). Estrarre il contenuto compresso in una directory del computer.

L'attività successiva consiste nel creare i file CSV.

## <a name="prepare-the-dataset-csv-file"></a>Preparare il file CSV dataset

### <a name="what-is-dataset-csv"></a>Definizione del file CSV dataset

Il file CSV dataset, che corrisponde al valore del flag /dataset, è un file CSV che contiene un elenco di directory e/o un elenco di file da copiare nelle unità di destinazione. Il primo passaggio per la creazione di un processo di importazione è determinare le directory e i file da importare. Può trattarsi di un elenco di directory, di un elenco di file univoci o di una combinazione di entrambi gli elementi. Quando viene inclusa una directory, tutti i file presenti nella directory e nelle relative sottodirectory fanno parte del processo di importazione.

Per ogni directory o file da importare, è necessario identificare un BLOB o una directory virtuale di destinazione nel servizio BLOB di Azure. Queste destinazioni vengono successivamente usate come input per lo strumento WAImportExport. Le directory devono essere delimitate con il carattere barra "/".

La tabella seguente mostra alcuni esempi di destinazioni BLOB:

| File o directory di origine | BLOB o directory virtuale di destinazione |
| --- | --- |
| H:\Video | https://mystorageaccount.blob.core.windows.net/video |
| H:\Photo | https://mystorageaccount.blob.core.windows.net/photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.blob.core.windows.net/music |

### <a name="sample-datasetcsv"></a>Esempio di dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Campi del file CSV dataset

| Campo | Descrizione |
| --- | --- |
| BasePath | **[Obbligatorio]**<br/>Il valore di questo parametro rappresenta l'origine in cui si trovano i dati da importare. Lo strumento copierà in modo ricorsivo tutti i dati che si trovano in questo percorso.<br><br/>**Valori consentiti:** deve essere un percorso valido nel computer locale o un percorso di condivisione valido e deve essere accessibile dall'utente. Il percorso di directory deve essere un percorso assoluto, non un percorso relativo. Se il percorso termina con "\\" rappresenta una directory, mentre se termina senza "\\" rappresenta un file.<br/>In questo campo non sono consentiti regex. Se il percorso contiene spazi, racchiuderlo tra "".<br><br/>**Esempio**: "c:\Directory\c\Directory\File.txt"<br>"\\\\FBaseFilesharePath.domain.net\nomecondivisione\directory\"  |
| DstBlobPathOrPrefix | **[Obbligatorio]**<br/> Percorso della directory virtuale di destinazione nell'account di archiviazione di Windows Azure. La directory virtuale può essere già esistente. Se non esiste, il servizio Importazione/Esportazione ne crea una.<br/><br/>Assicurarsi di usare nomi di contenitore validi quando si specificano BLOB o directory virtuali di destinazione. Tenere presente che i nomi di contenitore devono essere costituiti da lettere minuscole. Per le regole sulla denominazione dei contenitori, vedere [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nome e riferimento a contenitori, BLOB e metadati). Se è specificata solo la radice, la struttura di directory dell'origine viene replicata nel contenitore BLOB di destinazione. Se si vuole una struttura di directory diversa da quella dell'origine, sono necessarie più righe di mapping nel file CSV<br/><br/>È possibile specificare un contenitore o un prefisso di BLOB come music/70s/. La directory di destinazione deve iniziare con il nome del contenitore, seguito da una barra "/" e facoltativamente può includere una directory BLOB virtuale che termina con "/".<br/><br/>Quando la destinazione è il contenitore radice, è necessario specificarlo in modo esplicito, compresa la barra, con $root/. Poiché i BLOB nel contenitore radice non possono includere "/" nel nome, quando la directory di destinazione è il contenitore radice le sottodirectory della directory di origine non vengono copiate.<br/><br/>**Esempio**<br/>Se il percorso BLOB di destinazione è https://mystorageaccount.blob.core.windows.net/video, il valore di questo campo può essere video/  |
| BlobType | **[Facoltativo]**  block &#124; page<br/>Attualmente il servizio Importazione/Esportazione supporta 2 tipi di BLOB. di pagine e in blocchi. Per impostazione predefinita, tutti i file vengono importati come BLOB in blocchi. I file con estensione \*.vhd e \*.vhdx vengono importati come BLOB di pagine. La dimensione dei BLOB in blocchi e di pagine non può superare il valore massimo consentito. Per altre informazioni, vedere [Obiettivi di scalabilità per BLOB, code, tabelle e file](storage-scalability-targets.md#scalability-targets-for-blobs-queues-tables-and-files).  |
| Disposition | **[Facoltativo]** rename &#124; no-overwrite &#124; overwrite <br/> Questo campo specifica il comportamento di copia durante l'importazione, ovvero quando i dati vengono caricati nell'account di archiviazione dal disco. Le opzioni disponibili sono: rename&#124;overwite&#124;no-overwrite. Se non è specificato alcun valore, viene utilizzata l'opzione predefinita "rename". <br/><br/>**Rename**: se è presente un oggetto con lo stesso nome, viene creata una copia nella destinazione.<br/>Overwrite: il file viene sovrascritto con un file più recente. Prevale il file modificato per ultimo.<br/>**No-overwrite**: se il file è già presente, non viene scritto.|
| MetadataFile | **[Facoltativo]** <br/>Il valore per questo campo è il file di metadati che può essere specificato se è necessario mantenere i metadati degli oggetti o specificare metadati personalizzati. Percorso del file di metadati per i BLOB di destinazione. Per altre informazioni, vedere [Import/Export service Metadata and Properties File Format](../storage-import-export-file-format-metadata-and-properties.md) (Formato dei file di metadati e delle proprietà del servizio Importazione/Esportazione) |
| PropertiesFile | **[Facoltativo]** <br/>Percorso del file delle proprietà per i BLOB di destinazione. Per altre informazioni, vedere [Import/Export service Metadata and Properties File Format](../storage-import-export-file-format-metadata-and-properties.md) (Formato dei file di metadati e delle proprietà del servizio Importazione/Esportazione). |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>Preparare il file CSV InitialDriveSet o AdditionalDriveSet

### <a name="what-is-driveset-csv"></a>Definizione del file CSV driveset

Il valore del flag /InitialDriveSet o /AdditionalDriveSet è un file CSV che contiene l'elenco dei dischi in cui vengono mappate le lettere di unità in modo che lo strumento possa rilevare correttamente l'elenco dei dischi da preparare. Se le dimensioni dei dati sono superiori alla dimensione di un singolo disco, lo strumento WAImportExport opera una distribuzione ottimizzata dei dati tra i diversi dischi elencati nel file CSV.

Non esiste alcun limite al numero di dischi in cui è possibile scrivere i dati in un'unica sessione. Lo strumento distribuisce i dati in base alle dimensioni del disco e della cartella, selezionando il disco che risulta maggiormente ottimizzato per le dimensioni dell'oggetto. Dopo che i dati sono stati caricati nell'account di archiviazione, ne viene eseguita la convergenza nella struttura di directory specificata nel file dataset. Per creare un file CSV driveset, attenersi alla procedura seguente.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Creare un volume di base e assegnare una lettera di unità

Per creare un volume di base e assegnare una lettera di unità, seguire le istruzioni per la creazione semplificata di partizioni disponibile in [Overview of Disk Management](https://technet.microsoft.com/library/cc754936.aspx) (Panoramica di Gestione disco).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>File CSV InitialDriveSet e AdditionalDriveSet di esempio

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Campi del file CSV driveset

| Fields | Valore |
| --- | --- |
| DriveLetter | **[Obbligatorio]**<br/> Ogni unità fornita allo strumento come destinazione deve includere un volume NTFS semplice e deve avere una lettera di unità assegnata.<br/> <br/>**Esempio**: R o r |
| FormatOption | **[Obbligatorio]** Format &#124; AlreadyFormatted<br/><br/> **Format**: se si specifica questo valore, tutti i dati sul disco vengono formattati. <br/>**AlreadyFormatted**: quando viene specificato questo valore, lo strumento ignora la formattazione. |
| SilentOrPromptOnFormat | **[Obbligatorio]** SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: se si specifica questo valore, l'utente può eseguire lo strumento in modalità non interattiva. <br/>**PromptOnFormat**: lo strumento richiede all'utente di confermare se l'azione è veramente prevista per tutti i formati.<br/><br/>Se il valore non è impostato, il comando viene interrotto e viene visualizzato il messaggio di errore "Incorrect value for SilentOrPromptOnFormat: none" (Valore non corretto per SilentOrPromptOnFormat: none) |
| Crittografia | **[Obbligatorio]** Encrypt &#124; AlreadyEncrypted<br/> Il valore di questo campo indica quale disco crittografare. <br/><br/>**Encrypt**: lo strumento formatta l'unità. Se il campo "FormatOption" è impostato su "Format", il valore di questo campo deve essere "Encrypt". Se in questo caso viene specificato"AlreadyEncrypted", viene restituito l'errore seguente: "When Format is specified, Encrypt must also be specified" (Quando viene specificato Format, è necessario specificare anche Encrypt).<br/>**AlreadyEncrypted**: lo strumento decrittografa l'unità usando la chiave BitLocker specificata nel campo "ExistingBitLockerKey". Se il valore del campo "FormatOption" è "AlreadyFormatted", il valore di questo campo può essere "Encrypt" o "AlreadyEncrypted". |
| ExistingBitLockerKey | **[Obbligatorio]** Se il valore del campo "Encryption" è "AlreadyEncrypted",<br/> il valore di questo campo corrisponde alla chiave BitLocker associata al disco specifico. <br/><br/>Se il valore del campo "Encryption" è "Encrypt", questo campo deve essere lasciato vuoto.  Se in questo caso è specificata una chiave BitLocker, viene restituito l'errore seguente: "Bitlocker Key should not be specified" (Non specificare la chiave BitLocker).<br/>  **Esempio**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Preparazione del disco per il processo di importazione

Per preparare le unità per il processo di importazione, chiamare lo strumento WAImportExport con il comando **PrepImport**. I parametri da includere dipendono dal fatto che si tratti della prima sessione di copia o di una sessione di copia successiva.

### <a name="first-session"></a>Prima sessione

Prima sessione di copia di una directory singola o multipla in un disco singolo o multiplo, a seconda di quanto specificato nel file CSV - Comando PrepImport dello strumento WAImportExport per la prima sessione di copia di directory e/o file con una nuova sessione di copia:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Esempio:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Aggiungere dati in una sessione successiva

Nelle sessioni di copia successive non è necessario specificare i parametri iniziali. È necessario usare lo stesso file journal per consentire allo strumento di riprendere dal punto in cui l'operazione è stata sospesa nella sessione precedente. Lo stato della sessione di copia viene scritto nel file journal. Di seguito è riportata la sintassi relativa a una sessione successiva per la copia di directory e/o file aggiuntivi:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Esempio:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>Aggiungere unità all'ultima sessione

Se le unità specificate in InitialDriveset non sono sufficienti per i dati, è possibile usare lo strumento per aggiungere altre unità alla stessa sessione di copia. 

>[!NOTE] 
>L'ID sessione deve corrispondere all'ID della sessione precedente. Il file journal deve corrispondere a quello specificato nella sessione precedente.
>
```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
```

**Esempio:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>Interrompere l'ultima sessione

Se una sessione di copia si blocca e non è possibile riprenderla, ad esempio perché una directory di origine risulta inaccessibile, è necessario interrompere la sessione corrente in modo da poter eseguire il rollback e avviare nuove sessioni di copia:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Esempio:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

È possibile interrompere solo l'ultima sessione di copia, se terminata in modo anomalo. Si noti che, per un'unità, non è possibile interrompere la prima sessione di copia. È invece necessario riavviarla con un nuovo file journal.

### <a name="resume-a-latest-interrupted-session"></a>Riprendere l'ultima sessione interrotta

Se una sessione di copia viene interrotta per qualsiasi motivo, è possibile riprenderla eseguendo lo strumento e specificando solo il file journal:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Esempio:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> Quando si riprende una sessione di copia, non modificare le directory e i file dei dati di origine aggiungendo o rimuovendo file.

## <a name="waimportexport-parameters"></a>Parametri di WAImportExport

| Parametri | Descrizione |
| --- | --- |
|     /j:&lt;FileJournal&gt;  | **Obbligatorio**<br/> Percorso del file journal. Un file journal tiene traccia di un set di unità e registra lo stato di avanzamento della preparazione di queste unità. È sempre necessario specificare il file journal.  |
|     /logdir:&lt;DirectoryLog&gt;  | **Facoltativo**. Directory dei log.<br/> In questa directory vengono registrati i file di log dettagliati e alcuni file temporanei. Se questo parametro non è specificato, come directory dei log viene usata la directory corrente. La directory dei log può essere specificata una sola volta per lo stesso file journal.  |
|     /id:&lt;IdSessione&gt;  | **Obbligatorio**<br/> L'ID sessione consente di identificare una sessione di copia. Viene usato per garantire il recupero accurato di una sessione di copia interrotta.  |
|     /ResumeSession  | Facoltativa. Se l'ultima sessione di copia è terminata in modo anomalo, è possibile specificare questo parametro per riprenderla.   |
|     /AbortSession  | Facoltativa. Se l'ultima sessione di copia è terminata in modo anomalo, è possibile specificare questo parametro per interromperla.  |
|     /sn:&lt;NomeAccountArchiviazione&gt;  | **Obbligatorio**<br/> Applicabile solo per RepairImport e RepairExport. Nome dell'account di archiviazione.  |
|     /sk:&lt;ChiaveAccountArchiviazione&gt;  | **Obbligatorio**<br/> Chiave dell'account di archiviazione. |
|     /InitialDriveSet:&lt;driveset.csv&gt;  | **Obbligatorio** Quando si esegue la prima sessione di copia<br/> File CSV che contiene un elenco di unità da preparare.  |
|     /AdditionalDriveSet:&lt;driveset.csv&gt; | **Obbligatoria**. Usato per l'aggiunta di unità alla sessione di copia corrente. <br/> File CSV che contiene un elenco di unità da aggiungere.  |
|      /r:&lt;FileRipristino&gt; | **Obbligatorio** Applicabile solo per RepairImport e RepairExport.<br/> Percorso del file per tenere traccia dello stato di avanzamento del ripristino. Ogni unità deve contenere un solo file di ripristino.  |
|     /d:&lt;DirectoryDestinazione&gt; | **Obbligatoria**. Applicabile solo per RepairImport e RepairExport. Per RepairImport, una o più directory da ripristinare separate da punto e virgola; per RepairExport, una sola directory da ripristinare, ad esempio la radice dell'unità.  |
|     /CopyLogFile:&lt;FileLogCopiaUnità&gt; | **Obbligatorio** Applicabile solo per RepairImport e RepairExport. Percorso del file di log di copia dell'unità (file dettagliato o file di errore).  |
|     /ManifestFile:&lt;FileManifestoUnità&gt; | **Obbligatorio** Applicabile solo per RepairImport e RepairExport.<br/> Percorso del file manifesto dell'unità.  |
|     /PathMapFile:&lt;FileMappingPercorsoUnità&gt; | **Facoltativo**. Applicabile solo per RepairImport.<br/> Percorso del file contenente i mapping dei percorsi di file relativi alla radice dell'unità ai percorsi dei file effettivi (delimitati da tabulazione). Quando questo parametro viene specificato per la prima volta, viene popolato con percorsi di file con destinazioni vuote, perché si tratta di file non trovati in TargetDirectories, con accesso negato, con nome non valido o esistenti in più directory. Il file di mapping può essere modificato manualmente includendo i percorsi di destinazione corretti e può essere specificato nuovamente in modo da consentire allo strumento di risolvere i percorsi dei file in modo corretto.  |
|     /ExportBlobListFile:&lt;FileElencoBlobEsportazione&gt; | **Obbligatoria**. Applicabile solo per PreviewExport.<br/> Percorso del file XML contenente l'elenco dei percorsi o dei prefissi dei percorsi BLOB per i BLOB da esportare. Il formato del file è lo stesso usato dall'elenco dei BLOB nell'operazione Put Job dell'API REST del servizio Importazione/Esportazione.  |
|     /DriveSize:&lt;DimensioneUnità&gt; | **Obbligatoria**. Applicabile solo per PreviewExport.<br/>  Dimensioni delle unità da usare per l'esportazione, Ad esempio: 500 GB, 1,5 TB. Nota: 1 GB = 1.000.000.000 di byte, 1 TB = 1.000.000.000.000 di byte  |
|     /DataSet:&lt;dataset.csv&gt; | **Obbligatorio**<br/> File CSV che contiene un elenco di directory e/o un elenco di file da copiare nelle unità di destinazione.  |
|     /silentmode  | **Facoltativo**.<br/> Se questo parametro non è specificato, viene visualizzato un messaggio con i requisiti delle unità e sarà necessaria la conferma dell'utente per continuare.  |

## <a name="tool-output"></a>Output dello strumento

### <a name="sample-drive-manifest-file"></a>Esempio di file manifesto dell'unità

```xml
<?xml version="1.0" encoding="UTF-8"?>
<DriveManifest Version="2011-MM-DD">
   <Drive>
      <DriveId>drive-id</DriveId>
      <StorageAccountKey>storage-account-key</StorageAccountKey>
      <ClientCreator>client-creator</ClientCreator>
      <!-- First Blob List -->
      <BlobList Id="session#1-0">
         <!-- Global properties and metadata that applies to all blobs -->
         <MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>
         <PropertiesPath Hash="md5-hash">global-properties-file-path</PropertiesPath>
         <!-- First Blob -->
         <Blob>
            <BlobPath>blob-path-relative-to-account</BlobPath>
            <FilePath>file-path-relative-to-transfer-disk</FilePath>
            <ClientData>client-data</ClientData>
            <Length>content-length</Length>
            <ImportDisposition>import-disposition</ImportDisposition>
            <!-- page-range-list-or-block-list -->
            <!-- page-range-list -->
            <PageRangeList>
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
            </PageRangeList>
            <!-- block-list -->
            <BlockList>
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
            </BlockList>
            <MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>
            <PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>
         </Blob>
      </BlobList>
   </Drive>
</DriveManifest>
```

### <a name="sample-journal-file-xml-for-each-drive"></a>Esempio di file journal con estensione xml per ogni unità

```xml
[BeginUpdateRecord][2016/11/01 21:22:25.379][Type:ActivityRecord]
ActivityId: DriveInfo
DriveState: [BeginValue]
<?xml version="1.0" encoding="UTF-8"?>
<Drive>
   <DriveId>drive-id</DriveId>
   <BitLockerKey>*******</BitLockerKey>
   <ManifestFile>\DriveManifest.xml</ManifestFile>
   <ManifestHash>D863FE44F861AE0DA4DCEAEEFFCCCE68</ManifestHash> </Drive>
[EndValue]
SaveCommandOutput: Completed
[EndUpdateRecord]
```

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Esempio di file journal con estensione jrn che tiene traccia della successione delle sessioni

```
[BeginUpdateRecord][2016/11/02 18:24:14.735][Type:NewJournalFile]
VocabularyVersion: 2013-02-01
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.749][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
LogDirectory: F:\logs
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.754][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
StorageAccountKey: *******
[EndUpdateRecord]
```

## <a name="faq"></a>Domande frequenti

### <a name="general"></a>Generale

#### <a name="what-is-waimportexport-tool"></a>Che cos'è lo strumento WAImportExport?

WAImportExport è lo strumento di preparazione e ripristino delle unità che è possibile usare con il servizio Importazione/Esportazione di Microsoft Azure. Questo strumento consente di copiare dati nei dischi rigidi da spedire a un data center di Azure. Al termine di un processo di importazione, è possibile usare lo strumento per ripristinare gli eventuali BLOB danneggiati, mancanti o in conflitto con altri BLOB. Dopo aver ricevuto le unità da un processo di esportazione completato, lo strumento consente di ripristinare gli eventuali file danneggiati o mancanti nelle unità.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>Come funziona lo strumento WAImportExport con più dischi e directory di origine?

Se le dimensioni dei dati sono superiori alla dimensione del disco, lo strumento WAImportExport opera una distribuzione ottimizzata dei dati tra i diversi dischi. La copia dei dati in più dischi può essere eseguita in parallelo o in sequenza. Non esiste alcun limite al numero di dischi in cui è possibile scrivere i dati contemporaneamente. Lo strumento distribuisce i dati in base alle dimensioni del disco e della cartella, selezionando il disco che risulta maggiormente ottimizzato per le dimensioni dell'oggetto. Dopo che i dati sono stati caricati nell'account di archiviazione, ne viene eseguita la convergenza nella struttura di directory specificata.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>Dove è reperibile la versione precedente dello strumento WAImportExport?

WAImportExport include tutte le funzionalità dello strumento WAImportExport V1. Consente agli utenti di specificare più origini e scrivere in più unità. È inoltre possibile gestire con facilità più percorsi di origine da cui copiare i dati in un singolo file CSV. Tuttavia, se è necessario il supporto SAS o se si vuole copiare una singola origine in un solo disco, è possibile [scaricare lo strumento WAImportExport V1] (http://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid=0x409) e vedere [WAImportExport V1 Reference](storage-import-export-tool-how-to-v1.md) (Informazioni di riferimento su WAImportExport V1) per indicazioni sull'utilizzo di questo strumento.

#### <a name="what-is-a-session-id"></a>Che cos'è l'ID sessione?

Se si intende distribuire i dati in più dischi, lo strumento prevede che venga usato lo stesso parametro della sessione di copia (/id). Mantenendo lo stesso nome della sessione di copia, è possibile copiare i dati da una o più posizioni di origine in uno o più dischi o directory di destinazione. Con lo stesso ID sessione, lo strumento può riprendere la copia dei file dal punto in cui è stata sospesa l'ultima volta.

Tuttavia, la stessa sessione di copia non può essere usata per importare i dati in account di archiviazione diversi.

Quando il nome della sessione di copia è lo stesso in più esecuzioni dello strumento, anche il file di log (/logdir) e la chiave dell'account di archiviazione (/sk) devono essere gli stessi.

L'ID sessione può essere costituito da lettere, numeri da 0 a 9, caratteri di sottolineatura (\_), trattini (-) o hash (#) e la relativa lunghezza deve essere compresa tra 3 e 30 caratteri.

Esempio: session-1 oppure session#1 oppure session\_1

#### <a name="what-is-a-journal-file"></a>Che cos'è un file journal?

Ogni volta che si esegue WAImportExport per copiare file nel disco rigido, lo strumento crea una sessione di copia. Lo stato della sessione di copia viene scritto nel file journal. Se una sessione di copia viene interrotta, ad esempio a causa di un'interruzione dell'alimentazione del sistema, può essere ripresa eseguendo nuovamente lo strumento e specificando il file journal alla riga di comando.

Per ogni disco rigido preparato con lo strumento Importazione/Esportazione di Azure, viene creato un singolo file journal denominato "&lt;IDUnità&gt;.xml", dove l'ID corrisponde al numero di serie associato all'unità che lo strumento legge dal disco. Per creare il processo di importazione sono necessari i file journal di tutte le unità. Il file journal può essere usato anche per riprendere la preparazione delle unità nel caso in cui lo strumento venga interrotto.

#### <a name="what-is-a-log-directory"></a>Che cos'è una directory dei log?

La directory dei log è una directory da usare per archiviare log dettagliati e file manifesto temporanei. Se non è specificata, come directory dei log viene usata la directory corrente. I log sono di tipo dettagliato.

### <a name="prerequisites"></a>Prerequisiti

#### <a name="what-are-the-specifications-of-my-disk"></a>Quali sono le specifiche dei dischi?

Sono necessari uno o più dischi rigidi SATA II, III o SSD vuoti da 2,5 o 3,5 pollici, collegati al computer di copia.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>Come è possibile abilitare BitLocker nel computer?

Un modo semplice per effettuare una verifica è fare clic con il pulsante destro del mouse sull'unità di sistema. Se la funzionalità è attivata, vengono visualizzate le opzioni per BitLocker. Se è disattivata, le opzioni non sono visibili.

![Verificare BitLocker](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

È disponibile un articolo con informazioni su [come abilitare BitLocker](https://technet.microsoft.com/library/cc766295.aspx).

È possibile che nel computer non sia presente un chip TPM. Se non si ottiene output tramite tpm.msc, vedere la prossima domanda.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>Come è possibile disabilitare Trusted Platform Module (TPM) in BitLocker?
> [!NOTE]
> È necessario disabilitare i criteri TPM solo se non è presente alcun modulo TPM nei server. Se è presente un modulo TPM attendibile nel server dell'utente, non è necessario disabilitare TPM. 
> 

Per disabilitare TPM in BitLocker, attenersi alla procedura seguente:<br/>
1. Avviare **Group Policy Editor** (Editor Criteri di gruppo) digitando gpedit.msc in un prompt dei comandi. Se **Group Policy Editor** (Editor Criteri di gruppo) non è disponibile, per abilitare prima BitLocker vedere la domanda precedente.
2. Aprire **Criteri del computer locale &gt; Configurazione computer &gt; Modelli amministrativi &gt; Componenti di Windows&gt; Crittografia unità BitLocker &gt; Unità del sistema operativo**.
3. Modificare il criterio **Richiedi autenticazione aggiuntiva all'avvio**.
4. Impostare il criterio su **Abilitato** e assicurarsi che l'opzione **Consenti BitLocker senza un TPM compatibile** sia selezionata.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>Come è possibile verificare se nel computer in uso è installato .NET 4 o una versione successiva?

Tutte le versioni di Microsoft .NET Framework sono installate nella directory seguente: %windir%\Microsoft.NET\Framework\

Selezionare la directory del computer di destinazione citata in precedenza in cui deve essere eseguito lo strumento. Cercare il nome di cartella che inizia con "v4". Se non è presente una directory di questo tipo, .NET 4 non è installato nel computer in uso. È possibile scaricare .Net 4 nel computer dalla pagina [Microsoft .NET Framework 4 (programma di installazione Web)](https://www.microsoft.com/download/details.aspx?id=17851).

### <a name="limits"></a>Limiti

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>Quante unità è possibile preparare o inviare contemporaneamente?

Non esistono limiti al numero di dischi che lo strumento è in grado di preparare. Tuttavia, poiché lo strumento prevede l'input delle lettere di unità, non è possibile preparare più di 25 dischi simultaneamente. Un singolo processo può gestire al massimo 10 dischi alla volta. Se si preparano più di 10 dischi che hanno come destinazione lo stesso account di archiviazione, i dischi possono essere distribuiti in più processi.

#### <a name="can-i-target-more-than-one-storage-account"></a>È possibile impostare più account di archiviazione come destinazione?

È possibile inviare un solo account di archiviazione per ogni processo e in ogni singola sessione di copia.

### <a name="capabilities"></a>Capabilities

#### <a name="does-waimportexportexe-encrypt-my-data"></a>WAImportExport.exe esegue la crittografia dei dati?

Sì. La crittografia di BitLocker è abilitata ed è obbligatoria per questo processo.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>Qual è la gerarchia dei dati quando vengono visualizzati nell'account di archiviazione?

Anche se i dati vengono distribuiti in più dischi, dopo che sono stati caricati nell'account di archiviazione ne viene eseguita la convergenza nella struttura di directory specificata nel file CSV dataset.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>Quanti sono i dischi di input con I/O attivo in parallelo quando è in corso la copia?

Lo strumento distribuisce i dati tra i dischi di input in base alla dimensione dei file di input. In ogni caso, il numero di dischi attivi in parallelo dipende totalmente dalla natura dei dati di input. A seconda delle dimensioni dei singoli file nel set di dati di input, l'I/O può essere attivo in parallelo in uno o più dischi. Per informazioni più dettagliate, vedere la prossima domanda.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>Come avviene la distribuzione dei file nei dischi?

Lo strumento WAImportExport legge e scrive i file un batch alla volta e un batch contiene al massimo 100.000 file. È pertanto possibile scrivere al massimo 100.000 file in parallelo. Se i 100.000 file vengono distribuiti in più unità, la scrittura viene eseguita contemporaneamente in più dischi. Tuttavia, il fatto che lo strumento scriva in un solo disco o in più dischi simultaneamente dipende dalle dimensioni complessive del batch. Ad esempio, nel caso di file di dimensioni ridotte, se tutti i 100.000 file entrano in una singola unità, lo strumento li scrive in un solo disco durante l'elaborazione del batch.

### <a name="waimportexport-output"></a>Output di WAImportExport

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Sono disponibili due file journal, quale deve essere caricato nel portale di Azure?

**.xml**: per ogni disco rigido preparato con lo strumento WAImportExport, viene creato un singolo file journal denominato `<DriveID>.xml`, dove l'IDUnità corrisponde al numero di serie associato all'unità che lo strumento legge dal disco. Per creare il processo di importazione nel portale di Azure sono necessari i file journal di tutte le unità. Il file journal può essere usato anche per riprendere la preparazione delle unità nel caso in cui lo strumento venga interrotto.

**.jrn**: il file journal con suffisso `.jrn` contiene lo stato di tutte le sessioni di copia di un disco rigido. Include inoltre le informazioni necessarie per creare il processo di importazione. Quando si esegue lo strumento WAImportExport, è sempre necessario specificare un file journal, nonché l'ID della sessione di copia.

## <a name="next-steps"></a>Passaggi successivi

* [Configurazione dello strumento Importazione/Esportazione di Azure](storage-import-export-tool-setup.md)
* [Impostazione di proprietà e metadati durante il processo di importazione](storage-import-export-tool-setting-properties-metadata-import.md)
* [Sample workflow to prepare hard drives for an import job](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md) (Flusso di lavoro campione per preparare i dischi rigidi per un processo di importazione)
* [Quick reference for frequently used commands](storage-import-export-tool-quick-reference.md) (Riferimento rapido per i comandi usati più di frequente) 
* [Reviewing job status with copy log files](storage-import-export-tool-reviewing-job-status-v1.md) (Revisione dello stato dei processi con i file di log di copia)
* [Riparazione di un processo di importazione](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Repairing an export job](storage-import-export-tool-repairing-an-export-job-v1.md) (Riparazione di un processo di esportazione)
* [Risoluzione dei problemi relativi allo strumento Importazione/Esportazione di Azure](storage-import-export-tool-troubleshooting-v1.md)

