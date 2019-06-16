---
title: Pianificazione per la distribuzione di Sincronizzazione file di Azure | Microsoft Docs
description: Informazioni sugli aspetti da considerare quando si pianifica una distribuzione di File di Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 2/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7cbb934b87440d23e65fce53d7da40c5ffbd3150
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65597085"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Pianificazione per la distribuzione di Sincronizzazione file di Azure
Usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

Questo articolo espone considerazioni importanti per la distribuzione di Sincronizzazione file di Azure. È consigliabile leggere anche [Pianificazione per una distribuzione di File di Azure](storage-files-planning.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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
Un endpoint server rappresenta una posizione specifica in un server registrato, ad esempio una cartella in un volume del server. Possono esistere più endpoint server nello stesso volume se i relativi spazi dei nomi non si sovrappongono, ad esempio `F:\sync1` e `F:\sync2`. È possibile configurare criteri di suddivisione in livelli cloud singolarmente per ogni endpoint server. 

È possibile creare un endpoint server tramite un punto di montaggio. Si noti che i punti di montaggio all'interno dell'endpoint server vengono ignorati.  

È possibile creare un endpoint server nel volume di sistema ma esistono due limitazioni per questa operazione:
* La suddivisione in livelli nel cloud non può essere abilitata.
* Non viene eseguito il ripristino rapido dello spazio dei nomi (in cui il sistema riduce l'intero spazio dei nomi e quindi avvia il richiamo di contenuto).


> [!Note]  
> Sono supportati solo i volumi non rimovibili.  Le unità di cui è stato eseguito il mapping da una condivisione remota non sono supportate per un percorso dell'endpoint server.  Inoltre, un endpoint server può essere posizionato nel volume di sistema Windows anche se la suddivisione in livelli cloud non è supportata nel volume di sistema.

Se a un gruppo di sincronizzazione si aggiunge una posizione nel server con un set di file esistente come endpoint server, i file vengono uniti a tutti gli altri file già presenti in altri endpoint del gruppo di sincronizzazione.

### <a name="cloud-endpoint"></a>Endpoint cloud
Un endpoint cloud è una condivisione file di Azure che fa parte di un gruppo di sincronizzazione. L'intera condivisione file di Azure viene sincronizzata e una condivisione file di Azure può far parte di un solo endpoint cloud. Di conseguenza, una condivisione file di Azure può far parte di un solo gruppo di sincronizzazione. Se a un gruppo di sincronizzazione si aggiunge una condivisione file di Azure con un set di file esistente come endpoint cloud, i file esistenti vengono uniti a tutti gli altri file già presenti in altri endpoint del gruppo di sincronizzazione.

> [!Important]  
> Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una sola volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione. Per altre informazioni, vedere [Domande frequenti su File di Azure](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud 
La suddivisione in livelli nel cloud è una funzionalità facoltativa di Sincronizzazione file di Azure in base alla quale i file a cui si accede di frequente vengono memorizzati nella cache locale del server, mentre tutti gli altri file vengono archiviati a livelli in File di Azure in base alle impostazioni dei criteri. Per altre informazioni, vedere [Informazioni sulla suddivisione in livelli nel cloud](storage-sync-cloud-tiering.md).

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Requisiti di sistema e interoperabilità di Sincronizzazione file di Azure 
Questa sezione illustra l'interoperabilità e i requisiti di sistema dell'agente di Sincronizzazione file di Azure con funzionalità e ruoli di Windows Server e soluzioni di terze parti.

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Si noti che i controlli coprono la maggior parte delle funzionalità indicate di seguito, sebbene non tutte; è consigliabile leggere tutta la sezione con attenzione per assicurarsi che la distribuzione avvenga senza correttamente. 

#### <a name="download-instructions"></a>Istruzioni per il download
1. Assicurarsi di avere installato la versione più recente dei moduli PackageManagement e PowerShellGet (in questo modo è possibile installare i moduli in anteprima)
    
    ```powershell
        Install-Module -Name PackageManagement -Repository PSGallery -Force
        Install-Module -Name PowerShellGet -Repository PSGallery -Force
    ```
 
2. Riavviare PowerShell
3. Installare i moduli
    
    ```powershell
        Install-Module -Name Az.StorageSync -AllowPrerelease -AllowClobber -Force
    ```

#### <a name="usage"></a>Uso  
È possibile richiamare lo strumento di valutazione in modi diversi: è possibile eseguire i controlli di sistema, i controlli dei set di dati o entrambi. Per eseguire i controlli di sistema e del set di dati: 

```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Per testare solo il set di dati:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Per testare solo i requisiti di sistema:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Per visualizzare i risultati in CSV:
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Requisiti di sistema
- Un server che esegue Windows Server 2012 R2, Windows Server 2016 o Windows Server 2019:

    | Version | SKU supportati | Opzioni di distribuzione supportate |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter e Standard | Completa (server con un'interfaccia utente) |
    | Windows Server 2016 | Datacenter e Standard | Completa (server con un'interfaccia utente) |
    | Windows Server 2012 R2 | Datacenter e Standard | Completa (server con un'interfaccia utente) |

    Le versioni future di Windows Server verranno aggiunte non appena verranno rilasciate. Le versioni precedenti di Windows possono essere aggiunte in base ai commenti e ai suggerimenti degli utenti.

    > [!Important]  
    > È consigliabile mantenere aggiornati tutti i server usati con Sincronizzazione file di Azure in base agli aggiornamenti più recenti disponibili in Windows Update. 

- Un server con un minimo di 2 GiB di memoria.

    > [!Important]  
    > Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
    
- Un volume collegato al computer locale formattato con il file system NTFS.

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
| Flussi di dati alternativi (ADS) | Mantenuti, ma non sincronizzati | Ad esempio, i tag di classificazione creati tramite Infrastruttura di classificazione file non vengono sincronizzati. I tag di classificazione esistenti sui file in ognuno degli endpoint server non subiscono variazioni. |

> [!Note]  
> Sono supportati solo i volumi NTFS. Non sono supportati ReFS, FAT, FAT32 e altri file system.

### <a name="files-skipped"></a>File ignorati

| File/cartella | Note |
|-|-|
| Desktop.ini | File specifico del sistema |
| ethumbs.db$ | File temporaneo per anteprime |
| ~$\*.\* | File temporaneo di Office |
| \*.tmp | File temporaneo |
| \*.laccdb | File di blocco dei database di Access|
| 635D02A9D91C401B97884B82B3BCDAEA.* | File di sincronizzazione interno|
| \\System Volume Information | Cartella specifica del volume |
| $RECYCLE.BIN| Cartella |
| \\SyncShareState | Cartella per la sincronizzazione |

### <a name="failover-clustering"></a>Clustering di failover
Il clustering di failover di Windows Server è supportato da Sincronizzazione file di Azure per l'opzione di distribuzione "File server per uso generale". Il clustering di failover non è supportato per l'opzione "File server di scalabilità orizzontale per dati di applicazioni" né per i volumi condivisi cluster (CSV, Clustered Shared Volume).

> [!Note]  
> Perché la sincronizzazione funzioni correttamente, l'agente Sincronizzazione file di Azure deve essere installato in tutti i nodi di un cluster di failover.

### <a name="data-deduplication"></a>Deduplicazione dei dati
**Agente versione 5.0.2.0**   
La deduplicazione dei dati è supportata nei volumi con cloud a livelli abilitato in Windows Server 2016 e Windows Server 2019. Abilitando la deduplicazione dei dati in un volume con cloud a livelli abilitato, è possibile memorizzare nella cache un numero maggiore di file in locale senza effettuare il provisioning di altro spazio di archiviazione.

**Agente di Windows Server 2012 R2 o di versioni precedenti**  
Per i volumi per cui non è abilitata la suddivisione in livelli nel cloud, Sincronizzazione file di Azure supporta l'abilitazione della deduplicazione dei dati di Windows Server per il volume.

### <a name="distributed-file-system-dfs"></a>File system distribuito (DFS)
Sincronizzazione file di Azure supporta l'interoperabilità con Spazi dei nomi DFS (DFS-N) e Replica DFS (DFS-R).

**Spazi dei nomi DFS (DFS-N)** : Sincronizzazione file di Azure è completamente supportato nei server DFS-N. È possibile installare l'agente Sincronizzazione file di Azure in uno o più membri DFS-N per sincronizzare i dati tra gli endpoint server e l'endpoint cloud. Per altre informazioni, vedere [Informazioni generali su Spazi dei nomi DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replica DFS (DFS-R)** : dato che DFS-R e Sincronizzazione file di Azure sono entrambi soluzioni di replica, nella maggior parte dei casi è consigliabile sostituire DFS-R con Sincronizzazione file di Azure. Esistono tuttavia diversi scenari in cui può essere opportuno usare insieme DFS-R e Sincronizzazione file di Azure:

- Si esegue la migrazione da una distribuzione di DFS-R a una distribuzione di Sincronizzazione file di Azure. Per altre informazioni, vedere [Eseguire la migrazione di una distribuzione di Replica DFS (DFS-R) in Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Non tutti i server locali in cui è necessaria una copia dei dati dei file possono essere connessi direttamente a Internet.
- I server di succursale consolidano i dati in un unico server di hub per cui si vorrebbe usare Sincronizzazione file di Azure.

Per il funzionamento side-by-side di Sincronizzazione file di Azure e DFS-R:

1. Nei volumi con cartelle replicate con DFS-R deve essere disabilitata la suddivisione in livelli cloud di Sincronizzazione file di Azure.
2. Non devono essere configurati endpoint server nelle cartelle di replica di sola lettura di DFS-R.

Per altre informazioni, vedere la [panoramica di Replica DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'installazione dell'agente e la registrazione del server devono avvenire dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="windows-search"></a>Ricerca di Windows
Se in un endpoint server è abilitata la suddivisione in livelli nel cloud, i file suddivisi in livelli vengono ignorati e non vengono indicizzati dalla ricerca di Windows. I file non suddivisi in livelli vengono indicizzati correttamente.

### <a name="antivirus-solutions"></a>Soluzioni antivirus
Un antivirus esegue l'analisi dei file alla ricerca di codice dannoso noto e può quindi causare il richiamo di file archiviati a livelli. Nella versione 4.0 e successive dell'agente di Sincronizzazione file di Azure, per i file archiviati a livelli è impostato l'attributo sicuro di Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS. È consigliabile consultare il fornitore del software per ottenere informazioni su come configurare la soluzione in modo che non legga i file per cui è impostato questo attributo (molte lo fanno automaticamente).

Le soluzioni antivirus Microsoft, Windows Defender e System Center Endpoint Protection (SCEP), escludono automaticamente dalla lettura i file con questo attributo. Un test su entrambe le soluzioni ha identificato un problema secondario: quando si aggiunge un server a un gruppo di sincronizzazione esistente, i file di dimensioni inferiori a 800 byte vengono richiamati (scaricati) nel nuovo server. Questi file rimangono nel nuovo server e non vengono suddivisi in livelli, poiché non soddisfano il requisito relativo alle dimensioni della suddivisione in livelli (> 64 KB).

### <a name="backup-solutions"></a>Soluzioni di backup
Come le soluzioni antivirus, le soluzioni di backup possono causare il richiamo di file archiviati a livelli. È consigliabile usare una soluzione di backup nel cloud per eseguire il backup della condivisione file di Azure anziché usare un prodotto di backup locale.

Se si usa una soluzione di backup locale, è necessario eseguire il backup in un server del gruppo di sincronizzazione con la suddivisione in livelli nel cloud disabilitata. Quando si esegue un ripristino, usare le opzioni di ripristino a livello di volume o a livello di file. I file ripristinati tramite l'opzione di ripristino a livello di file vengono sincronizzati con tutti gli endpoint del gruppo di sincronizzazione e i file esistenti vengono sostituiti dalla versione ripristinata dal backup.  Nel ripristino a livello di volume i file non vengono sostituiti dalla versione più recente nella condivisione file di Azure o in altri endpoint server.

> [!Note]  
> Il ripristino bare metal può avere risultati imprevisti e non è attualmente supportato.

> [!Note]  
> Gli snapshot VSS (inclusa la scheda Versioni precedenti) non sono attualmente supportati nei volumi che hanno il cloud a livelli abilitato. Se il cloud a livelli è abilitato, usare gli snapshot di condivisione file di Azure per ripristinare un file dal backup.

### <a name="encryption-solutions"></a>Soluzioni di crittografia
Il supporto per le soluzioni di crittografia dipende dal modo in cui sono implementate. Sincronizzazione file di Azure funziona con:

- Crittografia BitLocker
- Azure Information Protection, Azure Rights Management Services (Azure RMS) e Active Directory RMS

Sincronizzazione file di Azure non funziona con:

- NTFS Encrypted File System (EFS)

In genere, Sincronizzazione file di Azure supporta l'interoperabilità con soluzioni di crittografia sottostanti il file system, ad esempio BitLocker, e con soluzioni implementate nel formato di file, ad esempio Azure Information Protection. Non è stato creato alcun tipo speciale di interoperabilità per soluzioni al di sopra del file system (ad esempio NTFS EFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Altre soluzioni di gestione dell'archiviazione gerarchica
Con Sincronizzazione file di Azure non devono essere usate altre soluzioni di gestione dell'archiviazione gerarchica.

## <a name="region-availability"></a>Aree di disponibilità
Sincronizzazione file di Azure è disponibile solo nelle aree seguenti:

| Region | Ubicazione del data center |
|--------|---------------------|
| Australia orientale | New South Wales |
| Australia sud-orientale | Victoria |
| Brasile meridionale | Lo stato di San Paolo |
| Canada centrale | Toronto |
| Canada orientale | Quebec City |
| India centrale | Pune |
| Stati Uniti centrali | Iowa |
| Asia orientale | RAS di Hong Kong |
| Stati Uniti orientali | Virginia |
| Stati Uniti Orientali 2 | Virginia |
| Corea del Sud centrale| Seul |
| Corea del Sud meridionale| Busan |
| Giappone orientale | Tokyo, Saitama |
| Giappone occidentale | Osaka |
| Stati Uniti centro-settentrionali | Illinois |
| Europa settentrionale | Irlanda |
| Stati Uniti centro-meridionali | Texas |
| India meridionale | Chennai |
| Asia sud-orientale | Singapore |
| Regno Unito meridionale | Londra |
| Regno Unito occidentale | Cardiff |
| US Gov Arizona (anteprima) | Arizona |
| US Gov Texas (anteprima) | Texas |
| US Gov Virginia (anteprima) | Virginia |
| Europa occidentale | Paesi Bassi |
| Stati Uniti centro-occidentali | Wyoming |
| Stati Uniti occidentali | California |
| Stati Uniti occidentali 2 | Washington |

Sincronizzazione file di Azure supporta solo la sincronizzazione con una condivisione file di Azure nella stessa area del servizio di sincronizzazione archiviazione.

> [!Note]  
> Sincronizzazione File di Azure è attualmente disponibile solo in anteprima privata per le aree per enti pubblici. Vedere la [note sulla versione](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#agent-version-5020) per istruzioni su come la registrazione nel programma di anteprima.

### <a name="azure-disaster-recovery"></a>Ripristino di emergenza di Azure
Per evitare la perdita di un'area di Azure, Sincronizzazione file di Azure si integra con l'opzione [ di ridondanza dell'archiviazione con ridondanza geografica](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS). L'archiviazione con ridondanza geografica funziona usando la replica a blocchi asincrona tra l'archiviazione nell'area primaria, con cui in genere interagisce l'utente, e l'archiviazione nell'area secondaria associata. In caso di un'emergenza che provoca la disconnessione temporanea o definitiva di un'area di Azure, Microsoft eseguirà il failover dell'archiviazione nell'area abbinata. 

> [!Warning]  
> Se si usa la condivisione file di Azure come endpoint cloud in un account di archiviazione con ridondanza geografica, è consigliabile non avviare il failover dell'account di archiviazione. Il failover causerebbe l'arresto della sincronizzazione e potrebbe causare inoltre una perdita di dati imprevista nel caso di file appena disposti su livelli. Nel caso di perdita di un'area di Azure, Microsoft attiverà il failover dell'account di archiviazione in modo compatibile con Sincronizzazione file di Azure.

Per supportare l'integrazione di failover tra l'archiviazione con ridondanza geografica e la Sincronizzazione file di Azure, tutte le aree di Sincronizzazione file di Azure vengono associate a un'area secondaria che corrisponde a quella secondaria usata dal servizio di archiviazione. Le associazioni sono le seguenti:

| Area primaria      | Area associata      |
|---------------------|--------------------|
| Australia orientale      | Australia sud-orientale|
| Australia sud-orientale | Australia orientale     |
| Brasile meridionale        | Stati Uniti centro-meridionali   |
| Canada centrale      | Canada orientale        |
| Canada orientale         | Canada centrale     |
| India centrale       | India meridionale        |
| Stati Uniti centrali          | Stati Uniti orientali 2          |
| Asia orientale           | Asia sud-orientale     |
| Stati Uniti orientali             | Stati Uniti occidentali            |
| Stati Uniti orientali 2           | Stati Uniti centrali         |
| Giappone orientale          | Giappone occidentale         |
| Giappone occidentale          | Giappone orientale         |
| Corea del Sud centrale       | Corea del Sud meridionale        |
| Corea del Sud meridionale         | Corea del Sud centrale      |
| Europa settentrionale        | Europa occidentale        |
| Stati Uniti centro-settentrionali    | Stati Uniti centro-meridionali   |
| Stati Uniti centro-meridionali    | Stati Uniti centro-settentrionali   |
| India meridionale         | India centrale      |
| Asia sud-orientale      | Asia orientale          |
| Regno Unito meridionale            | Regno Unito occidentale            |
| Regno Unito occidentale             | Regno Unito meridionale           |
| US Gov Arizona      | US Gov Texas       |
| Governo degli Stati Uniti - Iowa         | US Gov Virginia    |
| US Gov Virgini      | US Gov Texas       |
| Europa occidentale         | Europa settentrionale       |
| Stati Uniti centro-occidentali     | Stati Uniti occidentali 2          |
| Stati Uniti occidentali             | Stati Uniti orientali            |
| Stati Uniti occidentali 2           | Stati Uniti centro-occidentali    |

## <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md)
* [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
* [Come distribuire i file di Azure](storage-files-deployment-guide.md)
* [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
* [Monitorare Sincronizzazione file di Azure](storage-sync-files-monitoring.md)
