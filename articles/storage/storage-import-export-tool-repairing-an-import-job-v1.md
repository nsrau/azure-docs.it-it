---
title: Riparazione di un processo di importazione | Documentazione Microsoft
description: "Informazioni sulla modalità di ripristino di un processo di importazione che è stato creato ed eseguito tramite il servizio Importazione/Esportazione."
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
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 5d75bdb5ff39934e02d9af721b51e0aca24026bb
ms.openlocfilehash: 9278256edd5c117de9b0e6b5b12f0ca37c612661


---

# <a name="repairing-an-import-job"></a>Riparazione di un processo di importazione
Il servizio Importazione/Esportazione di Microsoft Azure potrebbe non riuscire a copiare alcuni file o parti di file nel servizio BLOB di Windows Azure. I motivi includono:  
  
-   File corrotti  
  
-   Unità danneggiate  
  
-   Chiave dell'account di archiviazione modificata durante il trasferimento del file.  
  
È possibile eseguire lo strumento Importazione/Esportazione di Microsoft Azure con i file di log di copia del processo. Lo strumento caricherà i file mancanti (o parti di file) nell'account di archiviazione di Azure per completare il processo di importazione.  
  
Il comando per la riparazione di un processo di importazione è **RepairImport**. È possibile specificare i parametri seguenti:  
  
|||  
|-|-|  
|**/r:**<RepairFile\>|**Obbligatorio.** Percorso del file di ripristino che tiene traccia dell'avanzamento del ripristino e consente di riprendere un ripristino interrotto. Ogni unità deve contenere un solo file di ripristino. Quando si avvia un ripristino per una determinata unità, si viene spostati nel percorso di un file di ripristino che non esiste ancora. Per riprendere un ripristino interrotto, è consigliabile modificare il nome di un file di ripristino esistente. Il file di ripristino corrispondente all'unità di destinazione deve essere sempre specificato.|  
|**/logdir:**<LogDirectory\>|**Facoltativo.** Directory dei log, in cui verranno scritti file di log dettagliati. Se non è specificata alcuna directory dei log, verrà usata la directory corrente.|  
|**/d:**<TargetDirectories\>|**Obbligatorio.** Una o più directory separate da punto e virgola contenenti i file originali che sono stati importati. È possibile usare anche l'unità di importazione, ma non è obbligatorio se sono disponibili posizioni alternative per i file originali.|  
|**/bk:**<BitLockerKey\>|**Facoltativo.** È necessario specificare la chiave BitLocker se si desidera che lo strumento sblocchi un'unità crittografata in cui sono disponibili i file originali.|  
|**/sn:**<StorageAccountName\>|**Obbligatorio.** Nome dell'account di archiviazione per il processo di importazione.|  
|**/sk:**<StorageAccountKey\>|**Obbligatorio** solo se non è specificata una firma di accesso condiviso del contenitore. Chiave dell'account per l'account di archiviazione per il processo di importazione.|  
|**/csas:**<ContainerSas\>|**Obbligatorio** solo se non è specificata la chiave dell'account di archiviazione. Firma di accesso condiviso del contenitore per l'accesso ai BLOB associati al processo di importazione.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Obbligatorio.** Percorso del file dei log di copia dell'unità (log dettagliato o log di errore). Il file viene generato dal servizio di Importazione/Esportazione di Azure e può essere scaricato dall'archiviazione BLOB associata al processo. Il file dei log di copia contiene informazioni sui BLOB non riusciti o i file da ripristinare.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Facoltativo.** Percorso di un file di testo che può essere usato per risolvere le ambiguità, se si dispone di più file con lo stesso nome importati nello stesso processo. La prima volta che viene eseguito lo strumento, è possibile popolare questo file con tutti i nomi ambigui. Le esecuzioni successive dello strumento useranno questo file per risolvere le ambiguità.|  
  
