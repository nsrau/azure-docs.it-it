---
title: Formato dei file di log del servizio Importazione/Esportazione | Documentazione Microsoft
description: Altre informazioni sul formato dei file di log creati quando vengono eseguiti i passaggi per un processo del servizio Importazione/Esportazione.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 38cc16bd-ad55-4625-9a85-e1726c35fd1b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 16234ccaf13ce1d85cfd207ed4734e683070faa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-importexport-service-log-file-format"></a>Formato dei file di log del servizio Importazione/Esportazione di Azure
Quando il servizio Importazione/Esportazione di Microsoft Azure esegue un'azione in un'unità come parte di un processo di importazione o di esportazione, i log vengono scritti in BLOB in blocchi nell'account di archiviazione associato a tale processo.  
  
Esistono due log che possono essere scritti dal servizio Importazione/Esportazione:  
  
-   Il log degli errori viene sempre generato in caso di errore.  
  
-   Il log dettagliato non è abilitato per impostazione predefinita, ma può essere abilitato impostando la proprietà `EnableVerboseLog` in un'operazione di tipo [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) o [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update).  
  
## <a name="log-file-location"></a>Posizione dei file di log  
I log vengono scritti in BLOB in blocchi nel contenitore o nella directory virtuale specificata tramite l'impostazione `ImportExportStatesPath`, che è possibile impostare in un'operazione di tipo `Put Job`. La posizione in cui vengono scritti i log dipende da come viene specificata l'autenticazione per il processo e dal valore specificato per `ImportExportStatesPath`. L'autenticazione per il processo può essere specificata tramite una chiave dell'account di archiviazione o la firma di accesso condiviso di un contenitore.  
  
Il nome del contenitore o della directory virtuale può essere il nome predefinito di `waimportexport` oppure il nome di un altro contenitore o di un'altra directory virtuale specificato dall'utente.  
  
Nella tabella seguente vengono illustrate le opzioni possibili:  
  
|Metodo di autenticazione|Valore dell'elemento `ImportExportStatesPath`|Posizione dei BLOB di log|  
|---------------------------|----------------------------------------------|---------------------------|  
|Chiave dell'account di archiviazione|Valore predefinito|Un contenitore denominato `waimportexport`, ovvero il contenitore predefinito. Ad esempio:<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Chiave dell'account di archiviazione|Valore specificato dall'utente|Un contenitore denominato dall'utente. Ad esempio:<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|Firma di accesso condiviso del contenitore|Valore predefinito|Una directory virtuale denominata `waimportexport`, ovvero il nome predefinito, sotto il contenitore specificato nella firma di accesso condiviso.<br /><br /> Ad esempio, se la firma di accesso condiviso specificata per il processo è `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, la posizione del log sarà `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`|  
|Firma di accesso condiviso del contenitore|Valore specificato dall'utente|Una directory virtuale denominata dall'utente, sotto il contenitore specificato nella firma di accesso condiviso.<br /><br /> Ad esempio, se la firma di accesso condiviso specificata per il processo è `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue` e la directory virtuale specificata è denominata `mylogblobs`, la posizione del log sarà `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
È possibile recuperare l'URL per l'errore e i log dettagliati chiamando l'operazione [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate). I log sono disponibili al termine dell'elaborazione dell'unità.  
  
## <a name="log-file-format"></a>Formato di file dei log  
Il formato è lo stesso per entrambi i log: un BLOB che contiene le descrizioni XML degli eventi che si sono verificati durante la copia dei BLOB tra il disco rigido e l'account del cliente.  
  
Il log dettagliato contiene informazioni complete sullo stato dell'operazione di copia per ogni BLOB (per un processo di importazione) o file (per un processo di esportazione), mentre il log degli errori contiene solo le informazioni per i BLOB o i file che hanno rilevato errori durante il processo di importazione o esportazione.  
  
Di seguito è mostrato il formato di log dettagliato. Il log degli errori presenta la stessa struttura, ma esclude le operazioni riuscite.  

