---
title: Formato dei file manifesto del servizio Importazione/Esportazione di Azure | Documentazione Microsoft
description: "Informazioni sul formato del file manifesto dell&quot;unità che descrive il mapping tra i BLOB nell&quot;archiviazione BLOB di Azure e i file su un disco in un processo di importazione o esportazione nel servizio Importazione/Esportazione."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f3119e1c-2c25-48ad-8752-a6ed4adadbb0
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: c1857eb94fba13c30e7f07669616f5d0ab9953f4
ms.lasthandoff: 04/25/2017


---

# <a name="azure-importexport-service-manifest-file-format"></a>Formato del file manifesto del servizio Importazione/Esportazione di Azure
Il file manifesto dell'unità descrive il mapping tra i BLOB nell'archiviazione Blob di Azure e i file nell'unità che comprende un processo di importazione o esportazione. Per un'operazione di importazione, il file manifesto viene creato come parte del processo di preparazione dell'unità e viene archiviato nell'unità prima che questa venga inviata al data center di Azure. Durante un'operazione di esportazione, il manifesto viene creato e archiviato nell'unità dal servizio Importazione/Esportazione di Azure.  
  
Per entrambi i processi di importazione ed esportazione, il file manifesto dell'unità viene archiviato nell'unità di importazione o esportazione; non viene trasmesso al servizio tramite un'operazione API.  
  
Di seguito viene descritto il formato generale di un file manifesto dell'unità:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  

