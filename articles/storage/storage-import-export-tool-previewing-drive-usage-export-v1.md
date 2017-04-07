---
title: Anteprima dell&quot;uso del disco per un processo di esportazione in Importazione/Esportazione di Azure - versione 1 | Documentazione Microsoft
description: Informazioni su come visualizzare in anteprima l&quot;elenco dei BLOB selezionati per un processo di esportazione nel servizio Importazione/Esportazione di Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 7707d744-7ec7-4de8-ac9b-93a18608dc9a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 7bf74247090f91e17f81a9bc98ebfa78334c8c10
ms.lasthandoff: 03/30/2017


---

# <a name="previewing-drive-usage-for-an-export-job"></a>Anteprima dell'uso del disco per un processo di esportazione
Prima di creare un processo di esportazione, è necessario scegliere un set di BLOB da esportare. Il servizio Importazione/Esportazione di Microsoft Azure consente di usare un elenco di percorsi o prefissi BLOB per rappresentare i BLOB selezionati.  
  
È poi necessario determinare il numero di unità necessarie per l'invio. Lo strumento Importazione/Esportazione consente di usare il comando `PreviewExport` per visualizzare in anteprima l'uso delle unità per i BLOB selezionati, in base alla dimensione delle unità che si intende usare.

## <a name="command-line-parameters"></a>Parametri della riga di comando

Quando si usa il comando `PreviewExport` dello strumento Importazione/Esportazione, è possibile usare i parametri seguenti.

|Parametro della riga di comando|Descrizione|  
|--------------------------|-----------------|  
|**/logdir:**<DirectoryLog\>|Facoltativa. Directory dei log. in cui verranno scritti file di log dettagliati. Se non è specificata alcuna directory dei log, verrà usata la directory corrente.|  
|**/sn:**<NomeAccountArchiviazione\>|Obbligatorio. Nome dell'account di archiviazione per il processo di esportazione.|  
|**/sk:**<ChiaveAccountArchiviazione\>|Obbligatorio solo se non è specificata una firma di accesso condiviso del contenitore. Chiave dell'account per l'account di archiviazione per il processo di esportazione.|  
|**/csas:**<FirmaAccessoCondivisoContenitore\>|Obbligatorio solo se non è specificata una chiave dell'account di archiviazione. Firma di accesso condiviso del contenitore per l'elenco dei BLOB da esportare nel processo di esportazione.|  
|**/ExportBlobListFile:**<FileElencoBlobEsportazione\>|Obbligatorio. Percorso del file XML contenente l'elenco dei percorsi o dei prefissi dei percorsi BLOB per i BLOB da esportare. Formato di file usato nell'elemento `BlobListBlobPath` nell'operazione [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) dell'API REST del servizio Importazione/Esportazione.|  
|**/DriveSize:**<DimensioneUnità\>|Obbligatorio. Dimensione delle unità da usare per un processo di esportazione, *ad es.* 500 GB, 1,5 TB.|  

## <a name="command-line-example"></a>Esempio di riga di comando

L'esempio seguente illustra il comando `PreviewExport`:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Il file dell'elenco del BLOB di esportazione può contenere nomi e prefissi BLOB, come illustrato di seguito:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Lo strumento Importazione/Esportazione di Azure elenca tutti i BLOB da esportare e calcola come comprimerli in unità della dimensione specificata, tenendo conto dell'eventuale sovraccarico necessario, quindi stima il numero di unità richieste per contenere i BLOB e le informazioni sull'uso delle unità.  
  
Di seguito è riportato un esempio dell'output, senza log informativi:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```  
  
## <a name="next-steps"></a>Passaggi successivi

* [Azure Import/Export Tool Reference](storage-import-export-tool-how-to-v1.md) (Informazioni di riferimento sullo strumento Importazione/Esportazione di Azure)

