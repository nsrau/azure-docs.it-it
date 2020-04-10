---
title: Note sulla versione dell'agente Sincronizzazione file di Azure | Microsoft Docs
description: Note sulla versione dell'agente Sincronizzazione file di Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/16/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 765decb8c65254d63ef5267cbc496d2320f58f6e
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991927"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Note sulla versione dell'agente Sincronizzazione file di Azure
Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Le installazioni Windows Server vengono trasformate in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS e FTPS. Si può usare qualsiasi numero di cache in tutto il mondo.

Questo articolo illustra le note sulla versione per le versioni supportate dell'agente Sincronizzazione file di Azure.

## <a name="supported-versions"></a>Versioni supportate
L'agente Sincronizzazione file di Azure supporta le versioni seguenti:

| Attività cardine | Numero di versione dell'agente | Data di rilascio | Stato |
|----|----------------------|--------------|------------------|
| Versione V10 - [KB4522359](https://support.microsoft.com/en-us/help/4522409/azure-file-sync-agent-v10-release-march-2020)| 10.0.0.0 | il 9 aprile 2020 | Supportato |
| Aggiornamento cumulativo di dicembre 2019 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 dicembre 2019 | Supportato |
| Versione V9 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | giovedì 2 dicembre 2019 | Supportato |
| Versione V8 - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 ottobre 2019 | Supportato |
| Aggiornamento cumulativo di luglio 2019 - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 luglio 2019 | Supportato |
| Aggiornamento cumulativo di luglio 2019 - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 luglio 2019 | Supportato |
| Versione V7 - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | giovedì 19 giugno 2019 | Supportato |
| Aggiornamento cumulativo di giugno 2019 - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | giovedì 27 giugno 2019 | Supportato - La versione dell'agente scadrà il 21 aprile 2020 |
| Aggiornamento cumulativo di giugno 2019 - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | giovedì 13 giugno 2019 | Supportato - La versione dell'agente scadrà il 21 aprile 2020 |
| Aggiornamento cumulativo di maggio 2019 - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | giovedì 7 maggio 2019 | Supportato - La versione dell'agente scadrà il 21 aprile 2020 |
| Versione V6 - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | giovedì 21 aprile 2019 | Supportato - La versione dell'agente scadrà il 21 aprile 2020 |
| Rilascio V5 | 5.0.2.0 - 5.2.0.0 | N/D | Non supportato - Le versioni dell'agente sono scadute il 18 marzo 2020 |
| Versione v4 | 4.0.1.0 - 4.3.0.0 | N/D | Non supportato - Le versioni dell'agente sono scadute il 6 novembre 2019 |
| Versione V3 | 3.1.0.0 - 3.4.0.0 | N/D | Non supportato - Le versioni dell'agente sono scadute il 19 agosto 2019 |
| Agenti pre-GA | 1.1.0.0 - 3.0.13.0 | N/D | Non supportato: versione dell'agente scaduta il 1° ottobre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10000"></a>Agente versione 10.0.0.0Agent version 10.0.0.0
Le note sulla versione seguenti sono relative alla versione 10.0.0.0 dell'agente di Sincronizzazione file di Azure (rilasciato il 9 aprile 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Stato di avanzamento della sincronizzazione migliorato nel portaleImproved sync progress in the portal
    - Con la versione dell'agente V10, il portale di Azure inizierà presto a mostrare il tipo di sessione di sincronizzazione in esecuzione. ad esempio download iniziale, download regolare, richiamo in background (casi di disaster recovery veloce) e simili. 

- Esperienza migliorata del portale di suddivisione in livelli cloud
    - Se si dispone di file che non riescono a eseguire il tiering o richiamo, è ora possibile visualizzare gli errori di suddivisione in livelli nelle proprietà dell'endpoint server.
    - Sono disponibili informazioni aggiuntive sulla suddivisione in livelli cloud per un endpoint server:Additional cloud tiering information is available for a server endpoint:
        - Dimensioni cache locale
        - Efficienza di utilizzo della cache
        - Dettagli dei criteri di suddivisione in livelli cloud: dimensioni del volume, spazio libero corrente o l'ora dell'ultimo accesso del file meno recente nella cache locale.
    - Queste modifiche verranno spedite nel portale di Azure poco dopo il rilascio iniziale dell'agente V10.These changes will ship in the Azure portal shortly after the initial V10 agent release.

- Supporto per lo spostamento del servizio di sincronizzazione archiviazione e/o dell'account di archiviazione in un tenant di Azure Active Directory (AAD) diversoSupport for moving the Storage Sync Service and/or storage account to a different Azure Active Directory (AAD) tenant
    - Sincronizzazione file di Azure supporta ora lo spostamento del servizio di sincronizzazione archiviazione e/o dell'account di archiviazione in un gruppo di risorse, una sottoscrizione o un tenant di Azure AD diverso.
    
- Lo strumento di valutazione ora identifica i file o le directory che terminano con un punto
    - Lo strumento di [valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) è stato aggiornato per identificare i file o le directory che terminano con un punto. I file o le directory che terminano con un punto non sono attualmente supportati da Sincronizzazione file di Azure.Files or directories that end with a period are not currently supported by Azure File Sync. Per utilizzare la versione aggiornata dello strumento di [valutazione,](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)installare la versione più recente del [modulo Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-az-ps)
 
- Miglioramenti vari delle prestazioni e dell'affidabilità
    - Rilevamento delle modifiche nella condivisione file di Azure potrebbe non riuscire se la rete virtuale (VNET) e le regole del firewall sono configurate nell'account di archiviazione.
    - Nessun limite di descrittore di protezione più di 2 KB per la sincronizzazione degli elenchi di controllo di accesso discrezionale (DACL).  
    - Riduzione del consumo di memoria associato al richiamo. 
    - Miglioramento delle prestazioni durante l'utilizzo del cmdlet [Invoke-AzStorageSyncChangeDetection.](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
    - Altri miglioramenti di affidabilità vari. 
    
### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può causare risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
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
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet PowerShell Invoke-AzStorageSyncChangeDetection per avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure.To immediately sync files that are changed in the Azure file share, the [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Quando si crea l'endpoint cloud, il servizio di sincronizzazione dell'archiviazione e l'account di archiviazione devono trovarsi nello stesso tenant di Azure AD. Dopo aver creato l'endpoint cloud, il servizio di sincronizzazione dell'archiviazione e l'account di archiviazione possono essere spostati in tenant di Azure AD diversi.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.

## <a name="agent-version-9100"></a>Agente versione 9.1.0.0Agent version 9.1.0.0
Le note sulla versione seguenti sono per la versione 9.1.0.0 dell'agente di Sincronizzazione file di Azure rilasciato il 12 dicembre 2019.The following release notes are for version 9.1.0.0 of the Azure File Sync agent released December 12, 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 9.0.0.0.These notes are in addition to the release notes listed for version 9.0.0.0.

Problema risolto in questa versione:  
- La sincronizzazione non riesce con uno degli errori seguenti dopo l'aggiornamento all'agente di Sincronizzazione file di Azure versione 9.0:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Agente versione 9.0.0.0
Le note sulla versione seguenti sono relative alla versione 9.0.0.0 dell'agente di Sincronizzazione file di Azure (rilasciato il 2 dicembre 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto per il ripristino self-service
    - Gli utenti possono ora ripristinare i propri file utilizzando la funzionalità della versione precedente. Prima della versione v9, la funzionalità della versione precedente non era supportata nei volumi in cui era abilitato il tiering cloud. Questa funzionalità deve essere abilitata per ogni volume separatamente, in cui è disponibile un endpoint con la suddivisione in livelli cloud abilitata. Per altre informazioni, vedere  
[Ripristino self-service tramite versioni precedenti e VSS (servizio Copia Shadow del volume)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Supporto per condivisioni file di dimensioni maggiori 
    - Sincronizzazione file di Azure ora supporta fino a 64TiB e 100 milioni di file in un unico spazio dei nomi di sincronizzazione.  
 
- Supporto della deduplicazione dei dati sul server 2019Data Deduplication support on Server 2019 
    - La deduplicazione dei dati è ora supportata con la suddivisione in livelli cloud abilitata in Windows Server 2019.Data Deduplication is now supported with cloud tiering enabled on Windows Server 2019. Per supportare la deduplicazione dei dati nei volumi con la suddivisione in livelli cloud, è necessario installare l'aggiornamento di Windows [KB4520062.](https://support.microsoft.com/help/4520062) 
 
- Dimensioni minime del file migliorate per un file a livelloImproved minimum file size for a file to tier 
    - La dimensione minima del file da un file a livello è ora basata sulla dimensione del cluster del file system (raddoppiare la dimensione del cluster del file system). Ad esempio, per impostazione predefinita, la dimensione del cluster del file system NTFS è 4 KB, la dimensione minima del file risultante per un file a livello è 8 KB. 
 
- Cmdlet per il test della connettività di rete 
    - Come parte della configurazione di Sincronizzazione file di Azure, è necessario contattare più endpoint del servizio. Ognuno di essi ha il proprio nome DNS che deve essere accessibile al server. Questi URL sono specifici anche per l'area in cui è registrato un server. Una volta registrato un server, il cmdlet di test di connettività (PowerShell e Utilità di registrazione server) può essere utilizzato per testare le comunicazioni con tutti gli URL specifici di questo server. Questo cmdlet consente di risolvere i problemi quando la comunicazione incompleta impedisce al server di lavorare completamente con Sincronizzazione file di Azure e può essere usato per ottimizzare le configurazioni di proxy e firewall.  
 
        Per eseguire il test di connettività di rete, eseguire i comandi di PowerShell seguenti:To run the network connectivity test, run the following PowerShell commands: 
 
        Modulo di importazione "C:" Programmi Azure StorageSyncAgent StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Rimuovere il miglioramento dell'endpoint server quando è abilitata la suddivisione in livelli cloudRemove server endpoint improvement when cloud tiering is enabled 
    - Come in precedenza, la rimozione di un endpoint server non comporta la rimozione di file nella condivisione file di Azure.As before, removing a server endpoint does not result in removing files in the Azure file share. Tuttavia, il comportamento per i reparse point sul server locale è stato modificato. I reparse point (puntatori a file che non sono locali nel server) vengono ora eliminati quando si rimuove un endpoint server. I file completamente memorizzati nella cache rimarranno sul server. Questo miglioramento è stato fatto per evitare che i [file a livelli orfani](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) durante la rimozione di un endpoint server. Se l'endpoint server viene ricreato, i reparse point per i file a livelli verranno ricreati nel server.  
 
- Miglioramenti dell'affidabilità e delle prestazioni 
    - Ridotti gli errori di richiamo. Le dimensioni del richiamo vengono ora regolate automaticamente in base alla larghezza di banda della rete. 
    - Miglioramento delle prestazioni di download quando si aggiunge un nuovo server a un gruppo di sincronizzazione. 
    - I file ridotti non vengono sincronizzati a causa di conflitti di vincoli. 
    - I file non riescono a eseguire il tiero o vengono richiamati in modo imprevisto in determinati scenari se il percorso dell'endpoint del server è un punto di montaggio del volume.
    
### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può causare risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

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
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili. Per ulteriori informazioni, vedere [I file a livelli non sono accessibili nel server dopo l'eliminazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)di un endpoint server.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet PowerShell Invoke-AzStorageSyncChangeDetection per avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure.To immediately sync files that are changed in the Azure file share, the [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
- I file potrebbero non riuscire a tier se il file pagefile.sys si trova in un volume che ha abilitato la suddivisione in livelli cloud. Il file pagefile.sys deve trovarsi in un volume con la suddivisione in livelli cloud disabilitata.

## <a name="agent-version-8000"></a>Agente versione 8.0.0.0Agent version 8.0.0.0
Le note sulla versione seguenti sono per la versione 8.0.0.0 dell'agente di Sincronizzazione file di Azure (rilasciato l'8 ottobre 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Miglioramenti delle prestazioni di ripristino
    - Tempi di ripristino più rapidi per il ripristino eseguiti tramite Backup di Azure.Faster recovery times for recovery done through Azure Backup. I file ripristinati verranno sincronizzati di nuovo nei server di sincronizzazione file di Azure molto più velocemente. 
- Esperienza migliorata del portale di suddivisione in livelli cloud  
    - Se si dispone di file a livelli che non vengono riprodotti, è ora possibile visualizzare gli errori di richiamo nelle proprietà dell'endpoint server. Inoltre, l'integrità dell'endpoint server mostrerà ora un errore e i passaggi di attenuazione se il driver del filtro di tiering cloud non è caricato nel server.
- Installazione più semplice degli agenti
    - Non è più necessario il modulo PowerShell Az-AzureRM per registrare il server rendendo l'installazione più semplice e veloce.
- Miglioramenti vari delle prestazioni e dell'affidabilità

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può causare risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

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
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili. Per ulteriori informazioni, vedere [I file a livelli non sono accessibili nel server dopo l'eliminazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)di un endpoint server.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet PowerShell Invoke-AzStorageSyncChangeDetection per avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure.To immediately sync files that are changed in the Azure file share, the [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.

## <a name="agent-version-7200"></a>Agente versione 7.2.0.0Agent version 7.2.0.0
Le note sulla versione seguenti sono per la versione 7.2.0.0 dell'agente di Sincronizzazione file di Azure rilasciato il 24 luglio 2019.The following release notes are for version 7.2.0.0 of the Azure File Sync agent released July 24, 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 7.0.0.0.These notes are in addition to the release notes listed for version 7.0.0.0.

Elenco dei problemi risolti in questa versione:  
- L'agente di sincronizzazione archiviazione (FileSyncSvc) si blocca se la configurazione del proxy è null.
- L'endpoint server avvierà BCDR (errore 0x80c80257 - ECS_E_BCDR_IN_PROGRESS) se più endpoint nel server hanno lo stesso nome.
- Miglioramenti dell'affidabilità dei livelli cloud.

## <a name="agent-version-7100"></a>Agente versione 7.1.0.0Agent version 7.1.0.0
Le note sulla versione seguenti sono per la versione 7.1.0.0 dell'agente di Sincronizzazione file di Azure rilasciato il 12 luglio 2019.The following release notes are for version 7.1.0.0 of the Azure File Sync agent released July 12, 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 7.0.0.0.These notes are in addition to the release notes listed for version 7.0.0.0.

Elenco dei problemi risolti in questa versione:  
- L'accesso o l'esplorazione di una posizione dell'endpoint server tramite SMB è lenta in Windows Server 2012 R2. 
- Aumento dell'utilizzo della CPU dopo l'installazione dell'agente di Sincronizzazione file di Azure v6.
- Miglioramenti della telemetria di tiering cloud.
- Vari miglioramenti dell'affidabilità per cloud a livelli e sincronizzazione.

## <a name="agent-version-7000"></a>Agente versione 7.0.0.0Agent version 7.0.0.0
Le note sulla versione seguenti sono per la versione 7.0.0.0 dell'agente di Sincronizzazione file di Azure (rilasciato il 19 giugno 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto per condivisioni file di dimensioni maggiori
    - Con l'anteprima di condivisioni file di Azure di grandi dimensioni, stiamo aumentando i nostri limiti di supporto anche per la sincronizzazione dei file. In questo primo passaggio, Sincronizzazione file di Azure ora supporta fino a 25 TB e 50 milioni di file in un unico spazio dei nomi di sincronizzazione. Per richiedere l'anteprima della condivisione https://aka.ms/azurefilesatscalesurveyfile di grandi dimensioni, compilare questo modulo . 
- Supporto per l'impostazione del firewall e della rete virtuale negli account di archiviazioneSupport for firewall and virtual network setting on storage accounts
    - Sincronizzazione file di Azure supporta ora l'impostazione del firewall e della rete virtuale negli account di archiviazione. Per configurare la distribuzione in modo che funzioni con l'impostazione firewall e rete virtuale, vedere [Configurare le impostazioni](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)del firewall e della rete virtuale.
- Cmdlet di PowerShell per sincronizzare immediatamente i file modificati nella condivisione file di AzurePowerShell cmdlet to immediately sync files changed in the Azure file share
    - Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet PowerShell Invoke-AzStorageSyncChangeDetection per avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure.To immediately sync files that are changed in the Azure file share, the Invoke-AzStorageSyncChangeDetection PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. Questo cmdlet è destinato a scenari in cui un certo tipo di processo automatizzato sta apportando modifiche nella condivisione file di Azure o le modifiche vengono apportate da un amministratore (ad esempio lo spostamento di file e directory nella condivisione). Per le modifiche apportate dall'utente finale, è consigliabile installare l'agente di Sincronizzazione file di Azure in una macchina virtuale IaaS e fare in modo che gli utenti finali accedano alla condivisione file tramite la macchina virtuale IaaS.For end-user changes, the recommendation is to install the Azure File Sync agent in an IaaS VM and have end users access the file share through the IaaS VM. In questo modo tutte le modifiche verranno sincronizzate rapidamente con altri agenti senza la necessità di utilizzare il cmdlet Invoke-AzStorageSyncChangeDetection. Per altre informazioni, vedere la documentazione di [Invoke-AzStorageSyncChangeDetection.To](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) learn more, see the Invoke-AzStorageSyncChangeDetection documentation.
- Esperienza del portale migliorata se si verificano file che non vengono sincronizzati
    - Se si dispone di file che non riescono a sincronizzare, è ora distinguere tra errori temporanei e persistenti nel portale. Gli errori temporanei in genere si risolvono da soli senza la necessità di un'azione di amministrazione. Ad esempio, un file attualmente in uso non verrà sincronizzato fino alla chiusura dell'handle di file. Per gli errori persistenti, ora mostriamo il numero di file interessati da ogni errore. Il conteggio degli errori permanenti viene visualizzato anche nella colonna dei file che non sincronizzano tutti gli endpoint server in un gruppo di sincronizzazione.
- Ripristino a livello di file di Backup di Azure miglioratoImproved Azure Backup file-level restore
    - I singoli file ripristinati tramite Backup di Azure vengono ora rilevati e sincronizzati più rapidamente con l'endpoint server.
- Miglioramento dell'affidabilità del cmdlet di richiamo del tiering cloud 
    - Il cmdlet Invoke-StorageSyncFileRecall consente ora ai clienti di specificare il numero di tentativi per file e il ritardo di ogni tentativo di file simile a robocopy. In precedenza, questo cmdlet richiamava tutti i file a livelli in un determinato percorso in ordine casuale. Con il nuovo parametro -Order, questo cmdlet richiamerà prima i dati più caldi e rispetterà i criteri di suddivisione in livelli cloud (smettere di richiamare se il criterio di data viene soddisfatto o lo spazio libero del volume è soddisfatto; a seconda di quale evento si verifica per primo).
- Supporto solo per TLS 1.2 (TLS 1.0 e 1.1 è disabilitato)
    - Sincronizzazione file di Azure ora supporta l'uso di TLS 1.2 solo nei server con TLS 1.0 e 1.1 disabilitato. Prima di questo miglioramento, la registrazione del server avrebbe esito negativo se TLS 1.0 e 1.1 è stato disabilitato sul server.
- Vari miglioramenti delle prestazioni e dell'affidabilità per la sincronizzazione e il tiering cloud
    - In questa versione sono stati apportati diversi miglioramenti all'affidabilità e alle prestazioni. Alcuni di essi sono destinati a rendere la suddivisione in livelli cloud più efficiente e Sincronizzazione file di Azure nel suo complesso funziona meglio in quelle situazioni quando è stata impostata una pianificazione di limitazione della larghezza di banda.

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può causare risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

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
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.

## <a name="agent-version-6300"></a>Agente versione 6.3.0.0Agent version 6.3.0.0
Le note sulla versione seguenti sono per la versione 6.3.0.0 dell'agente di Sincronizzazione file di Azure rilasciato il 27 giugno 2019.The following release notes are for version 6.3.0.0 of the Azure File Sync agent released June 27, 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 6.0.0.0.These notes are in addition to the release notes listed for version 6.0.0.0.

Elenco dei problemi risolti in questa versione:  
- L'accesso o l'esplorazione di una posizione dell'endpoint server tramite SMB è lenta in Windows Server 2012 R2 
- Aumento dell'utilizzo della CPU dopo l'installazione dell'agente di Sincronizzazione file di Azure v6
- Miglioramenti dei dati di telemetria nel tiering cloudCloud tiering telemetry improvements

## <a name="agent-version-6200"></a>Agente versione 6.2.0.0Agent version 6.2.0.0
Le note sulla versione seguenti sono per la versione 6.2.0.0 dell'agente di Sincronizzazione file di Azure rilasciato il 13 giugno 2019.The following release notes are for version 6.2.0.0 of the Azure File Sync agent released June 13, 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 6.0.0.0.These notes are in addition to the release notes listed for version 6.0.0.0.

Elenco dei problemi risolti in questa versione:  
- Dopo aver creato un endpoint server, può verificarsi un utilizzo elevato della CPU quando il richiamo in background sta scaricando i file nel server
- Le operazioni di sincronizzazione e suddivisione in livelli nel cloud potrebbero non riuscire con ECS_E_SERVER_CREDENTIAL_NEEDED di errore a causa della scadenza del token
- La richiamata di un file potrebbe non riuscire se l'URL per scaricarlo contiene caratteri riservati 

## <a name="agent-version-6100"></a>Agente versione 6.1.0.0Agent version 6.1.0.0
Le note sulla versione seguenti sono per la versione 6.1.0.0 dell'agente di Sincronizzazione file di Azure rilasciata il 6 maggio 2019.The following release notes are for version 6.1.0.0 of the Azure File Sync agent released May 6, 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 6.0.0.0.These notes are in addition to the release notes listed for version 6.0.0.0.

Elenco dei problemi risolti in questa versione:  
- Interfaccia di amministrazione di Windows non riesce a visualizzare la versione dell'agente e la configurazione dell'endpoint server nei server in cui è installato l'agente di Sincronizzazione file di Azure versione 6.0.

## <a name="agent-version-6000"></a>Agente versione 6.0.0.0Agent version 6.0.0.0
Le note sulla versione seguenti sono relative alla versione 6.0.0.0 dell'agente di Sincronizzazione file di Azure (rilasciato il 22 aprile 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto per l'aggiornamento automatico dell'agente
  - Abbiamo sentito il tuo feedback e aggiunto una funzionalità di aggiornamento automatico nell'agente del server di sincronizzazione file di Azure. Per altre informazioni, vedere Criteri di [aggiornamento dell'agente di Sincronizzazione file](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy)di Azure.For more information, see Azure File Sync agent update policy .
- Supporto per gli ACL di condivisione file di AzureSupport for Azure file share ACLs
  - Sincronizzazione file di Azure ha sempre supportato la sincronizzazione degli ACL tra endpoint server, ma gli ACL non sono stati sincronizzati con l'endpoint cloud (condivisione file di Azure). Questa versione aggiunge il supporto per la sincronizzazione degli ACL tra endpoint server e cloud.
- Sessioni di sincronizzazione di caricamento e download parallele per un endpoint server 
  - Gli endpoint server supportano ora il caricamento e il download di file contemporaneamente. Non è più necessario attendere il completamento di un download, in modo che i file possano essere caricati nella condivisione file di Azure.No more waiting for a download to complete so files can be uploaded to the Azure file share. 
- Nuovi cmdlet cloud Tiering per ottenere lo stato di volume e suddivisione in livelli
  - Due nuovi cmdlet PowerShell locali al server possono ora essere utilizzati per ottenere informazioni sulla suddivisione in livelli cloud e sul richiamo dei file. Consentono di rendere disponibili le informazioni di registrazione da due canali di eventi sul server:
    - Get-StorageSyncFileTieringResult elencherà tutti i file e i relativi percorsi che non sono stati suddivisi in livelli e segnala il motivo.
    - Get-StorageSyncFileRecallResult segnala tutti gli eventi di richiamo dei file. Elenca ogni file richiamato e il suo percorso, nonché il successo o l'errore per quel richiamo.
  - Per impostazione predefinita, entrambi i canali evento possono memorizzare fino a 1 MB ciascuno: è possibile aumentare la quantità di file segnalati aumentando la dimensione del canale degli eventi.
- Supporto per la modalità FIPS
  - Sincronizzazione file di Azure supporta ora l'abilitazione della modalità FIPS nei server in cui è installato l'agente di Sincronizzazione file di Azure.Azure File Sync now supports enabling FIPS mode on servers which have the Azure File Sync agent installed.
    - Prima di abilitare la modalità FIPS nel server, installare l'agente di Sincronizzazione file di Azure e il [modulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) nel server. Se FIPS è già abilitato sul server, [scaricare manualmente](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) il [modulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) sul server.
- Vari miglioramenti dell'affidabilità per la suddivisione in livelli e la sincronizzazione cloud

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
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
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.

## <a name="agent-version-5200"></a>Agente versione 5.2.0.0Agent version 5.2.0.0
Le note sulla versione seguenti sono per la versione 5.2.0.0 dell'agente di Sincronizzazione file di Azure rilasciato il 4 aprile 2019.The following release notes are for version 5.2.0.0 of the Azure File Sync agent released April 4, 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 5.0.2.0.These notes are in addition to the release notes listed for version 5.0.2.0.

Elenco dei problemi risolti in questa versione:  
- Miglioramenti dell'affidabilità per il trasferimento di dati offline e le funzionalità di ripresa del trasferimento dati
- Miglioramenti della telemetria di sincronizzazione

## <a name="agent-version-5100"></a>Agente versione 5.1.0.0Agent version 5.1.0.0
Le note sulla versione seguenti sono per la versione 5.1.0.0 dell'agente di Sincronizzazione file di Azure rilasciata il 7 marzo 2019.The following release notes are for version 5.1.0.0 of the Azure File Sync agent released March 7, 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 5.0.2.0.These notes are in addition to the release notes listed for version 5.0.2.0.

Elenco dei problemi risolti in questa versione:  
- I file potrebbero non riuscire a sincronizzare con errore 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) se l'enumerazione della modifica non riesce nel server
- Se una sessione o un file di sincronizzazione riceve un errore 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), la sincronizzazione tenterà ora di ripetere l'operazione
- I file potrebbero non essere sincronizzati con l'errore 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- L'utilizzo elevato della memoria può verificarsi quando si richiamano i file
- Miglioramenti dei dati di telemetria nel tiering cloudCloud tiering telemetry improvements 

## <a name="agent-version-5020"></a>Versione dell'agente 5.0.2.0
Le note seguenti si riferiscono alla versione 5.0.2.0 dell'agente Sincronizzazione file di Azure resa disponibile il 12 febbraio 2019.

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto per il cloud di Azure per enti pubblici
  - È stato aggiunto il supporto di anteprima per il cloud di Azure per enti pubblici. Richiede una sottoscrizione consentita e un download di agenti speciali da Microsoft. Per ottenere l'accesso all'anteprima, [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)scriveteci direttamente all'indirizzo .
- Supporto per la deduplicazione dei dati
    - La deduplicazione dei dati ha un supporto completo con cloud a livelli abilitato in Windows Server 2016 e Windows Server 2019. Abilitando la deduplicazione dei dati in un volume con cloud a livelli abilitato, è possibile memorizzare nella cache un numero maggiore di file in locale senza effettuare il provisioning di altro spazio di archiviazione.
- Supporto per il trasferimento dei dati offline (ad esempio tramite Data Box)
    - Migrare facilmente grandi quantità di dati in Sincronizzazione file di Azure secondo la modalità scelta. È possibile scegliere Azure Data Box, AzCopy e anche i servizi di migrazione di terze parti. Non servono grandi quantità di larghezza di banda per trasferire i dati in Azure, nel caso di Data Box è sufficiente inviarli all'interno del servizio tramite posta elettronica. Per altre informazioni, vedere la [documentazione per il trasferimento dati offline](https://aka.ms/AFS/OfflineDataTransfer).
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
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.
