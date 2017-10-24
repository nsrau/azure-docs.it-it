---
title: Pianificazione per la distribuzione di Sincronizzazione file di Azure (anteprima) | Microsoft Docs
description: Informazioni sugli aspetti da considerare quando si pianifica una distribuzione di File di Azure.
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: d8ac076334a7ed9476b4830596d6ea54c29c0e3c
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Pianificazione per la distribuzione di Sincronizzazione file di Azure (anteprima)
Sincronizzazione file di Azure (anteprima) consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale, attraverso la trasformazione dei server Windows in una cache rapida della condivisione file di Azure. È possibile usare qualsiasi protocollo disponibile in Windows Server per accedere ai dati in locale (tra cui SMB, NFS e FTPS) e sono disponibili tutte le cache di cui si ha bisogno in tutto il mondo.

Questa guida descrive gli aspetti da considerare quando si distribuisce Sincronizzazione file di Azure. È consigliabile leggere l'articolo relativo alla [pianificazione della distribuzione di File di Azure](storage-files-planning.md). 

## <a name="understanding-azure-file-sync-terminology"></a>Terminologia di Sincronizzazione file di Azure
Prima di esaminare in dettaglio Sincronizzazione file di Azure è importante comprendere la relativa terminologia.

### <a name="storage-sync-service"></a>Servizio di sincronizzazione archiviazione
Il servizio di sincronizzazione archiviazione è la risorsa principale di Azure che rappresenta Sincronizzazione file di Azure. Il servizio di sincronizzazione archiviazione è un peer di un'altra risorsa, l'account di archiviazione, e può essere distribuito allo stesso modo nei gruppi di risorse di Azure. Una risorsa di primo livello distinta dall'account di archiviazione è richiesta perché il servizio di sincronizzazione archiviazione può creare relazioni di sincronizzazione con più account di archiviazione usando più gruppi di sincronizzazione. Per una sottoscrizione possono essere distribuiti più servizi di sincronizzazione archiviazione.

### <a name="sync-group"></a>Gruppo di sincronizzazione
Un gruppo di sincronizzazione definisce la topologia di sincronizzazione per un set di file. Gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Se, ad esempio, si hanno due set distinti di file che si vuole gestire con AFS, si possono creare due gruppi di sincronizzazione e aggiungere endpoint diversi a ognuno di essi. Un servizio di sincronizzazione archiviazione può ospitare qualsiasi numero di gruppi di sincronizzazione.  

### <a name="registered-server"></a>Server registrato
L'oggetto Server registrato rappresenta una relazione di trust tra il server (o cluster) e il servizio di sincronizzazione archiviazione. Non esiste una limitazione per il numero di server che si possono registrare a un'istanza del servizio di sincronizzazione archiviazione. Tuttavia, è possibile registrare un server, o un cluster, solo con un servizio di sincronizzazione archiviazione alla volta.

### <a name="azure-file-sync-agent"></a>Agente Sincronizzazione file di Azure
L'agente Sincronizzazione file di Azure è un pacchetto scaricabile che consente di sincronizzare un server Windows Server con una condivisione file di Azure. L'agente Sincronizzazione file di Azure è costituito da tre componenti principali: 
- **FileSyncSvc.exe**: il servizio di Windows in background responsabile del monitoraggio delle modifiche agli endpoint server e dell'avvio delle sessioni di sincronizzazione in Azure.
- **StorageSync.sys**: il filtro del file system di Sincronizzazione file di Azure responsabile della suddivisione in livelli dei file in File di Azure, se è abilitata la suddivisione in livelli nel cloud.
- **Cmdlet di gestione di PowerShell**: cmdlet di PowerShell per l'interazione con il provider di risorse di Azure Microsoft.StorageSync. Per impostazione predefinita, i cmdlet si trovano nel percorso seguente:
    - C:\Programmi\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Programmi\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="cloud-endpoint"></a>Endpoint cloud
Un edpoint cloud è una condivisione file di Azure che fa parte di un gruppo di sincronizzazione. L'intera condivisione file di Azure viene sincronizzata e una condivisione file di Azure può solo essere membro di un unico endpoint cloud e quindi di un unico gruppo di sincronizzazione. Se si aggiunge a un gruppo di sincronizzazione una condivisione file di Azure con un set di file esistente come endpoint cloud, i file verranno uniti con qualsiasi altro file già presente in altri endpoint del gruppo di sincronizzazione.

### <a name="server-endpoint"></a>Endpoint server
Un endpoint server rappresenta una posizione specifica in un server registrato, ad esempio una cartella in un volume del server o la radice del volume. Possono esistere più endpoint server nello stesso volume se i relativi spazi dei nomi non si sovrappongono, ad esempio F:\sync1 e F:\sync2. È possibile configurare i criteri di suddivisione in livelli nel cloud singolarmente per ogni endpoint server. Se si aggiunge a un gruppo di sincronizzazione un percorso del server con un set di file esistente come endpoint server, i file verranno uniti con qualsiasi altro file già presente in altri endpoint del gruppo di sincronizzazione.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud 
La suddivisione in livelli nel cloud è una funzionalità facoltativa di Sincronizzazione file di Azure che consente di archiviare a livelli in File di Azure i file che si usano o a cui si accede raramente. Quando un file è archiviato a livelli, il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) sostituisce il file in locale con un puntatore, o punto di analisi, che rappresenta un URL al file in File di Azure. Un file a livelli ha l'attributo "offline" impostato in NTFS, in modo che le applicazioni di terze parti possano identificare i file a livelli. Quando un utente apre un file a livelli, Sincronizzazione file di Azure richiama facilmente i dati del file da File di Azure senza che l'utente debba sapere che il file non è archiviato localmente nel sistema. Questa funzionalità è nota anche come gestione dell'archiviazione gerarchica (Hierarchical Storage Management, HSM).