```xml
<DriveLog Version="2014-11-01">  
  <DriveId>drive-id</DriveId>  
  [<Blob Status="blob-status">  
   <BlobPath>blob-path</BlobPath>  
   <FilePath>file-path</FilePath>  
   [<Snapshot>snapshot</Snapshot>]  
   <Length>length</Length>  
   [<LastModified>last-modified</LastModified>]  
   [<ImportDisposition Status="import-disposition-status">import-disposition</ImportDisposition>]  
   [page-range-list-or-block-list]  
   [metadata-status]  
   [properties-status]  
  </Blob>]  
  [<Blob>  
    . . .  
  </Blob>]  
  <Status>drive-status</Status>  
</DriveLog>  
  
page-range-list-or-block-list ::= 
  page-range-list | block-list  
  
page-range-list ::=   
<PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
</PageRangeList>  
  
block-list ::=  
<BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       [Hash="md5-hash"] Status="block-status"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       [Hash="md5-hash"] Status="block-status"/>]  
</BlockList>  
  
metadata-status ::=  
<Metadata Status="metadata-status">  
   [<GlobalPath Hash="md5-hash">global-metadata-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">metadata-file-path</Path>]  
</Metadata>  
  
properties-status ::=  
<Properties Status="properties-status">  
   [<GlobalPath Hash="md5-hash">global-properties-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">properties-file-path</Path>]  
</Properties>  
```

Nella tabella seguente vengono illustrati gli elementi del file di log.  
  
|Elemento XML|Tipo|Descrizione|  
|-----------------|----------|-----------------|  
|`DriveLog`|Elemento XML|Rappresenta un log di unità.|  
|`Version`|Attributo, stringa|Versione del formato del log.|  
|`DriveId`|string|Numero di serie dell'hardware dell'unità.|  
|`Status`|String|Stato di elaborazione dell'unità. Per altre informazioni, vedere la tabella `Drive Status Codes` di seguito.|  
|`Blob`|Elemento XML nidificato|Rappresenta un BLOB.|  
|`Blob/BlobPath`|string|L'URI del BLOB.|  
|`Blob/FilePath`|String|Il percorso relativo verso il file nell'unità.|  
|`Blob/Snapshot`|DateTime|La versione dello snapshot del BLOB, solo per un processo di esportazione.|  
|`Blob/Length`|Integer|La lunghezza totale dei BLOB in byte.|  
|`Blob/LastModified`|DateTime|Data e ora dell'ultima modifica al BLOB, solo per un processo di esportazione.|  
|`Blob/ImportDisposition`|String|La disposizione di importazione del BLOB, solo per un processo di importazione.|  
|`Blob/ImportDisposition/@Status`|Attributo, stringa|Lo stato della disposizione di importazione.|  
|`PageRangeList`|Elemento XML nidificato|Rappresenta un elenco di intervalli di pagine per un BLOB di pagine.|  
|`PageRange`|Elemento XML|Rappresenta un intervallo di pagine.|  
|`PageRange/@Offset`|Attributo, valore integer|L'offset iniziale dell'intervallo di pagine nel BLOB.|  
|`PageRange/@Length`|Attributo, valore integer|La lunghezza in byte dell'intervallo di pagine.|  
|`PageRange/@Hash`|Attributo, stringa|L'hash MD5 codificato in base 16 dell'intervallo di pagine.|  
|`PageRange/@Status`|Attributo, stringa|Lo stato di elaborazione dell'intervallo di pagine.|  
|`BlockList`|Elemento XML nidificato|Rappresenta un elenco di blocchi per un BLOB in blocchi.|  
|`Block`|Elemento XML|Rappresenta un blocco.|  
|`Block/@Offset`|Attributo, valore integer|L'offset iniziale del blocco nel BLOB.|  
|`Block/@Length`|Attributo, valore integer|La lunghezza in byte del blocco.|  
|`Block/@Id`|Attributo, stringa|L'ID del blocco.|  
|`Block/@Hash`|Attributo, stringa|L'hash MD5 codificato in base 16 del blocco.|  
|`Block/@Status`|Attributo, stringa|Lo stato di elaborazione del blocco.|  
|`Metadata`|Elemento XML nidificato|Rappresenta i metadati del BLOB.|  
|`Metadata/@Status`|Attributo, stringa|Lo stato di elaborazione dei metadati del BLOB.|  
|`Metadata/GlobalPath`|String|Il percorso relativo verso il file di metadati globale.|  
|`Metadata/GlobalPath/@Hash`|Attributo, stringa|L'hash MD5 codificato in base 16 del file di metadati globale.|  
|`Metadata/Path`|String|Il percorso relativo verso il file di metadati.|  
|`Metadata/Path/@Hash`|Attributo, stringa|L'hash MD5 codificato in base 16 del file di metadati.|  
|`Properties`|Elemento XML nidificato|Rappresenta le proprietà del BLOB.|  
|`Properties/@Status`|Attributo, stringa|Lo stato di elaborazione delle proprietà del BLOB, ad esempio file non trovato, completato e così via.|  
|`Properties/GlobalPath`|String|Il percorso relativo verso il file di proprietà globale.|  
|`Properties/GlobalPath/@Hash`|Attributo, stringa|L'hash MD5 codificato in base 16 del file di proprietà globale.|  
|`Properties/Path`|String|Il percorso relativo verso il file di proprietà.|  
|`Properties/Path/@Hash`|Attributo, stringa|L'hash MD5 codificato in base 16 del file di proprietà.|  
|`Blob/Status`|String|Lo stato di elaborazione del BLOB.|  
  
