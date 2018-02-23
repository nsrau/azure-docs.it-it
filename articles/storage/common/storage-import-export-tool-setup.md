---
title: Configurazione dello strumento Importazione/Esportazione di Azure | Documentazione Microsoft
description: "Informazioni su come configurare lo strumento di preparazione e ripristino delle unità per il servizio Importazione/Esportazione di Azure."
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
ms.openlocfilehash: 6b5febd051d0b956c90cb14c260dda1881adac3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="setting-up-the-azure-importexport-tool"></a>Configurazione dello strumento Importazione/Esportazione di Azure

Lo strumento Importazione/Esportazione di Microsoft Azure è lo strumento di preparazione e ripristino delle unità che è possibile usare con il servizio Importazione/Esportazione di Microsoft Azure. È possibile usare lo strumento per svolgere le funzioni seguenti:

* Prima di creare un processo di importazione, è possibile usare questo strumento per copiare i dati nei dischi rigidi che si intende spedire a un data center di Azure.
* Al termine di un processo di importazione, è possibile usare questo strumento per ripristinare eventuali BLOB danneggiati, mancanti o in conflitto con altri BLOB.
* Dopo aver ricevuto le unità da un processo di esportazione completato, è possibile usare questo strumento per recuperare eventuali file danneggiati o mancanti nelle unità.

## <a name="prerequisites"></a>prerequisiti

Se si intende **preparare le unità** per un processo di importazione, è necessario soddisfare i prerequisiti seguenti:

* È necessario disporre di una sottoscrizione di Azure attiva.
* La sottoscrizione deve includere un account di archiviazione con spazio disponibile sufficiente per archiviare i file che si intende importare.
* È necessaria almeno una delle chiavi di accesso dell'account di archiviazione.
* È necessario un computer ("computer di copia") in cui sia installato Windows 7, Windows Server 2008 R2 o un sistema operativo Windows più recente.
* Nel computer di copia deve essere installato .NET Framework 4.
* Nel computer di copia deve essere abilitato BitLocker.
* Sono necessari uno o più dischi rigidi SATA II, III o SSD vuoti da 2,5 o 3,5 pollici, collegati al computer di copia.
* I file che si intende importare devono essere accessibili dal computer di copia, che si trovino in una condivisione di rete o in un disco rigido locale.

Se si tenta di **recuperare un'importazione** parzialmente non riuscita, è necessario disporre dei seguenti elementi:

* I file di log di copia
* La chiave dell'account di archiviazione

Se si tenta di **recuperare un'esportazione** parzialmente non riuscita, è necessario disporre dei seguenti elementi:

* I file di log di copia
* I file manifesto (facoltativi)
* La chiave dell'account di archiviazione

## <a name="installing-the-azure-importexport-tool"></a>Installazione dello strumento di importazione/esportazione di Azure