```

## <a name="manifest-xml-elements-and-attributes"></a>Elementi e attributi XML del manifesto

Nella tabella seguente vengono specificati gli elementi di dati e gli attributi del formato XML del manifesto dell'unità.  
  
|Elemento XML|Tipo|Descrizione|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Elemento radice|Elemento radice del file manifesto. Tutti gli altri elementi nel file sono al di sotto di questo elemento.|  
|`Version`|Attributo, stringa|Versione del file manifesto.|  
|`Drive`|Elemento XML nidificato|Contiene il manifesto per tutte le unità.|  
|`DriveId`|string|Identificatore di unità univoco per l'unità. L'identificatore di unità viene trovato eseguendo una query all'unità sul numero di serie. Il numero di serie in genere è stampato anche all'esterno dell'unità. L'elemento `DriveID` deve comparire prima di qualsiasi elemento `BlobList` nel file manifesto.|  
|`StorageAccountKey`|string|Necessaria per i processi di importazione se e solo se `ContainerSas` non è specificato. Chiave dell'account di archiviazione di Azure associata al processo.<br /><br /> Nelle operazioni di esportazione questo elemento viene omesso dal manifesto.|  
|`ContainerSas`|string|Necessaria per i processi di importazione se e solo se `StorageAccountKey` non è specificato. Firma di accesso condiviso del contenitore per l'accesso ai BLOB associati al processo. Per il formato, vedere [Jobs](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) (Processi). Nelle operazioni di esportazione questo elemento viene omesso dal manifesto.|  
|`ClientCreator`|String|Specifica il client che ha creato il file XML. Questo valore non viene interpretato dal servizio Importazione/Esportazione.|  
|`BlobList`|Elemento XML nidificato|Contiene un elenco di BLOB che fanno parte del processo di importazione o esportazione. Tutti i BLOB in un elenco di BLOB condividono gli stessi metadati e proprietà.|  
|`BlobList/MetadataPath`|String|Facoltativa. Specifica il percorso relativo di un file sul disco che contiene i metadati predefiniti che verranno impostati nei BLOB presenti nell'elenco per un'operazione di importazione. Questi metadati possono essere sostituiti facoltativamente un BLOB alla volta.<br /><br /> Nelle operazioni di esportazione questo elemento viene omesso dal manifesto.|  
|`BlobList/MetadataPath/@Hash`|Attributo, stringa|Specifica il valore hash MD5 codificato in base 16 per il file di metadati.|  
|`BlobList/PropertiesPath`|string|Facoltativa. Specifica il percorso relativo di un file sul disco che contiene le proprietà predefinite che verranno impostate nei BLOB presenti nell'elenco per un'operazione di importazione. Queste proprietà possono essere sostituite facoltativamente un BLOB alla volta.<br /><br /> Nelle operazioni di esportazione questo elemento viene omesso dal manifesto.|  
|`BlobList/PropertiesPath/@Hash`|Attributo, stringa|Specifica il valore hash MD5 codificato in base 16 per il file delle proprietà.|  
|`Blob`|Elemento XML nidificato|Contiene informazioni su tutti i BLOB in tutti gli elenchi di BLOB.|  
|`Blob/BlobPath`|String|URI relativo al BLOB che inizia con il nome del contenitore. Se il BLOB si trova nel contenitore radice, deve iniziare con `$root`.|  
|`Blob/FilePath`|String|Specifica il percorso relativo verso il file sull'unità. Per i processi di esportazione, il percorso BLOB verrà usato per il percorso del file, se possibile; *ad esempio*, `pictures/bob/wild/desert.jpg` sarà esportato in `\pictures\bob\wild\desert.jpg`. Tuttavia, a causa delle limitazioni sui nomi NTFS, un BLOB può essere esportato in un file con un percorso non simile al percorso del BLOB.|  
|`Blob/ClientData`|string|Facoltativa. Contiene i commenti del cliente. Questo valore non viene interpretato dal servizio Importazione/Esportazione.|  
|`Blob/Snapshot`|DateTime|Opzionale per i processi di esportazione. Specifica l'identificatore dello snapshot per uno snapshot di BLOB esportato.|  
|`Blob/Length`|Integer|Specifica la lunghezza totale del BLOB in byte. Il valore può raggiungere 200 GB per un BLOB in blocchi e 1 TB per un BLOB di pagine. Per un BLOB di pagine, questo valore deve essere multiplo di 512.|  
|`Blob/ImportDisposition`|String|Facoltativo per i processi di importazione, omesso per i processi di esportazione. Specifica in che modo il servizio di Importazione/Esportazione deve comportarsi nel caso di un processo di importazione quando esiste già un BLOB con lo stesso nome. Se questo valore viene omesso dal manifesto di importazione, il valore predefinito è `rename`.<br /><br /> I valori per questo elemento includono:<br /><br /> -   `no-overwrite`: se esiste già un BLOB di destinazione con lo stesso nome, l'operazione di importazione ignora questo file.<br />-   `overwrite`: eventuali BLOB di destinazione esistenti vengono completamente sovrascritti dal nuovo file importato.<br />-   `rename`: il nuovo blob verrà caricato con un nome modificato.<br /><br /> La regola di ridenominazione si presenta come segue:<br /><br /> -   Se il nome del blob non contiene un punto, viene generato un nuovo nome aggiungendo `(2)` al nome del BLOB originale; se il nuovo nome è in conflitto con il nome di un BLOB esistente, viene aggiunto `(3)` al posto di `(2)` e così via.<br />-   Se il nome del BLOB contiene un punto, la parte che segue l'ultimo punto viene considerata come nome dell'estensione. Analogamente alla procedura descritta in alto, `(2)` viene inserito prima dell'ultimo punto per generare un nuovo nome; se il nuovo nome continua a essere in conflitto con il nome di un BLOB esistente, il servizio prova con `(3)`, `(4)` e così via, fino a quando non viene trovato un nome non in conflitto.<br /><br /> Di seguito sono riportati alcuni esempi:<br /><br /> Il BLOB `BlobNameWithoutDot` verrà rinominato in:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> Il BLOB `Seattle.jpg` verrà rinominato in:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Elemento XML nidificato|Obbligatorio per un BLOB di pagine.<br /><br /> Per un'operazione di importazione, specifica un elenco di intervalli di byte di un file da importare. Ogni intervallo di pagine viene descritto da un offset e dalla lunghezza nel file di origine che descrive l'intervallo di pagine, insieme a un hash MD5 dell'area. L'attributo `Hash` di un intervallo di pagine è obbligatorio. Il servizio verifica che l'hash dei dati nel BLOB corrisponda all'hash MD5 calcolato dall'intervallo di pagine. Per descrivere un file per un'importazione, può essere usato qualsiasi numero di intervalli di pagine con una dimensione totale massima di 1 TB. Tutti gli intervalli di pagine devono essere ordinati tramite offset e non sono consentite sovrapposizioni.<br /><br /> Per un'operazione di esportazione, specifica un set di intervalli di byte di un BLOB che sono stati esportati nell'unità.<br /><br /> Tutti gli intervalli di pagine insieme possono coprire solo intervalli secondari di un BLOB o di un file.  La parte rimanente del file non deve essere coperta da intervalli di pagine e il relativo contenuto può essere non definito.|  
|`PageRange`|Elemento XML|Rappresenta un intervallo di pagine.|  
|`PageRange/@Offset`|Attributo, valore integer|Specifica l'offset nel file di trasferimento e il BLOB in cui inizia l'intervallo di pagine specificato. Questo valore deve essere un multiplo di 512.|  
|`PageRange/@Length`|Attributo, valore integer|Specifica la lunghezza dell'intervallo di pagine. Questo valore deve essere un multiplo di 512 e non superiore a 4 MB.|  
|`PageRange/@Hash`|Attributo, stringa|Specifica il valore hash MD5 codificato in base 16 per l'intervallo di date.|  
|`BlockList`|Elemento XML nidificato|Obbligatorio per un BLOB in blocchi con blocchi denominati.<br /><br /> Per un'operazione di importazione, l'elenco di blocchi specifica un set di blocchi che verranno importati nell'archiviazione di Azure. Per un'operazione di esportazione, l'elenco di blocchi specifica il punto in cui ogni blocco è stato archiviato nel file sul disco di esportazione. Ogni blocco viene descritto da un offset nel file e dalla lunghezza del blocco; ogni blocco viene ulteriormente denominato da un attributo ID del blocco e contiene un hash MD5 per il blocco. Per descrivere un BLOB è possibile usare fino a 50.000 blocchi.  Tutti i blocchi devono essere ordinati in base all'offset e nell'insieme devono coprire l'intervallo completo del file, *ovvero*, tra i blocchi non devono essere presenti spazi vuoti. Se il BLOB è non superiore a 64 MB, gli ID del blocco per ogni blocco devono essere tutti presenti o tutti mancanti. Gli ID del blocco devono essere stringhe con codifica Base64. Per gli altri requisiti sugli ID del blocco, vedere [Put Block](/rest/api/storageservices/put-block) (Blocco Put).|  
|`Block`|Elemento XML|Rappresenta un blocco.|  
|`Block/@Offset`|Attributo, valore integer|Specifica l'offset in cui inizia il blocco specificato.|  
|`Block/@Length`|Attributo, valore integer|Specifica il numero di byte nel blocco; questo valore non deve essere superiore a 4 MB.|  
|`Block/@Id`|Attributo, stringa|Specifica una stringa che rappresenta l'ID del blocco per il blocco.|  
|`Block/@Hash`|Attributo, stringa|Specifica l'hash MD5 codificato in base 16 del blocco.|  
|`Blob/MetadataPath`|String|Facoltativa. Specifica il percorso relativo di un file di metadati. Durante l'importazione, i metadati vengono impostati sul BLOB di destinazione. Durante un'operazione di esportazione, i metadati del BLOB vengono archiviati nel file di metadati sull'unità.|  
|`Blob/MetadataPath/@Hash`|Attributo, stringa|Specifica l'hash MD5 codificato in base 16 del file di metadati del BLOB.|  
|`Blob/PropertiesPath`|String|Facoltativa. Specifica il percorso relativo di un file di proprietà. Durante l'importazione, le proprietà vengono impostate sul BLOB di destinazione. Durante un'operazione di esportazione, le proprietà del BLOB vengono archiviate nel file di proprietà sull'unità.|  
|`Blob/PropertiesPath/@Hash`|Attributo, stringa|Specifica l'hash MD5 codificato in base 16 del file di proprietà del BLOB.|  
  
## <a name="next-steps"></a>Passaggi successivi
 
* [Storage Import/Export REST API](/rest/api/storageimportexport/) (API REST di importazione/esportazione dell'archiviazione)