# <a name="drive-status-codes"></a>Codici di stato per un'unità  
Nella tabella seguente sono elencati i codici di stato per l'elaborazione di un'unità.  
  
|Codice di stato|Descrizione|  
|-----------------|-----------------|  
|`Completed`|L'unità ha completato l'elaborazione senza errori.|  
|`CompletedWithWarnings`|L'unità ha completato l'elaborazione con avvisi di uno o più BLOB in base alle disposizioni di importazione specificate per i BLOB.|  
|`CompletedWithErrors`|L'unità ha completato l'elaborazione con errori in uno o più BLOB o blocchi.|  
|`DiskNotFound`|Nessun disco trovato nell'unità.|  
|`VolumeNotNtfs`|Il primo volume di dati sul disco non è in formato NTFS.|  
|`DiskOperationFailed`|Si è verificato un errore sconosciuto durante l'esecuzione di operazioni sull'unità.|  
|`BitLockerVolumeNotFound`|Non è stato trovato nessun volume BitLocker crittografabile.|  
|`BitLockerNotActivated`|Nel volume non è abilitato BitLocker.|  
|`BitLockerProtectorNotFound`|La protezione con chiave con password numerica non esiste nel volume.|  
|`BitLockerKeyInvalid`|La password numerica specificata non può sbloccare il volume.|  
|`BitLockerUnlockVolumeFailed`|Si è verificato un errore sconosciuto durante il tentativo di sbloccare il volume.|  
|`BitLockerFailed`|Si è verificato un errore sconosciuto durante l'esecuzione di operazioni BitLocker.|  
|`ManifestNameInvalid`|Il nome del file manifesto non è valido.|  
|`ManifestNameTooLong`|Il nome del file manifesto è troppo lungo.|  
|`ManifestNotFound`|Il file manifesto non è stato trovato.|  
|`ManifestAccessDenied`|L'accesso al file manifesto è stato negato.|  
|`ManifestCorrupted`|Il file manifesto è danneggiato (il contenuto non corrisponde all'hash).|  
|`ManifestFormatInvalid`|Il contenuto del manifesto non è conforme al formato richiesto.|  
|`ManifestDriveIdMismatch`|L'ID unità nel file manifesto non corrisponde a quello letto dall'unità.|  
|`ReadManifestFailed`|Si è verificato un errore di I/O durante la lettura dal manifesto.|  
|`BlobListFormatInvalid`|Il BLOB dell'elenco di BLOB di esportazione non è conforme al formato richiesto.|  
|`BlobRequestForbidden`|Non è consentito l'accesso ai BLOB nell'account di archiviazione. Ciò potrebbe essere dovuto al fatto che la chiave dell'account di archiviazione o la firma di accesso condiviso del contenitore non sono validi.|  
|`InternalError`|Si è verificato un errore interno durante l'elaborazione dell'unità.|  
  
## <a name="blob-status-codes"></a>Codici di stato per un BLOB  
Nella tabella seguente sono elencati i codici di stato per l'elaborazione di un BLOB.  
  
|Codice di stato|Descrizione|  
|-----------------|-----------------|  
|`Completed`|Il BLOB ha completato l'elaborazione senza errori.|  
|`CompletedWithErrors`|Il BLOB ha completato l'elaborazione con errori in uno o più intervalli di pagine o blocchi, metadati o proprietà.|  
|`FileNameInvalid`|Il nome del file non è valido.|  
|`FileNameTooLong`|Il nome del file è troppo lungo.|  
|`FileNotFound`|Il file non è stato trovato.|  
|`FileAccessDenied`|L'accesso al file è stato negato.|  
|`BlobRequestFailed`|La richiesta del servizio BLOB di accedere al BLOB ha avuto esito negativo.|  
|`BlobRequestForbidden`|La richiesta del servizio BLOB di accedere al BLOB è vietata. Ciò potrebbe essere dovuto al fatto che la chiave dell'account di archiviazione o la firma di accesso condiviso del contenitore non sono validi.|  
|`RenameFailed`|Impossibile rinominare il BLOB (per un processo di importazione) o il file (per un processo di esportazione).|  
|`BlobUnexpectedChange`|Si è verificato un cambiamento imprevisto nel BLOB (per un processo di esportazione).|  
|`LeasePresent`|È presente un lease nel BLOB.|  
|`IOFailed`|Si è verificato un errore di I/O nel disco o nella rete durante l'elaborazione del BLOB.|  
|`Failed`|Si è verificato un errore sconosciuto durante l'elaborazione del BLOB.|  
  
## <a name="import-disposition-status-codes"></a>Codici di stato per una disposizione di importazione  
Nella tabella seguente sono elencati i codici di stato per la risoluzione di una disposizione di importazione.  
  
|Codice di stato|Descrizione|  
|-----------------|-----------------|  
|`Created`|Il BLOB è stato creato.|  
|`Renamed`|Il BLOB è stato rinominato in base alla disposizione di importazione di ridenominazione. L'elemento `Blob/BlobPath` contiene l'URI per il BLOB rinominato.|  
|`Skipped`|Il BLOB è stato ignorato in base alla disposizione di importazione `no-overwrite`.|  
|`Overwritten`|Il BLOB ha sovrascritto un BLOB esistente in base alla disposizione di importazione `overwrite`.|  
|`Cancelled`|Un errore precedente ha arrestato un'ulteriore elaborazione della disposizione di importazione.|  
  
## <a name="page-rangeblock-status-codes"></a>Codici di stato per un intervallo/blocco di pagine  
Nella tabella seguente sono elencati i codici di stato per l'elaborazione di un intervallo di pagine o un blocco.  
  
|Codice di stato|Descrizione|  
|-----------------|-----------------|  
|`Completed`|L'intervallo di pagine o il blocco ha completato l'elaborazione senza errori.|  
|`Committed`|È stato eseguito il commit del blocco, ma non per l'intero elenco di blocchi, dal momento che altri blocchi o l'intero elenco di blocchi hanno avuto esito negativo.|  
|`Uncommitted`|Il blocco è stato caricato ma non è stato eseguito il commit.|  
|`Corrupted`|L'intervallo di pagine o il blocco è danneggiato (il contenuto non corrisponde all'hash).|  
|`FileUnexpectedEnd`|È stata rilevata una fine imprevista del file.|  
|`BlobUnexpectedEnd`|È stata rilevata una fine imprevista del BLOB.|  
|`BlobRequestFailed`|La richiesta del servizio BLOB di accedere all'intervallo di pagine o al blocco ha avuto esito negativo.|  
|`IOFailed`|Si è verificato un errore di I/O nel disco o nella rete durante l'elaborazione dell'intervallo di pagine o del blocco.|  
|`Failed`|Si è verificato un errore sconosciuto durante l'elaborazione dell'intervallo di pagine o del blocco.|  
|`Cancelled`|Un errore precedente ha arrestato un'ulteriore elaborazione dell'intervallo di pagine o del blocco.|  
  
