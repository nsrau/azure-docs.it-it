---
title: Riparazione di un processo di esportazione di Azure | Documentazione Microsoft
description: "Informazioni su come ripristinare un processo di esportazione che è stato creato ed eseguito mediante il servizio di Importazione/Esportazione di Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 728e2a42-04ce-4be8-9375-e9e2bc6827a5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 74182c8c357085f186aaa43adfaef80a083d16bb
ms.openlocfilehash: 7ae819a662230a7ca7da6f7bc5bbb3b3f940074e
ms.lasthandoff: 02/16/2017


---
# <a name="repairing-an-export-job"></a>Riparazione di un processo di esportazione
Al termine di un processo di esportazione, è possibile eseguire lo strumento di Importazione/Esportazione di Microsoft Azure in locale per:  
  
1.  Scaricare i file che il servizio di Importazione/Esportazione di Azure non è riuscito a esportare.  
  
2.  Verificare che i file sull'unità siano stati esportati correttamente.  
  
È necessario disporre della connettività all'archiviazione di Azure per usare questa funzionalità.  
  
Il comando per la riparazione di un processo di importazione è **RepairExport**. È possibile specificare i parametri seguenti:  
  
|Parametro|Descrizione|  
|---------------|-----------------|  
|**/r:<RepairFile\>**|Obbligatorio. Percorso del file di ripristino che tiene traccia dell'avanzamento del ripristino e consente di riprendere un ripristino interrotto. Ogni unità deve contenere un solo file di ripristino. Quando si avvia un ripristino per una determinata unità, si viene spostati nel percorso di un file di ripristino che non esiste ancora. Per riprendere un ripristino interrotto, è consigliabile inserire il nome di un file di ripristino esistente. Il file di ripristino corrispondente all'unità di destinazione deve essere sempre specificato.|  
|**/logdir:<LogDirectory\>**|Facoltativa. Directory dei log. in cui verranno scritti file di log dettagliati. Se non è specificata alcuna directory dei log, verrà usata la directory corrente.|  
|**/d:<TargetDirectory\>**|Obbligatorio. La directory per la convalida e il ripristino. Si tratta in genere della directory radice dell'unità di esportazione, ma potrebbe anche essere una condivisione di file di rete che contiene una copia dei file esportati.|  
|**/bk:<BitLockerKey\>**|Facoltativa. È necessario specificare la chiave BitLocker se si desidera che lo strumento sblocchi un'unità crittografata in cui sono archiviati i file esportati.|  
|**/sn:<StorageAccountName\>**|Obbligatorio. Il nome dell'account di archiviazione per il processo di esportazione.|  
|**/sk:<StorageAccountKey\>**|**Obbligatorio** solo se non è specificata una firma di accesso condiviso del contenitore. Chiave dell'account per l'account di archiviazione per il processo di esportazione.|  
|**/csas:<ContainerSas\>**|**Obbligatorio** solo se non è specificata la chiave dell'account di archiviazione. Firma di accesso condiviso del contenitore per l'accesso ai BLOB associati al processo di esportazione.|  
|**/CopyLogFile:\><DriveCopyLogFile**|Obbligatorio. Il percorso del file dei log di copia dell'unità. Il file viene generato dal servizio di Importazione/Esportazione di Azure e può essere scaricato dall'archiviazione BLOB associata al processo. Il file dei log di copia contiene informazioni sui BLOB non riusciti o sui file da ripristinare.|  
|**/ManifestFile:<DriveManifestFile\>**|Facoltativa. Il percorso al file manifesto dell'unità di esportazione. Questo file è generato dal servizio Importazione/Esportazione di Azure e archiviato nell'unità di esportazione e facoltativamente in un BLOB nell'account di archiviazione associato al processo.<br /><br /> Il contenuto dei file nell'unità di esportazione verrà verificato con gli hash MD5 contenuti in questo file. Tutti i file considerati danneggiati che saranno scaricati e riscritti nelle directory di destinazione.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Utilizzo della modalità RepairExport per correggere esportazioni non riuscite  
È possibile utilizzare lo strumento di Importazione/Esportazione di Azure per scaricare i file non esportati. Il file di log di copia conterrà un elenco di file con errori di esportazione.  
  
