---
title: Note sulla versione dell'agente Sincronizzazione file di Azure | Microsoft Docs
description: Note sulla versione dell'agente Sincronizzazione file di Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 10/8/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 8328170d73d75e8e2eb1a84881375ce21c72a61b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227843"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Note sulla versione dell'agente Sincronizzazione file di Azure
Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Le installazioni Windows Server vengono trasformate in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS e FTPS. Si può usare qualsiasi numero di cache in tutto il mondo.

Questo articolo illustra le note sulla versione per le versioni supportate dell'agente Sincronizzazione file di Azure.

## <a name="supported-versions"></a>Versioni supportate
L'agente Sincronizzazione file di Azure supporta le versioni seguenti:

| Attività cardine | Numero di versione dell'agente | Data di rilascio | Status |
|----|----------------------|--------------|------------------|
| V8 Release - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | October 8, 2019 | Supportato |
| July 2019 update rollup - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 luglio 2019 | Supportato |
| July 2019 update rollup - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | July 12, 2019 | Supportato |
| V7 Release - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | June 19, 2019 | Supportato |
| June 2019 update rollup - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | June 27, 2019 | Supportato |
| June 2019 update rollup - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | June 13, 2019 | Supportato |
| May 2019 update rollup - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | May 7, 2019 | Supportato |
| V6 Release - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | April 21, 2019 | Supportato |
| April 2019 update rollup - [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | April 4, 2019 | Supportato |
| March 2019 update rollup - [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | March 7, 2019 | Supportato |
| Versione v5 - [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 febbraio 2019 | Supportato |
| Versione v4 | 4.0.1.0 - 4.3.0.0 | N/D | Not Supported - Agent versions expired on November 6, 2019 |
| V3 Release | 3.1.0.0 - 3.4.0.0 | N/D | Not Supported - Agent versions expired on August 19, 2019 |
| Pre-GA agents | 1.1.0.0 - 3.0.13.0 | N/D | Non supportato: versione dell'agente scaduta il 1° ottobre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-8000"></a>Agent version 8.0.0.0
The following release notes are for version 8.0.0.0 of the Azure File Sync agent (released October 8, 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Restore performance Improvements
    - Faster recovery times for recovery done through Azure Backup. Restored files will sync back down to Azure File Sync servers much faster. 
- Improved cloud tiering portal experience  
    - If you have tiered files that are failing to recall, you can now view the recall errors in the server endpoint properties. Also, the server endpoint health will now show an error and mitigation steps if the cloud tiering filter driver is not loaded on the server.
- Simpler agent installation
    - The Az\AzureRM PowerShell module is no longer required to register the server making installation simpler and fast.
- Miscellaneous performance and reliability improvements

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- The agent is not supported on Nano Server deployment option.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- Running sysprep on a server that has the Azure File Sync agent installed is not supported and can lead to unexpected results. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili. To learn more, see [Tiered files are not accessible on the server after deleting a server endpoint](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. To immediately sync files that are changed in the Azure file share, the [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio di sincronizzazione dei file ibridi nell'account di archiviazione (vedere [Assicurarsi che la sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.

## <a name="agent-version-7200"></a>Agent version 7.2.0.0
The following release notes are for version 7.2.0.0 of the Azure File Sync agent released July 24, 2019. These notes are in addition to the release notes listed for version 7.0.0.0.

Elenco dei problemi risolti in questa versione:  
- Storage Sync Agent (FileSyncSvc) crashes if the proxy configuration is null.
- Server endpoint will start BCDR (error 0x80c80257 - ECS_E_BCDR_IN_PROGRESS) if multiple endpoints on the server have the same name.
- Cloud tiering reliability improvements.

## <a name="agent-version-7100"></a>Agent version 7.1.0.0
The following release notes are for version 7.1.0.0 of the Azure File Sync agent released July 12, 2019. These notes are in addition to the release notes listed for version 7.0.0.0.

Elenco dei problemi risolti in questa versione:  
- Accessing or browsing a server endpoint location over SMB is slow on Windows Server 2012 R2. 
- Increased CPU utilization after installing the Azure File Sync v6 agent.
- Cloud tiering telemetry improvements.
- Vari miglioramenti dell'affidabilità per cloud a livelli e sincronizzazione.

## <a name="agent-version-7000"></a>Agent version 7.0.0.0
The following release notes are for version 7.0.0.0 of the Azure File Sync agent (released June 19, 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Support for larger file share sizes
    - With the preview of larger Azure file shares, we are increasing our support limits for file sync as well. In this first step, Azure File Sync now supports up to 25 TB and 50 million files in a single, syncing namespace. To apply for the large file share preview, fill in this form https://aka.ms/azurefilesatscalesurvey. 
- Support for firewall and virtual network setting on storage accounts
    - Azure File Sync now supports the firewall and virtual network setting on storage accounts. To configure your deployment to work with the firewall and virtual network setting, see [Configure firewall and virtual network settings](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- PowerShell cmdlet to immediately sync files changed in the Azure file share
    - To immediately sync files that are changed in the Azure file share, the Invoke-AzStorageSyncChangeDetection PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. This cmdlet is intended for scenarios where some type of automated process is making changes in the Azure file share or the changes are done by an administrator (like moving files and directories into the share). For end-user changes, the recommendation is to install the Azure File Sync agent in an IaaS VM and have end users access the file share through the IaaS VM. This way all changes will quickly sync to other agents without the need to use the Invoke-AzStorageSyncChangeDetection cmdlet. To learn more, see the [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) documentation.
- Improved portal experience if you encounter files that are not syncing
    - If you have files that are failing to sync, we now differentiate between transient and persistent errors in the portal. Transient errors usually resolve themselves without the need for admin action. For example, a file that is currently in use will not sync until the file handle is closed. For persistent errors, we now show the number of files impacted by each error. The persistent error count is also displayed in the files not syncing column of all server endpoints in a sync group.
- Improved Azure Backup file-level restore
    - Individual files restored using Azure Backup are now detected and synced to the server endpoint faster.
- Improved cloud tiering recall cmdlet reliability 
    - The Invoke-StorageSyncFileRecall cmdlet now allows customers to specify per file retry count and per file retry delay similar to robocopy. Previously, this cmdlet would recall all tiered files under a given path in random order. With the new -Order parameter, this cmdlet will recall the hottest data first and honor the cloud tiering policy (stop recalling if the date policy is met or the volume free space is met; whichever happens first).
- Support for TLS 1.2 only (TLS 1.0 and 1.1 is disabled)
    - Azure File Sync now supports using TLS 1.2 only on servers that have TLS 1.0 and 1.1 disabled. Prior to this improvement, server registration would fail if TLS 1.0 and 1.1 was disabled on the server.
- Miscellaneous performance and reliability improvements for sync and cloud tiering
    - There are several reliability and performance improvements in this release. Some of them are targeted to make cloud tiering more efficient and Azure File Sync as a whole work better in those situations when you have a bandwidth throttling schedule set.

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- The agent is not supported on Nano Server deployment option.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- Running sysprep on a server that has the Azure File Sync agent installed is not supported and can lead to unexpected results. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio di sincronizzazione dei file ibridi nell'account di archiviazione (vedere [Assicurarsi che la sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.

## <a name="agent-version-6300"></a>Agent version 6.3.0.0
The following release notes are for version 6.3.0.0 of the Azure File Sync agent released June 27, 2019. These notes are in addition to the release notes listed for version 6.0.0.0.

Elenco dei problemi risolti in questa versione:  
- Accessing or browsing a server endpoint location over SMB is slow on Windows Server 2012 R2 
- Increased CPU utilization after installing the Azure File Sync v6 agent
- Cloud tiering telemetry improvements

## <a name="agent-version-6200"></a>Agent version 6.2.0.0
The following release notes are for version 6.2.0.0 of the Azure File Sync agent released June 13, 2019. These notes are in addition to the release notes listed for version 6.0.0.0.

Elenco dei problemi risolti in questa versione:  
- After creating a server endpoint, High CPU usage may occur when background recall is downloading files to the server
- Sync and cloud tiering operations may fail with error ECS_E_SERVER_CREDENTIAL_NEEDED due to token expiration
- Recalling a file may fail if the URL to download the file contains reserved characters 

## <a name="agent-version-6100"></a>Agent version 6.1.0.0
The following release notes are for version 6.1.0.0 of the Azure File Sync agent released May 6, 2019. These notes are in addition to the release notes listed for version 6.0.0.0.

Elenco dei problemi risolti in questa versione:  
- Windows Admin Center fails to display the agent version and server endpoint configuration on servers which have Azure File Sync agent version 6.0 installed.

## <a name="agent-version-6000"></a>Agent version 6.0.0.0
The following release notes are for version 6.0.0.0 of the Azure File Sync agent (released April 22, 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Agent auto-update support
  - We have heard your feedback and added an auto-update feature into the Azure File Sync server agent. For more information, see [Azure File Sync agent update policy](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Support for Azure file share ACLs
  - Azure File Sync has always supported syncing ACLs between server endpoints but the ACLs were not synced to the cloud endpoint (Azure file share). This release adds support for syncing ACLs between server and cloud endpoints.
- Parallel upload and download sync sessions for a server endpoint 
  - Server endpoints now support uploading and downloading files at the same time. No more waiting for a download to complete so files can be uploaded to the Azure file share. 
- New Cloud Tiering cmdlets to get volume and tiering status
  - Two new, server-local PowerShell cmdlets can now be used to obtain cloud tiering and file recall information. They make logging information from two event channels on the server available:
    - Get-StorageSyncFileTieringResult will list all files and their paths that haven't tiered and reports on the reason why.
    - Get-StorageSyncFileRecallResult reports all file recall events. It lists every file recalled and its path as well as success or error for that recall.
  - By default, both event channels can store up to 1 MB each – you can increase the amount of files reported by increasing the event channel size.
- Support for FIPS mode
  - Azure File Sync now supports enabling FIPS mode on servers which have the Azure File Sync agent installed.
    - Prior to enabling FIPS mode on your server, install the Azure File Sync agent and [PackageManagement module](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) on your server. If FIPS is already enabled on the server, [manually download](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) the [PackageManagement module](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) to your server.
- Miscellaneous reliability improvements for cloud tiering and sync

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- The agent is not supported on Nano Server deployment option.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio di sincronizzazione dei file ibridi nell'account di archiviazione (vedere [Assicurarsi che la sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.

## <a name="agent-version-5200"></a>Agent version 5.2.0.0
The following release notes are for version 5.2.0.0 of the Azure File Sync agent released April 4, 2019. These notes are in addition to the release notes listed for version 5.0.2.0.

Elenco dei problemi risolti in questa versione:  
- Reliability improvements for offline data transfer and data transfer resume features
- Sync telemetry improvements

## <a name="agent-version-5100"></a>Agent version 5.1.0.0
The following release notes are for version 5.1.0.0 of the Azure File Sync agent released March 7, 2019. These notes are in addition to the release notes listed for version 5.0.2.0.

Elenco dei problemi risolti in questa versione:  
- Files may fail to sync with error 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) if change enumeration is failing on the server
- If a sync session or file receives an error 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), sync will now retry the operation
- Files may fail to sync with error 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- High memory usage may occur when recalling files
- Cloud tiering telemetry improvements 

## <a name="agent-version-5020"></a>Versione dell'agente 5.0.2.0
Le note seguenti si riferiscono alla versione 5.0.2.0 dell'agente Sincronizzazione file di Azure resa disponibile il 12 febbraio 2019.

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto per il cloud di Azure per enti pubblici
  - È stato aggiunto il supporto di anteprima per il cloud di Azure per enti pubblici. Richiede una sottoscrizione consentita e un download di agenti speciali da Microsoft. Per ottenere l'accesso all'anteprima, inviare un messaggio di posta elettronica direttamente a Microsoft all'indirizzo [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com).
- Supporto per la deduplicazione dei dati
    - La deduplicazione dei dati ha un supporto completo con cloud a livelli abilitato in Windows Server 2016 e Windows Server 2019. Abilitando la deduplicazione dei dati in un volume con cloud a livelli abilitato, è possibile memorizzare nella cache un numero maggiore di file in locale senza effettuare il provisioning di altro spazio di archiviazione.
- Supporto per il trasferimento dei dati offline (ad esempio tramite Data Box)
    - Migrare facilmente grandi quantità di dati in Sincronizzazione file di Azure secondo la modalità scelta. You can choose Azure Data Box, AzCopy and even third-party migration services. Non servono grandi quantità di larghezza di banda per trasferire i dati in Azure, nel caso di Data Box è sufficiente inviarli all'interno del servizio tramite posta elettronica. Per altre informazioni, vedere la [documentazione per il trasferimento dati offline](https://aka.ms/AFS/OfflineDataTransfer).
- Prestazioni di sincronizzazione migliorate
    - I clienti con più endpoint server nello stesso volume potrebbero aver riscontrato prestazioni di sincronizzazione più lente nelle versioni precedenti. Sincronizzazione file di Azure crea uno snapshot VSS temporaneo una volta al giorno nel server per sincronizzare i file con handle aperti. Sincronizzazione file ora supporta la sincronizzazione di più endpoint server in un volume quando è attiva una sessione di sincronizzazione VSS. Non occorrerà più attendere il completamento della sessione di sincronizzazione VSS perché la sincronizzazione possa riprendere in altri endpoint server nel volume.
- Funzionalità di monitoraggio nel portale migliorata
    - Sono stati aggiunti grafici nel portale del Servizio di sincronizzazione archiviazione per visualizzare:
        - Numero di file sincronizzati
        - Dimensione dei dati trasferiti
        - Numero di file che non eseguono la sincronizzazione
        - Dimensione dei dati richiamati
        - Stato di connettività del server
    - Per altre informazioni, vedere [Monitorare Sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Scalabilità e affidabilità migliorate
    - Numero massimo di oggetti file system (directory e file) in una directory aumentato a 1.000.000. Il limite precedente era 200.000.
    - Sincronizzazione file effettuerà un tentativo di riprendere il trasferimento dei dati anziché una nuova trasmissione, quando un trasferimento viene interrotto per file di grandi dimensioni 

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nelle opzioni di distribuzione Windows Server Core o Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.
- La modalità FIPS non è supportata e deve essere disabilitata. 

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio di sincronizzazione dei file ibridi nell'account di archiviazione (vedere [Assicurarsi che la sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.
