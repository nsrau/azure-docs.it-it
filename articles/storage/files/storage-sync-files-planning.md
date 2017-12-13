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
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: f2e7f93d2d2914399f3fc7b24a00540f1c045b58
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Pianificazione per la distribuzione di Sincronizzazione file di Azure (anteprima)
È possibile usare Sincronizzazione file di Azure (anteprima) per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

Questo articolo espone considerazioni importanti per la distribuzione di Sincronizzazione file di Azure. È consigliabile leggere anche [Pianificazione per una distribuzione di File di Azure](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Terminologia di Sincronizzazione file di Azure
Prima di entrare nei dettagli della pianificazione di una distribuzione di Sincronizzazione file di Azure, è importante comprendere la terminologia usata.

### <a name="storage-sync-service"></a>Servizio di sincronizzazione archiviazione
Il servizio di sincronizzazione archiviazione è la principale risorsa di Azure per Sincronizzazione file di Azure. Il servizio di sincronizzazione archiviazione è un peer della risorsa account di archiviazione e può essere distribuito in modo analogo a questa nei gruppi di risorse di Azure. Una risorsa di primo livello distinta dall'account di archiviazione è necessaria perché il servizio di sincronizzazione archiviazione può creare relazioni di sincronizzazione con più account di archiviazione tramite più gruppi di sincronizzazione. Per una sottoscrizione possono essere distribuiti più servizi di sincronizzazione archiviazione.

### <a name="sync-group"></a>Gruppo di sincronizzazione
Un gruppo di sincronizzazione definisce la topologia di sincronizzazione per un set di file. Gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Se si hanno due set distinti di file da gestire con Sincronizzazione file di Azure, ad esempio, si creeranno due gruppi di sincronizzazione e si aggiungeranno endpoint diversi a ognuno. Un servizio di sincronizzazione archiviazione può ospitare qualsiasi numero di gruppi di sincronizzazione.  

### <a name="registered-server"></a>Server registrato
L'oggetto server registrato rappresenta una relazione di trust tra il server (o cluster) e il servizio di sincronizzazione archiviazione. Non esiste un limite per il numero di server che si possono registrare in un'istanza del servizio di sincronizzazione archiviazione. È tuttavia possibile registrare un server (o un cluster) solo con un servizio di sincronizzazione archiviazione alla volta.

### <a name="azure-file-sync-agent"></a>Agente Sincronizzazione file di Azure
L'agente Sincronizzazione file di Azure è un pacchetto scaricabile che consente di sincronizzare Windows Server con una condivisione file di Azure. L'agente Sincronizzazione file di Azure ha tre componenti principali: 
- **FileSyncSvc.exe**: servizio di Windows in background responsabile del monitoraggio delle modifiche negli endpoint server e dell'avvio delle sessioni di sincronizzazione in Azure.
- **StorageSync.sys**: filtro del file system di Sincronizzazione file di Azure responsabile della suddivisione in livelli dei file in File di Azure, se è abilitata la suddivisione in livelli nel cloud.
- **Cmdlet di gestione di PowerShell**: cmdlet di PowerShell per l'interazione con il provider di risorse di Azure Microsoft.StorageSync. Questi componenti sono disponibili nei percorsi predefiniti seguenti:
    - C:\Programmi\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Programmi\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Endpoint server
Un endpoint server rappresenta una posizione specifica in un server registrato, ad esempio una cartella in un volume del server o la radice del volume. Possono esistere più endpoint server nello stesso volume se i relativi spazi dei nomi non si sovrappongono, ad esempio F:\sync1 e F:\sync2. È possibile configurare criteri di suddivisione in livelli cloud singolarmente per ogni endpoint server. Se a un gruppo di sincronizzazione si aggiunge una posizione nel server con un set di file esistente come endpoint server, i file vengono uniti a tutti gli altri file già presenti in altri endpoint del gruppo di sincronizzazione.

> [!Note]  
> Un endpoint server potrebbe trovarsi nel volume di sistema Windows. La suddivisione in livelli cloud non è supportata nel volume di sistema.

### <a name="cloud-endpoint"></a>Endpoint cloud
Un endpoint cloud è una condivisione file di Azure che fa parte di un gruppo di sincronizzazione. L'intera condivisione file di Azure viene sincronizzata e una condivisione file di Azure può far parte di un solo endpoint cloud. Di conseguenza, una condivisione file di Azure può far parte di un solo gruppo di sincronizzazione. Se a un gruppo di sincronizzazione si aggiunge una condivisione file di Azure con un set di file esistente come endpoint cloud, i file esistenti vengono uniti a tutti gli altri file già presenti in altri endpoint del gruppo di sincronizzazione.

> [!Important]  
> Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una sola volta ogni 24 ore. Per altre informazioni, vedere [Domande frequenti su File di Azure](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud 
La suddivisione in livelli nel cloud è una funzionalità facoltativa di Sincronizzazione file di Azure che consente di archiviare a livelli in File di Azure i file che si usano con scarsa frequenza o a cui si accede raramente. Quando un file è archiviato a livelli, il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) sostituisce il file in locale con un puntatore, o punto di analisi. Il punto di analisi rappresenta un URL del file in File di Azure. Un file a livelli ha l'attributo "offline" impostato in NTFS, in modo che le applicazioni di terze parti possano identificare i file a livelli. Quando un utente apre un file a livelli, Sincronizzazione file di Azure richiama facilmente i dati del file da File di Azure senza che l'utente debba sapere che il file non è archiviato localmente nel sistema. Questa funzionalità è nota anche come gestione dell'archiviazione gerarchica (Hierarchical Storage Management, HSM).

> [!Important]  
> La suddivisione in livelli cloud non è supportata per gli endpoint server nei volumi di sistema Windows.

## <a name="azure-file-sync-interoperability"></a>Interoperabilità di Sincronizzazione file di Azure 
Questa sezione illustra l'interoperabilità di Sincronizzazione file di Azure con funzionalità e ruoli di Windows Server e soluzioni di terze parti.

### <a name="supported-versions-of-windows-server"></a>Versioni di Windows Server supportate
Le versioni di Windows Server attualmente supportate da Sincronizzazione file di Azure sono:

| Versione | SKU supportati | Opzioni di distribuzione supportate |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter e Standard | Completa (server con un'interfaccia utente) |
| Windows Server 2012 R2 | Datacenter e Standard | Completa (server con un'interfaccia utente) |

Le versioni future di Windows Server verranno aggiunte non appena verranno rilasciate. Le versioni precedenti di Windows possono essere aggiunte in base ai commenti e ai suggerimenti degli utenti.

> [!Important]  
> È consigliabile mantenere aggiornati tutti i server usati con Sincronizzazione file di Azure in base agli aggiornamenti più recenti disponibili in Windows Update. 

### <a name="file-system-features"></a>Funzionalità del file system
| Funzionalità | Stato del supporto | Note |
|---------|----------------|-------|
| Elenchi di controllo di accesso (ACL) | Supporto completo | Gli elenchi di controllo di accesso di Windows vengono mantenuti da Sincronizzazione file di Azure e vengono applicati da Windows Server negli endpoint server. Gli ACL di Windows non sono ancora supportati da File di Azure se si accede ai file direttamente nel cloud. |
| Collegamenti reali | Skipped | |
| Collegamenti simbolici | Skipped | |
| Punti di montaggio | Supporto parziale | I punti di montaggio possono corrispondere alla radice di un endpoint server, ma vengono ignorati se sono contenuti nello spazio dei nomi di un endpoint server. |
| Giunzioni | Skipped | Ad esempio, le cartelle DfrsrPrivate e DFSRoots del file system distribuito. |
| Punti di analisi | Skipped | |
| Compressione NTFS | Supporto completo | |
| File sparse | Supporto completo | I file sparse vengono sincronizzati (non bloccati), ma vengono sincronizzati nel cloud come file completi. Se il contenuto del file viene modificato nel cloud (o in un altro server), il file non è più di tipo sparse quando viene scaricata la modifica. |
| Flussi di dati alternativi (ADS) | Mantenuti, ma non sincronizzati | |

> [!Note]  
> Sono supportati solo i volumi NTFS. Non sono supportati ReFS, FAT, FAT32 e altri file system.

### <a name="failover-clustering"></a>Clustering di failover
Il clustering di failover di Windows Server è supportato da Sincronizzazione file di Azure per l'opzione di distribuzione "File server per uso generale". Il clustering di failover non è supportato per l'opzione "File server di scalabilità orizzontale per dati di applicazioni" né per i volumi condivisi cluster (CSV, Clustered Shared Volume).

> [!Note]  
> Perché la sincronizzazione funzioni correttamente, l'agente Sincronizzazione file di Azure deve essere installato in tutti i nodi di un cluster di failover.

### <a name="data-deduplication"></a>Deduplicazione dei dati
Per i volumi per cui non è abilitata la suddivisione in livelli nel cloud, Sincronizzazione file di Azure supporta l'abilitazione della deduplicazione dei dati di Windows Server per il volume. Non è attualmente supportata l'interoperabilità tra Sincronizzazione file di Azure con abilitazione della suddivisione in livelli nel cloud e la deduplicazione dei dati.

### <a name="distributed-file-system-dfs"></a>File system distribuito (DFS)
Sincronizzazione file di Azure supporta l'interoperabilità con Spazi dei nomi DFS (DFS-N) e Replica DFS (DFS-R) a partire dall'[agente Sincronizzazione file di Azure 1.2](https://go.microsoft.com/fwlink/?linkid=864522).

**Spazi dei nomi DFS (DFS-N)**: Sincronizzazione file di Azure è completamente supportato nei server DFS-N. È possibile installare l'agente Sincronizzazione file di Azure in uno o più membri DFS-N per sincronizzare i dati tra gli endpoint server e l'endpoint cloud. Per altre informazioni, vedere [Informazioni generali su Spazi dei nomi DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replica DFS (DFS-R)**: dato che DFS-R e Sincronizzazione file di Azure sono entrambi soluzioni di replica, nella maggior parte dei casi è consigliabile sostituire DFS-R con Sincronizzazione file di Azure. Esistono tuttavia diversi scenari in cui può essere opportuno usare insieme DFS-R e Sincronizzazione file di Azure:

- Si esegue la migrazione da una distribuzione di DFS-R a una distribuzione di Sincronizzazione file di Azure. Per altre informazioni, vedere [Eseguire la migrazione di una distribuzione di Replica DFS (DFS-R) in Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Non tutti i server locali in cui è necessaria una copia dei dati dei file possono essere connessi direttamente a Internet.
- I server di succursale consolidano i dati in un unico server di hub per cui si vorrebbe usare Sincronizzazione file di Azure.

Per il funzionamento side-by-side di Sincronizzazione file di Azure e DFS-R:

1. Nei volumi con cartelle replicate con DFS-R deve essere disabilitata la suddivisione in livelli cloud di Sincronizzazione file di Azure.
2. Non devono essere configurati endpoint server nelle cartelle di replica di sola lettura di DFS-R.

Per altre informazioni, vedere la [panoramica di Replica DFS](https://technet.microsoft.com/library/jj127250).

### <a name="antivirus-solutions"></a>Soluzioni antivirus
Un antivirus esegue l'analisi dei file alla ricerca di codice dannoso noto e può quindi causare il richiamo di file archiviati a livelli. Poiché per i file archiviati a livelli è impostato l'attributo "offline", è consigliabile consultare il fornitore del software per sapere come configurare la soluzione in modo che non legga i file offline. 

Le soluzioni seguenti supportano l'esclusione dei file offline:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (vedere "Scan only what you need to" (Analizza solo ciò che è necessario) a pagina 90 del file PDF)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Soluzioni di backup
Come le soluzioni antivirus, le soluzioni di backup possono causare il richiamo di file archiviati a livelli. È consigliabile usare una soluzione di backup nel cloud per eseguire il backup della condivisione file di Azure anziché usare un prodotto di backup locale.

### <a name="encryption-solutions"></a>Soluzioni di crittografia
Il supporto per le soluzioni di crittografia dipende dal modo in cui sono implementate. Sincronizzazione file di Azure funziona con:

- Crittografia BitLocker
- Azure Rights Management Services (Azure RMS) e Active Directory RMS legacy

Sincronizzazione file di Azure non funziona con:

- NTFS Encrypted File System (EFS)

In genere, Sincronizzazione file di Azure supporta l'interoperabilità con soluzioni di crittografia sottostanti il file system, ad esempio BitLocker, e con soluzioni implementate nel formato di file, ad esempio BitLocker. Non è stato creato alcun tipo speciale di interoperabilità per soluzioni al di sopra del file system (ad esempio NTFS EFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Altre soluzioni di gestione dell'archiviazione gerarchica
Con Sincronizzazione file di Azure non devono essere usate altre soluzioni di gestione dell'archiviazione gerarchica.

## <a name="region-availability"></a>Aree di disponibilità
Sincronizzazione file di Azure è disponibile in anteprima solo nelle aree seguenti:

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
* [Come distribuire i file di Azure](storage-files-deployment-guide.md)
* [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