Prima di tutto [scaricare lo strumento Importazione/Esportazione di Azure](https://www.microsoft.com/download/details.aspx?id=55280) ed estrarlo in una directory nel computer in uso, ad esempio `c:\WAImportExport`.

Lo strumento Importazione/Esportazione di Azure è costituito dai file indicati di seguito:

* dataset.csv
* driveset.csv
* hddid.dll
* Microsoft.Data.Services.Client.dll
* Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportExport.exe
* WAImportExport.exe.config
* WAImportExport.pdb
* WAImportExportCore.dll
* WAImportExportCore.pdb
* WAImportExportRepair.dll
* WAImportExportRepair.pdb

Aprire quindi una finestra del prompt dei comandi in **modalità amministratore** e passare alla directory contenente i file estratti.

Per visualizzare la Guida per il comando, eseguire lo strumento (`WAImportExport.exe`) senza parametri:

```
WAImportExport, a client tool for Windows Azure Import/Export Service. Microsoft (c) 2013


Copy directories and/or files with a new copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>]
        [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]
        DataSet:<dataset.csv>

Add more drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>

Abort an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession

Resume an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession

List drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListDrives

List copy sessions:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListCopySessions

Repair a Drive:
    WAImportExport.exe RepairImport | RepairExport
        /r:<RepairFile> [/logdir:<LogDirectory>]
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]
        [/PathMapFile:<DrivePathMapFile>]

Preview an Export Job:
    WAImportExport.exe PreviewExport
        [/logdir:<LogDirectory>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>

Parameters:

    /j:<JournalFile>
        - Required. Path to the journal file. A journal file tracks a set of drives and
          records the progress in preparing these drives. The journal file must always
          be specified.
    /logdir:<LogDirectory>
        - Optional. The log directory. Verbose log files as well as some temporary
          files will be written to this directory. If not specified, current directory
          will be used as the log directory. The log directory can be specified only
          once for the same journal file.
    /id:<SessionId>
        - Optional. The session Id is used to identify a copy session. It is used to
          ensure accurate recovery of an interrupted copy session.
    /ResumeSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to resume the session.
    /AbortSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to abort the session.
    /sn:<StorageAccountName>
        - Required. Only applicable for RepairImport and RepairExport. The name of
          the storage account.
    /sk:<StorageAccountKey>
        - Required. The key of the storage account.
    /InitialDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of drives to prepare.
    /AdditionalDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of additional drives to be added.
    /r:<RepairFile>
        - Required. Only applicable for RepairImport and RepairExport.
          Path to the file for tracking repair progress. Each drive must have one
          and only one repair file.
    /d:<TargetDirectories>
        - Required. Only applicable for RepairImport and RepairExport.
          For RepairImport, one or more semicolon-separated directories to repair;
          For RepairExport, one directory to repair, e.g. root directory of the drive.
    /CopyLogFile:<DriveCopyLogFile>
        - Required. Only applicable for RepairImport and RepairExport. Path to the
          drive copy log file (verbose or error).
    /ManifestFile:<DriveManifestFile>
        - Required. Only applicable for RepairExport. Path to the drive manifest file.
    /PathMapFile:<DrivePathMapFile>
        - Optional. Only applicable for RepairImport. Path to the file containing
          mappings of file paths relative to the drive root to locations of actual files
          (tab-delimited). When first specified, it will be populated with file paths
          with empty targets, which means either they are not found in TargetDirectories,
          access denied, with invalid name, or they exist in multiple directories. The
          path map file can be manually edited to include the correct target paths and
          specified again for the tool to resolve the file paths correctly.
    /ExportBlobListFile:<ExportBlobListFile>
        - Required. Path to the XML file containing list of blob paths or blob path
          prefixes for the blobs to be exported. The file format is the same as the
          blob list blob format in the Put Job operation of the Import/Export Service
          REST API.
    /DriveSize:<DriveSize>
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.
          Note: 1 GB = 1,000,000,000 bytes
                1 TB = 1,000,000,000,000 bytes
    /DataSet:<dataset.csv>
        - Required. A .csv file that contains a list of directories and/or a list files
          to be copied to target drives.

    /silentmode
        - Optional. If not specified, it will remind you the requirement of drives and
          need your confirmation to continue.

Examples:

    Copy a data set to a drive:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /InitialDriveSet:driveset1.csv
        /DataSet:data.csv

    Copy another dataset to the same drive following the above command:
    WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#2 /DataSet:dataset2.csv

    Preview how many 1.5 TB drives are needed for an export job:
    WAImportExport.exe PreviewExport
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml
        /DriveSize:1.5TB

    Repair an finished import job:
    WAImportExport.exe RepairImport
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log
```

## <a name="next-steps"></a>Passaggi successivi

* [Preparazione dei dischi rigidi per un processo di importazione](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Previewing drive usage for an export job](../storage-import-export-tool-previewing-drive-usage-export-v1.md) (Anteprima dell'uso del disco per un processo di esportazione)
* [Revisione dello stato dei processi con i file di log di copia](../storage-import-export-tool-reviewing-job-status-v1.md)
* [Riparazione di un processo di importazione](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [Repairing an export job](../storage-import-export-tool-repairing-an-export-job-v1.md) (Riparazione di un processo di esportazione)
* [Risoluzione dei problemi relativi allo strumento Importazione/Esportazione di Azure](storage-import-export-tool-troubleshooting-v1.md)
