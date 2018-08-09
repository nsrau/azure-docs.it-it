---
title: Informazioni di riferimento rapido sui comandi per i processi di importazione dello strumento Importazione/Esportazione di Azure - versione 1 | Documentazione Microsoft
description: Guida di riferimento per i comandi dello strumento Importazione/Esportazione di Azure usati di frequente per i processi di importazione. Si riferisce alla versione 1 dello strumento Importazione/Esportazione.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 16e0fa6f7336b39f63b00564d37c1be308bebb16
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526284"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Informazioni di riferimento rapido sui comandi di uso frequente per i processi di importazione
Questa sezione contiene informazioni di riferimento rapido su alcuni comandi di uso frequente. Per informazioni dettagliate sull'uso, vedere [Preparazione dei dischi rigidi per un processo di importazione](../storage-import-export-tool-preparing-hard-drives-import-v1.md).  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>Preparare un disco rigido quando i dati sono già stati copiati nel disco rigido
 Il comando seguente consente di preparare un disco rigido quando i dati sono già stati copiati al suo interno ma non sono ancora stati crittografati con BitLocker:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Copiare una singola directory in un disco rigido  
 Il comando seguente consente di copiare una singola directory di origine in un disco rigido non ancora crittografato con BitLocker:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>Copiare due directory in un disco rigido  
 Per copiare due directory di origine in un'unità, usare i comandi seguenti:  
  
 Il primo comando specifica la directory di log, la chiave dell'account di archiviazione, la lettera dell'unità di destinazione e i requisiti `format/encrypt`, oltre ai parametri comuni:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 Il secondo comando specifica il file journal, un nuovo ID di sessione e i percorsi di origine e di destinazione:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Copiare un file di grandi dimensioni in un disco rigido in una seconda sessione di copia  
 Il comando seguente consente di copiare un singolo file di grandi dimensioni in un'unità preparata in una sessione di copia precedente:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>Passaggi successivi

* [Sample workflow to prepare hard drives for an import job](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md) (Flusso di lavoro campione per preparare i dischi rigidi per un processo di importazione)