## <a name="azure-file-sync-interoperability"></a>Interoperabilità di Sincronizzazione file di Azure 
Questa sezione illustra l'interoperabilità di Sincronizzazione file di Azure con funzionalità e ruoli di Windows Server e soluzioni di terze parti.

### <a name="supported-versions-of-windows-server"></a>Versioni di Windows Server supportate
Attualmente, le versioni di Windows Server supportate da Sincronizzazione file di Azure sono:

| Versione | SKU supportati | Opzioni di distribuzione supportate |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter e Standard | Completa (server con un'interfaccia utente) |
| Windows Server 2012 R2 | Datacenter e Standard | Completa (server con un'interfaccia utente) |

Le versioni future di Windows Server verranno aggiunte non appena rilasciate e le versioni precedenti di Windows possono essere aggiunte in base ai commenti degli utenti.

> [!Important]  
> È consigliabile mantenere aggiornate tutte le istanze di Windows Server usate con Sincronizzazione file di Azure in base agli aggiornamenti più recenti disponibili in Windows Update. 

### <a name="file-system-features"></a>Funzionalità del file system
| Funzionalità | Stato del supporto | Note |
|---------|----------------|-------|
| Elenchi di controllo di accesso (ACL) | Supporto completo | Gli ACL di Windows vengono conservati da Sincronizzazione file di Azure e vengono applicati da Windows Server per gli endpoint server. Gli ACL di Windows non sono (ancora) supportati da File di Azure se si accede ai file direttamente nel cloud. |
| Collegamenti reali | Skipped | |
| Collegamenti simbolici | Skipped | |
| Punti di montaggio | Supporto parziale | I punti di montaggio possono essere la radice di un endpoint server, ma verranno ignorati se contenuti nello spazio dei nomi dell'endpoint server. |
| Giunzioni | Skipped | |
| Punti di analisi | Skipped | |
| Compressione NTFS | Supporto completo | |
| File sparse | Supporto completo | I file sparse verranno sincronizzati (non bloccati), ma verranno sincronizzati nel cloud come file completo. Se il contenuto del file viene modificato nel cloud (o in un altro server), il file non sarà più di tipo sparse quando viene scaricata la modifica |
| Flussi di dati alternativi (ADS) | Mantenuti, ma non sincronizzati | |

> [!Note]  
> Sono supportati solo i volumi NTFS.

### <a name="failover-clustering"></a>Clustering di failover
Il clustering di failover di Windows Server è supportato da Sincronizzazione file di Azure per l'opzione di distribuzione "File server per uso generale". Il clustering di failover non è supportato per l'opzione "File server di scalabilità orizzontale per dati di applicazioni" (SOFS) né per i volumi condivisi cluster (CSV).

> [!Note]  
> L'agente Sincronizzazione file di Azure deve essere installato in ogni nodo di un cluster di failover affinché la sincronizzazione funzioni correttamente.

### <a name="windows-server-data-deduplication"></a>Deduplicazione dei dati di Windows Server
Per i volumi per cui non è abilitata la suddivisione in livelli nel cloud, Sincronizzazione file di Azure supporta l'abilitazione della deduplicazione dei dati per il volume. Al momento non è supportata l'interoperabilità tra Sincronizzazione file di Azure con la suddivisione in livelli nel cloud abilitata e la deduplicazione dei dati.

### <a name="anti-virus-solutions"></a>Soluzioni antivirus
L'antivirus esegue una scansione dei file alla ricerca di codice dannoso noto, quindi può causare il richiamo di file archiviati a livelli. Poiché per i file a livelli è impostato l'attributo "offline", è consigliabile consultare il fornitore del software per sapere come configurare la soluzione in modo che non legga i file offline. 

Le soluzioni seguenti supportano l'esclusione dei file offline:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (vedere la sezione "Scan only what you need to" (Analizza solo quello che devi analizzare) a pagina 90)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Soluzioni di backup
Come le soluzioni antivirus, le soluzioni di backup possono causare il richiamo di file a livelli. Si consiglia di usare una soluzione di backup su cloud per eseguire il backup della condivisione file di Azure anziché usare un prodotto di backup in locale.

### <a name="encryption-solutions"></a>Soluzioni di crittografia
Il supporto per le soluzioni di crittografia dipende dal modo in cui sono implementate. Sincronizzazione file di Azure funziona con:

- Crittografia BitLocker
- Azure RMS (e Active Directory RMS legacy)

Sincronizzazione file di Azure non funziona con:

- NTFS Encrypted File System (EFS)

In generale, Sincronizzazione file di Azure deve assicurare l'interoperabilità con le soluzioni di crittografia situate sotto il file system, ad esempio BitLocker, e con le soluzioni implementate in formato di file, ad esempio BitLocker, ma non è prevista un'interoperabilità speciale per le soluzioni situate sopra il file system, ad esempio NTFS Encrypted File System.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Altre soluzioni di gestione dell'archiviazione gerarchica
Non devono essere usate altre soluzioni di gestione dell'archiviazione gerarchica con Sincronizzazione file di Azure.

## <a name="region-availability"></a>Aree di disponibilità
Sincronizzazione file di Azure è disponibile in anteprima solo nelle seguenti aree:

| Region | Ubicazione del data center |
|--------|---------------------|
| Stati Uniti occidentali | California, Stati Uniti |
| Europa occidentale | Paesi Bassi |
| Asia sudorientale | Singapore |
| Australia orientale | New South Wales, Australia |

In anteprima è supportata solo la sincronizzazione con una condivisione file di Azure nella stessa area del servizio di sincronizzazione archiviazione.

## <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
* [Distribuzione di File di Azure](storage-files-deployment-guide.md)
* [Distribuzione di Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