Le cause di errori di esportazione includono le possibilità seguenti:  
  
-   Unità danneggiate  
  
-   La chiave di account di archiviazione modificata durante il processo di trasferimento  
  
Per eseguire lo strumento in modalità **RepairExport**, è necessario innanzitutto connettere l'unità contenente i file esportati nel computer. Successivamente, eseguire lo strumento di Importazione/Esportazione di Azure, specificando il percorso dell'unità con il parametro `/d`. È inoltre necessario specificare il percorso di file di log di copia dell'unità che è stato scaricato. Nell'esempio seguente, la riga di comando esegue lo strumento per recuperare eventuali file con errori di esportazione:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Di seguito è riportato un esempio di un file di log di copia che mostra un blocco nel BLOB con errori di esportazione:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Il file di log di copia indica che si è verificato un errore durante il download di uno dei blocchi del BLOB sul file nell'unità di esportazione da parte del servizio di Importazione/Esportazione di Azure. Gli altri componenti del file scaricato correttamente e la lunghezza del file sono stati impostati correttamente. In questo caso, lo strumento apre il file nell'unità, scarica il blocco dall'account di archiviazione e scrive sull'intervallo dei file a partire dall'offset 65536 con lunghezza 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Utilizzo di RepairExport per convalidare i contenuti dell'unità  
È inoltre possibile utilizzare Importazione/Esportazione di Azure con l'opzione **RepairExport** per convalidare la correttezza del contenuto sull'unità. Il file manifesto in ogni unità di esportazione contiene MD5 per il contenuto dell'unità.  
  
Il servizio di Importazione/Esportazione di Azure può inoltre salvare i file manifesto su un account di archiviazione durante il processo di esportazione. Il percorso del file manifesto è disponibile tramite l'operazione [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) (Ottieni processo) al completamento del processo. Vedere [Formato del file manifesto del servizio Importazione/Esportazione](storage-import-export-file-format-metadata-and-properties.md) per ulteriori informazioni sul formato di un file manifesto dell'unità.  
  
Nell'esempio seguente viene illustrato come eseguire lo strumento di Importazione/Esportazione di Azure con i parametri **/ManifestFile** e **/CopyLogFile**:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Di seguito è riportato un esempio di file manifesto:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
Dopo aver completato il processo di ripristino, lo strumento leggerà ciascun file a cui fa riferimento nel file manifesto e verificare l'integrità del file con gli hash MD5. Per il file manifesto di cui sopra, analizzerà i componenti seguenti.  
  
G:\pictures\city\redmond.jpg, offset 0, lunghezza 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, lunghezza 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, lunghezza 3584  
  
G:\pictures\city\redmond.jpg.Properties  
  
G:\pictures\wild\canyon.jpg, offset 0, lunghezza 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, lunghezza 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, lunghezza 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, lunghezza 2721  
  
G:\pictures\wild\canyon.jpg.Properties  
  
Qualsiasi componente con esito negativo della verifica verrà scaricato dallo strumento e riscritto nello stesso file sull'unità.  
  
## <a name="see-also"></a>Vedere anche  
[Configurazione dello strumento Importazione/Esportazione di Azure](storage-import-export-tool-setup-v1.md)   
[Preparing Hard Drives for an Import Job](storage-import-export-tool-preparing-hard-drives-import-v1.md)  (Preparazione dei dischi rigidi per un processo di importazione)  
[Reviewing Job Status with Copy Log Files](storage-import-export-tool-reviewing-job-status-v1.md)  (Revisione dello stato dei processi con i file di log di copia)  
[Repairing an Import Job](storage-import-export-tool-repairing-an-import-job-v1.md)  (Riparazione di un processo di importazione)  
[Risoluzione dei problemi relativi allo strumento di Importazione/Esportazione di Azure](storage-import-export-tool-troubleshooting-v1.md)

