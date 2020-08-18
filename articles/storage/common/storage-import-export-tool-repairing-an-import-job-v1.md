---
title: Riparazione di un processo di importazione in Importazione/Esportazione di Azure - versione 1 | Documentazione Microsoft
description: Informazioni su come ripristinare un processo di importazione che è stato creato ed eseguito usando il servizio Importazione/Esportazione di Azure.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: b3ba9844bf8412e169322fd4953cbc259a94e174
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525776"
---
# <a name="repairing-an-import-job"></a>Riparazione di un processo di importazione
Il servizio Importazione/Esportazione di Microsoft Azure potrebbe non riuscire a copiare alcuni file o parti di file nel servizio BLOB di Windows Azure. I motivi includono:  
  
-   File corrotti  
  
-   Unità danneggiate  
  
-   Chiave dell'account di archiviazione modificata durante il trasferimento del file.  
  
È possibile eseguire lo strumento Importazione/Esportazione di Microsoft Azure con i file di log di copia del processo di importazione. Per completare il processo di importazione, lo strumento carica i file mancanti o parti di un file nell'account di archiviazione di Microsoft Azure.  
  
## <a name="repairimport-parameters"></a>Parametri di RepairImport

È possibile specificare i parametri seguenti con **RepairImport**: 
  
|||  
|-|-|  
|**/r:**<fileripristino\>|**Obbligatoria.** Percorso del file di ripristino che tiene traccia dell'avanzamento del ripristino e consente di riprendere un ripristino interrotto. Ogni unità deve contenere un solo file di ripristino. Quando si avvia un ripristino per una determinata unità, passare il percorso di un file di ripristino che non esiste ancora. Per riprendere un ripristino interrotto, è consigliabile inserire il nome di un file di ripristino esistente. Specificare sempre il file di ripristino corrispondente all'unità di destinazione.|  
|**/LogDir:**<LogDirectory\>|**Opzionale.** Directory dei log. in cui vengono scritti file di log dettagliati. Se non è specificata alcuna directory dei log, viene usata la directory corrente.|  
|**/d:**<TargetDirectories\>|**Obbligatoria.** Una o più directory separate da punto e virgola contenenti i file originali che sono stati importati. È possibile utilizzare anche l'unità di importazione, ma non è necessaria se sono disponibili posizioni alternative dei file originali.|  
|**/BK:**<chiave BitLocker\>|**Opzionale.** Specificare la chiave BitLocker se si desidera che lo strumento sblocchi un'unità crittografata in cui sono disponibili i file originali.|  
|**/sn:**<NomeAccountArchiviazione\>|**Obbligatoria.** Nome dell'account di archiviazione per il processo di importazione.|  
|**/sk:**&lt;ChiaveAccountArchiviazione\>|**Obbligatorio** solo se non è specificata una firma di accesso condiviso del contenitore. Chiave dell'account per l'account di archiviazione per il processo di importazione.|  
|**/csas:**&lt;ContainerSas\>|**Obbligatorio** solo se la chiave dell'account di archiviazione non è specificata. Firma di accesso condiviso del contenitore per l'accesso ai BLOB associati al processo di importazione.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Obbligatoria.** Percorso del file dei log di copia dell'unità (log dettagliato o log di errore). Il file viene generato dal servizio di Importazione/Esportazione di Azure e può essere scaricato dall'archiviazione BLOB associata al processo. Il file dei log di copia contiene informazioni sui BLOB non riusciti o sui file da ripristinare.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Opzionale.** Percorso di un file di testo utilizzato per risolvere le ambiguità se si dispone di più file con lo stesso nome importati nello stesso processo. La prima volta che viene eseguito lo strumento, è possibile popolare questo file con tutti i nomi ambigui. Le esecuzioni successive dello strumento useranno questo file per risolvere le ambiguità.|  
  
## <a name="using-the-repairimport-command"></a>Uso del comando RepairImport  
Per ripristinare i dati di importazione tramite flusso di dati in rete, è necessario specificare le directory contenenti i file originali importati usando il parametro `/d`. Specificare anche il file di log di copia scaricato dall'account di archiviazione. Una tipica riga di comando per ripristinare un processo di importazione con errori parziali è:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
Nell'esempio seguente di file dei log di copia, una parte da 64 K di un file era corrotta nell'unità inviata per il processo di importazione. Poiché questo errore è quello indicato, il resto dei BLOB nel processo è stato importato correttamente.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Quando questo log di copia viene passato allo strumento di importazione/esportazione di Azure, lo strumento prova a completare l'importazione del file copiando i contenuti mancanti in rete. Come nell'esempio precedente, lo strumento cerca il file originale `\animals\koala.jpg` all'interno delle due directory `C:\Users\bob\Pictures` e `X:\BobBackup\photos`. Se il file `C:\Users\bob\Pictures\animals\koala.jpg` esiste, lo strumento di importazione/esportazione di Azure copia l'intervallo di dati mancante nel BLOB corrispondente `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Risoluzione dei conflitti quando si usa RepairImport  
In alcuni casi, lo strumento potrebbe non essere in grado di trovare o aprire il file necessario per uno dei seguenti motivi: non è stato possibile trovare il file, il file non è accessibile, il nome del file è ambiguo oppure il contenuto del file non è più corretto.  
  
È possibile che si verifichi un errore ambiguo se lo strumento tenta di individuare `\animals\koala.jpg` ed è presente un file con lo stesso nome in `C:\Users\bob\pictures` e `X:\BobBackup\photos` . Ovvero sia `C:\Users\bob\pictures\animals\koala.jpg` che `X:\BobBackup\photos\animals\koala.jpg` esistono nelle unità del processo di importazione.  
  
L'opzione `/PathMapFile` consente di risolvere questi errori. È possibile specificare il nome del file che contiene l'elenco di file che lo strumento non è stato in grado di identificare correttamente. La riga di comando di esempio seguente consente di popolare `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Lo strumento scriverà quindi i percorsi dei file problematici in `9WM35C2V_pathmap.txt`, uno per ogni riga. Ad esempio, dopo l'esecuzione del comando il file potrebbe contenere le voci seguenti:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Per ogni file nell'elenco, è consigliabile tentare di individuare e aprire il file per assicurarsi che sia disponibile per lo strumento. Se si desidera indicare allo strumento in modo esplicito dove trovare un file, modificare il file di mapping del percorso e aggiungere il percorso di ogni file nella stessa riga, separati da un carattere di tabulazione:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Dopo aver reso disponibili i file necessari allo strumento o aver aggiornato il file di mappa del percorso, è possibile eseguire nuovamente lo strumento per completare il processo di importazione.  
  
## <a name="next-steps"></a>Passaggi successivi
 
* [Configurazione dello strumento di importazione/esportazione di Azure](storage-import-export-tool-setup-v1.md)   
* [Preparazione dei dischi rigidi per un processo di importazione](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisione dello stato dei processi con i file di log di copia](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Repairing an export job](../storage-import-export-tool-repairing-an-export-job-v1.md) (Riparazione di un processo di esportazione)
