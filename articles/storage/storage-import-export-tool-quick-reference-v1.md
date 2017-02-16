---
title: Guida di riferimento rapido per i comandi per il processo di importazione dello strumento di importazione/esportazione di Azure | Documentazione Microsoft
description: Guida di riferimento per i comandi dello strumento di importazione/esportazione di Azure utilizzati di frequente per i processi di importazione. Si riferisce alla versione 1 dello strumento di importazione/esportazione.
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
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 0c58a94a553a22ac06bfdfd8032879f4a4a87fe5
ms.openlocfilehash: e1c440ee165d148b59f29035b853cd8e13a44e7c


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Guida di riferimento rapido per i comandi di uso frequente per i processi di importazione
In questa sezione vengono forniti riferimenti rapidi per alcuni comandi utilizzati di frequente. Per informazioni dettagliate sull'uso, vedere [Preparazione dei dischi rigidi per un processo di importazione](storage-import-export-tool-preparing-hard-drives-import-v1.md).  

## <a name="prepare-the-disks-when-data-already-copied-to-the-disks"></a>Preparare i dischi quando i dati sono già copiati nei dischi
 Ecco un comando di esempio per preparare un disco quando i dati sono già copiati nel disco rigido non ancora crittografato con BitLocker:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Copiare una singola directory in un disco rigido  
 Ecco un comando di esempio per copiare una singola directory di origine in un disco rigido non ancora crittografato con BitLocker:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-wwo-directories-to-a-hard-drive"></a>Copiare due directory in un disco rigido  
 Per copiare due directory di origine in un'unità, sarà necessario usare due comandi.  
  
 Il primo comando specifica la directory di registro, la chiave dell'account di archiviazione, lettera dell'unità di destinazione e i requisiti `format/encrypt`, nonché i parametri comuni:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 Il secondo comando specifica il file journal, un nuovo ID di sessione e i percorsi di origine e di destinazione:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Copiare un file di grandi dimensioni in un disco rigido in una seconda sessione di copia  
 Ecco un comando di esempio che copia un singolo file di grandi dimensioni in un'unità che è stata preparata in una sessione di copia precedente:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="see-also"></a>Vedere anche  
 [Flusso di lavoro di esempio per preparare i dischi rigidi per un processo di importazione](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)



<!--HONumber=Dec16_HO3-->