## <a name="using-the-repairimport-command"></a>Uso del comando RepairImport  
Per ripristinare i dati di importazione tramite flusso di dati in rete, è necessario specificare le directory contenenti i file originali importati usando il parametro `/d`. È anche necessario specificare il file di log di copia scaricato dall'account di archiviazione. Una tipica riga di comando per ripristinare un processo di importazione con errori parziali è:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
Di seguito è riportato un esempio di file di log di copia. In questo caso una parte da 64 K di un file era corrotta nell'unità inviata per il processo di importazione. Poiché si tratta dell'unico errore indicato, il resto dei BLOB nel processo sono stati importati.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status=”CompletedWithErrors”>  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status=”Overwritten”>overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset=”65536” Length=”65536” Hash=”AA2585F6F6FD01C4AD4256E018240CD4” Status=”Corrupted” />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Quando questo log di copia viene passato allo strumento Importazione/Esportazione di Azure, lo strumento tenterà di completare l'importazione del file copiando i contenuti mancanti in rete. Come nell'esempio precedente lo strumento cercherà il file originale `\animals\koala.jpg` all'interno delle due directory `C:\Users\bob\Pictures` e `X:\BobBackup\photos`. Se il file `C:\Users\bob\Pictures\animals\koala.jpg` esiste, lo strumento Importazione/Esportazione di Azure copierà l'intervallo di dati mancante nel BLOB corrispondente `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Risoluzione dei conflitti quando si usa RepairImport  
In alcune situazioni lo strumento potrebbe non essere in grado di trovare o aprire il file necessario per uno dei motivi seguenti: è impossibile trovare il file, il file non è accessibile, il nome del file è ambiguo oppure il contenuto del file non è più corretto.  
  
Potrebbe verificarsi un errore di ambiguità se lo strumento tenta di individuare `\animals\koala.jpg` ed esiste un file con lo stesso nome sia in `C:\Users\bob\pictures` sia in `X:\BobBackup\photos`. Ovvero sia `C:\Users\bob\pictures\animals\koala.jpg` che `X:\BobBackup\photos\animals\koala.jpg` esistono nelle unità del processo di importazione.  
  
L'opzione `/PathMapFile` consente di risolvere questi errori. È possibile specificare il nome del file che conterrà l'elenco di file che lo strumento non è stato in grado di identificare in modo corretto. Una riga di comando di esempio che potrebbe popolare `9WM35C2V_pathmap.txt` è riportata di seguito:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Lo strumento scriverà quindi i percorsi dei file problematici in `9WM35C2V_pathmap.txt`, uno per ogni riga. Ad esempio, dopo l'esecuzione del comando il file potrebbe contenere le voci seguenti:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Per ogni file nell'elenco, è consigliabile tentare di individuare e aprire il file per assicurarsi che sia disponibile per lo strumento. Se si desidera indicare in modo esplicito allo strumento dove trovare un file, è possibile modificare il file di mappa del percorso e aggiungere i percorsi di tutti i file nella stessa riga, separati da un carattere di tabulazione:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Dopo aver reso disponibili i file necessari allo strumento o aver aggiornato il file di mappa del percorso, è possibile eseguire nuovamente lo strumento per completare il processo di importazione.  
  
## <a name="see-also"></a>Vedere anche  
[Configurazione dello strumento Importazione/Esportazione di Azure](storage-import-export-tool-setup-v1.md)   
[Preparing hard drives for an import job](storage-import-export-tool-preparing-hard-drives-import-v1.md)  (Preparazione dei dischi rigidi per un processo di importazione)  
[Reviewing job status with copy log files](storage-import-export-tool-reviewing-job-status-v1.md)  (Revisione dello stato dei processi con i file di log di copia)  
[Repairing an export job](storage-import-export-tool-repairing-an-export-job-v1.md)  (Riparazione di un processo di esportazione)  
[Risoluzione dei problemi relativi allo strumento di importazione/esportazione di Azure](storage-import-export-tool-troubleshooting-v1.md)



<!--HONumber=Dec16_HO2-->