## <a name="metadata-status-codes"></a>Codici di stato per i metadati  
Nella tabella seguente sono elencati i codici di stato per l'elaborazione dei metadati del BLOB.  
  
|Codice di stato|Descrizione|  
|-----------------|-----------------|  
|`Completed`|I metadati hanno completato l'elaborazione senza errori.|  
|`FileNameInvalid`|Il nome del file di metadati non è valido.|  
|`FileNameTooLong`|Il nome del file di metadati è troppo lungo.|  
|`FileNotFound`|Il nome del file di metadati non è stato trovato.|  
|`FileAccessDenied`|L'accesso al file di metadati è stato negato.|  
|`Corrupted`|Il file di metadati è danneggiato (il contenuto non corrisponde all'hash).|  
|`XmlReadFailed`|Il contenuto dei metadati non è conforme al formato richiesto.|  
|`XmlWriteFailed`|La scrittura dell'elemento XML dei metadati ha avuto esito negativo.|  
|`BlobRequestFailed`|La richiesta del servizio BLOB di accedere ai metadati ha avuto esito negativo.|  
|`IOFailed`|Si è verificato un errore di I/O nel disco o nella rete durante l'elaborazione dei metadati.|  
|`Failed`|Si è verificato un errore sconosciuto durante l'elaborazione dei metadati.|  
|`Cancelled`|Un errore precedente ha arrestato un'ulteriore elaborazione dei metadati.|  
  
## <a name="properties-status-codes"></a>Codici di stato delle proprietà  
Nella tabella seguente sono elencati i codici di stato per l'elaborazione delle proprietà del BLOB.  
  
|Codice di stato|Descrizione|  
|-----------------|-----------------|  
|`Completed`|Le proprietà hanno completato l'elaborazione senza errori.|  
|`FileNameInvalid`|Il nome del file di proprietà non è valido.|  
|`FileNameTooLong`|Il nome del file di proprietà è troppo lungo.|  
|`FileNotFound`|Il file di proprietà non è stato trovato.|  
|`FileAccessDenied`|L'accesso al file di proprietà è stato negato.|  
|`Corrupted`|Il file di proprietà è danneggiato (il contenuto non corrisponde all'hash).|  
|`XmlReadFailed`|Il contenuto delle proprietà non è conforme al formato richiesto.|  
|`XmlWriteFailed`|La scrittura dell'elemento XML delle proprietà ha avuto esito negativo.|  
|`BlobRequestFailed`|La richiesta del servizio BLOB di accedere alle proprietà ha avuto esito negativo.|  
|`IOFailed`|Si è verificato un errore di I/O nel disco o nella rete durante l'elaborazione delle proprietà.|  
|`Failed`|Si è verificato un errore sconosciuto durante l'elaborazione delle proprietà.|  
|`Cancelled`|Un errore precedente ha arrestato un'ulteriore elaborazione delle proprietà.|  
  
## <a name="sample-logs"></a>Esempi di log  
Di seguito è riportato un esempio di log dettagliato.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV123456</DriveId>  
    <Blob Status="Completed">  
       <BlobPath>pictures/bob/wild/desert.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\wild\desert.jpg</FilePath>  
       <Length>98304</Length>  
       <ImportDisposition Status="Created">overwrite</ImportDisposition>  
       <BlockList>  
          <Block Offset="0" Length="65536" Id="AAAAAA==" Hash=" 9C8AE14A55241F98533C4D80D85CDC68" Status="Completed"/>  
          <Block Offset="65536" Length="32768" Id="AQAAAA==" Hash=" DF54C531C9B3CA2570FDDDB3BCD0E27D" Status="Completed"/>  
       </BlockList>  
       <Metadata Status="Completed">  
          <GlobalPath Hash=" E34F54B7086BCF4EC1601D056F4C7E37">\Users\bob\Pictures\wild\metadata.xml</GlobalPath>  
       </Metadata>  
    </Blob>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="0" Length="65536" Hash="19701B8877418393CB3CB567F53EE225" Status="Completed"/>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
          <PageRange Offset="131072" Length="4096" Hash="9BA552E1C3EEAFFC91B42B979900A996" Status="Completed"/>  
       </PageRangeList>  
       <Properties Status="Completed">  
          <Path Hash="38D7AE80653F47F63C0222FEE90EC4E7">\Users\bob\Pictures\animals\koala.jpg.properties</Path>  
       </Properties>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Di seguito è riportato il log degli errori corrispondente.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV6965824</DriveId>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
       </PageRangeList>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

 Il log degli errori seguente per un processo di importazione contiene un errore relativo a un file non trovato nell'unità di importazione. Si noti che lo stato dei componenti successivi è `Cancelled`.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="FileNotFound">  
    <BlobPath>pictures/animals/koala.jpg</BlobPath>  
    <FilePath>\animals\koala.jpg</FilePath>  
    <Length>30310</Length>  
    <ImportDisposition Status="Cancelled">rename</ImportDisposition>  
    <BlockList>  
      <Block Offset="0" Length="6062" Id="MD5/cAzn4h7VVSWXf696qp5Uaw==" Hash="700CE7E21ED55525977FAF7AAA9E546B" Status="Cancelled" />  
      <Block Offset="6062" Length="6062" Id="MD5/PEnGwYOI8LPLNYdfKr7kAg==" Hash="3C49C6C18388F0B3CB35875F2ABEE402" Status="Cancelled" />  
      <Block Offset="12124" Length="6062" Id="MD5/FG4WxqfZKuUWZ2nGTU2qVA==" Hash="146E16C6A7D92AE5166769C64D4DAA54" Status="Cancelled" />  
      <Block Offset="18186" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
      <Block Offset="24248" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

Il log degli errori seguente per un processo di esportazione indica che il contenuto del BLOB è stato scritto correttamente nell'unità, ma si è verificato un errore durante l'esportazione le proprietà del BLOB.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C3U</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
    <FilePath>\pictures\wild\canyon.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList />  
    <Properties Status="Failed" />  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
## <a name="next-steps"></a>Passaggi successivi
 
* [Storage Import/Export REST API](/rest/api/storageimportexport/) (API REST di importazione/esportazione dell'archiviazione)
