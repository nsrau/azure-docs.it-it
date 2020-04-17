---
title: Pianificazione per la distribuzione di Sincronizzazione file di Azure | Microsoft Docs
description: Informazioni sugli aspetti da considerare quando si pianifica una distribuzione di File di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8666f51b88d2a70a2cb27e3606f24010771c8017
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460707"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Pianificazione per la distribuzione di Sincronizzazione file di Azure

:::row:::
    :::column:::
        [![Intervista e demo che presenta Azure File Sync - clicca per giocare!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Sincronizzazione file di Azure è un servizio che consente di memorizzare nella cache un numero di condivisioni file di Azure in una macchina virtuale cloud o Windows Server locale. 
        
        Questo articolo illustra le funzionalità e i concetti di Sincronizzazione file di Azure.This article introduces you to Azure File Sync concepts and features. Quando si ha familiarità con Sincronizzazione file di Azure, è consigliabile seguire la guida alla distribuzione di [Sincronizzazione file](storage-sync-files-deployment-guide.md) di Azure per provare questo servizio.        
    :::column-end:::
:::row-end:::

I file verranno archiviati nel cloud in [condivisioni file](storage-files-introduction.md)di Azure. Le condivisioni file di Azure possono essere usate in due modi: montando direttamente queste condivisioni file di Azure senza server (SMB) o memorizzando nella cache le condivisioni file di Azure in locale usando Sincronizzazione file di Azure.Azure file shares can be used in two ways: by mounting these serverless Azure file shares (SMB) or by caching Azure file shares on-premises using Azure File Sync. L'opzione di distribuzione scelta modifica gli aspetti da considerare durante la pianificazione della distribuzione. 

- **Montaggio diretto di una condivisione file di Azure:** poiché File di Azure fornisce l'accesso SMB, è possibile montare le condivisioni file di Azure in locale o nel cloud usando il client SMB standard disponibile in Windows, macOS e Linux.Direct mount of an Azure file share : Since Azure Files provides SMB access, you can mount Azure file shares on-premises or in the cloud using the standard SMB client available in Windows, macOS, and Linux. Poiché le condivisioni file di Azure sono senza server, la distribuzione per gli scenari di produzione non richiede la gestione di un file server o di un dispositivo NAS. Ciò significa che non è necessario applicare patch software o scambiare i dischi fisici. 

- **Memorizzare nella cache**la condivisione file di Azure in locale con Sincronizzazione file di Azure: Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure, mantenendo la flessibilità, le prestazioni e la compatibilità di un file server locale. Sincronizzazione file di Azure trasforma un server Windows Server locale (o cloud) in una cache rapida della condivisione file di Azure.Azure File Sync transforms an on-premises (or cloud) Windows Server into a quick cache of your Azure file share. 

## <a name="management-concepts"></a>Concetti relativi alla gestione
Una distribuzione di Sincronizzazione file di Azure ha tre oggetti di gestione fondamentali:An Azure File Sync deployment has three fundamental management objects:

- **Condivisione file**di Azure: una condivisione file di Azure è una condivisione file cloud senza server, che fornisce *l'endpoint cloud* di una relazione di sincronizzazione di Sincronizzazione file di Azure.Azure file share : An Azure file share is a serverless cloud file share, which provides the cloud endpoint of an Azure File Sync sync relationship. Files in an Azure file share can be accessed directly with SMB or the FileREST protocol, although we encourage you to primarily access the files through the Windows Server cache when the Azure file share is being used with Azure File Sync. Questo perché File di Azure oggi manca di un meccanismo di rilevamento delle modifiche efficiente come Windows Server, pertanto le modifiche alla condivisione file di Azure direttamente richiederà tempo per propagarsi nuovamente agli endpoint server.
- **Endpoint server:** percorso in Windows Server sincronizzato con una condivisione file di Azure.Server endpoint : The path on the Windows Server that is being synced to an Azure file share. Può trattarsi di una cartella specifica in un volume o nella radice del volume. Nello stesso volume possono esistere più endpoint server se i relativi spazi dei nomi non si sovrappongono.
- **Sync group (Gruppo di sincronizzazione):** oggetto che definisce la relazione di sincronizzazione tra un **endpoint cloud**o una condivisione file di Azure e un endpoint server. Gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Se, ad esempio, si hanno due set distinti di file che si desidera gestire con Sincronizzazione file di Azure, è necessario creare due gruppi di sincronizzazione e aggiungere endpoint diversi a ogni gruppo di sincronizzazione.

### <a name="azure-file-share-management-concepts"></a>Concetti relativi alla gestione delle condivisioni file di AzureAzure file share management concepts
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Concetti di gestione di Sincronizzazione file di AzureAzure File Sync management concepts
I gruppi di sincronizzazione vengono distribuiti in **Storage Sync Services,** che sono oggetti di primo livello che registrano i server per l'uso con Sincronizzazione file di Azure e contengono le relazioni tra i gruppi di sincronizzazione. Il servizio di sincronizzazione archiviazione è un peer della risorsa account di archiviazione e può essere distribuito in modo analogo a questa nei gruppi di risorse di Azure. Un servizio di sincronizzazione archiviazione può creare gruppi di sincronizzazione che contengono condivisioni file di Azure tra più account di archiviazione e più server Windows registrati.

Prima di poter creare un gruppo di sincronizzazione in un servizio di sincronizzazione archiviazione, è necessario registrare un server Windows con il servizio di sincronizzazione archiviazione. Verrà creato un oggetto **server registrato,** che rappresenta una relazione di trust tra il server o il cluster e il servizio di sincronizzazione archiviazione. Per registrare un servizio di sincronizzazione archiviazione, è innanzitutto necessario installare l'agente di Sincronizzazione file di Azure nel server. Un singolo server o cluster può essere registrato con un solo servizio di sincronizzazione archiviazione alla volta.

Un gruppo di sincronizzazione contiene un endpoint cloud, una o una condivisione file di Azure e almeno un endpoint server. L'oggetto endpoint server contiene le impostazioni che configurano la funzionalità di suddivisione in livelli cloud, che fornisce la funzionalità di memorizzazione nella cache di Sincronizzazione file di Azure.The server endpoint object contains the settings that configure the **cloud tiering** capability, which provides the caching capability of Azure File Sync. Per eseguire la sincronizzazione con una condivisione file di Azure, l'account di archiviazione contenente la condivisione file di Azure deve trovarsi nella stessa area di Azure del servizio di sincronizzazione archiviazione.

### <a name="management-guidance"></a>Indicazioni per la gestione
Quando si distribuisce Sincronizzazione file di Azure, è consigliabile:When deploying Azure File Sync, we recommend:

- Distribuzione di condivisioni file di Azure 1:1 con condivisioni file di Windows.Deploying Azure file shares 1:1 with Windows file shares. L'oggetto endpoint server offre un elevato grado di flessibilità nella configurazione della topologia di sincronizzazione sul lato server della relazione di sincronizzazione. Per semplificare la gestione, fare in modo che il percorso dell'endpoint server corrisponda al percorso della condivisione file di Windows.To simplify management, make the path of the server endpoint match the path of the Windows file share. 

- Utilizzare il minor numero possibile di servizi di sincronizzazione archiviazione. Questa operazione semplificherà la gestione quando si dispone di gruppi di sincronizzazione che contengono più endpoint server, poiché un Server Windows può essere registrato solo in un servizio di sincronizzazione archiviazione alla volta. 

- Prestare attenzione alle limitazioni delle operazioni di I/O al secondo di un account di archiviazione durante la distribuzione di condivisioni file di Azure.Prepaying attention to a storage account's IOPS limitations when deploying Azure file shares. Idealmente, è consigliabile eseguire il mapping delle condivisioni file 1:1 con gli account di archiviazione, tuttavia ciò potrebbe non essere sempre possibile a causa di vari limiti e restrizioni, sia dall'organizzazione che da Azure. Quando non è possibile distribuire una sola condivisione file in un account di archiviazione, considerare quali condivisioni saranno altamente attive e quali condivisioni saranno meno attive per garantire che le condivisioni file più calde non vengano messe insieme nello stesso account di archiviazione.

## <a name="windows-file-server-considerations"></a>Considerazioni sul file server di Windows
Per abilitare la funzionalità di sincronizzazione in Windows Server, è necessario installare l'agente scaricabile di Sincronizzazione file di Azure.To enable the sync capability on Windows Server, you must install the Azure File Sync downloadable agent. L'agente di Sincronizzazione file `FileSyncSvc.exe`di Azure fornisce due componenti principali: , il servizio Windows in background `StorageSync.sys`responsabile del monitoraggio delle modifiche negli endpoint server e dell'avvio delle sessioni di sincronizzazione e , un filtro del file system che consente la suddivisione in livelli cloud e il ripristino di emergenza rapido.  

### <a name="operating-system-requirements"></a>Requisiti del sistema operativo
Azure File Sync is supported with the following versions of Windows Server:

| Versione | SKU supportati | Opzioni di distribuzione supportate |
|---------|----------------|------------------------------|
| Windows Server 2019 | Data center, standard e IoT | Completo e Core |
| Windows Server 2016 | Data center, Standard e Storage Server | Completo e Core |
| Windows Server 2012 R2 | Data center, Standard e Storage Server | Completo e Core |

Le versioni future di Windows Server verranno aggiunte non appena verranno rilasciate.

> [!Important]  
> È consigliabile mantenere aggiornati tutti i server usati con Sincronizzazione file di Azure in base agli aggiornamenti più recenti disponibili in Windows Update. 

### <a name="minimum-system-resources"></a>Risorse minime di sistema
Sincronizzazione file di Azure richiede un server, fisico o virtuale, con almeno una CPU e almeno 2 GiB di memoria.

> [!Important]  
> Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.

Per la maggior parte dei carichi di lavoro di produzione, non è consigliabile configurare un server di sincronizzazione di Sincronizzazione file di Azure con solo i requisiti minimi. Per ulteriori informazioni, vedere [Risorse di sistema consigliate.](#recommended-system-resources)

### <a name="recommended-system-resources"></a>Risorse di sistema consigliate
Analogamente a qualsiasi funzionalità o applicazione server, i requisiti delle risorse di sistema per Sincronizzazione file di Azure sono determinati dalla scalabilità della distribuzione. distribuzioni di grandi dimensioni su un server richiedono maggiori risorse di sistema. Per Sincronizzazione file di Azure, la scalabilità è determinata dal numero di oggetti tra gli endpoint del server e dalla varianza nel set di dati. Un singolo server può avere endpoint server in più gruppi di sincronizzazione e il numero di oggetti elencati nella tabella seguente rappresenta lo spazio dei nomi completo a cui è collegato un server. 

Ad esempio, l'endpoint server A con 10 milioni di oggetti , l'endpoint server B con 10 milioni di oggetti e 20 milioni di oggetti. Per la distribuzione di questo esempio, è consigliabile consigliare 8 CPU, 16 GiB di memoria per lo stato stabile e (se possibile) 48 GiB di memoria per la migrazione iniziale.
 
I dati dello spazio dei nomi vengono archiviati in memoria per motivi di prestazioni. Per questo scopo, gli spazi dei nomi più grandi richiedono più memoria per mantenere buone prestazioni e più varianza richiede più CPU per l'elaborazione. 
 
Nella tabella seguente sono state fornite sia le dimensioni dello spazio dei nomi che la conversione in capacità per le condivisioni file di uso generale tipiche, in cui la dimensione media del file è 512 KiB. Se le dimensioni dei file sono inferiori, è consigliabile aggiungere ulteriore memoria per la stessa quantità di capacità. Basare la configurazione della memoria sulle dimensioni dello spazio dei nomi.

| Dimensione dello spazio dei nomi: file & directory (milioni)  | Capacità tipica (TiB)  | Core CPU  | Memoria consigliata (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (sincronizzazione iniziale)/ 2 (varianza tipica)      |
| 5        | 2.3     | 2        | 16 (sincronizzazione iniziale)/ 4 (varianza tipica)    |
| 10       | 4.7     | 4        | 32 (sincronizzazione iniziale)/ 8 (varianza tipica)   |
| 30       | 14,0    | 8        | 48 (sincronizzazione iniziale)/16 (varianza tipica)   |
| 50       | 23.3    | 16       | 64 (sincronizzazione iniziale)/ 32 (varianza tipica)  |
| 100*     | 46.6    | 32       | 128 (sincronizzazione iniziale)/ 32 (varianza tipica)  |

\*La sincronizzazione di più di 100 milioni di file & directory non è al momento consigliata. Questo è un limite morbido basato sulle nostre soglie testate. Per altre informazioni, vedere Obiettivi di scalabilità e prestazioni di File di Azure.For more information, see [Azure Files scalability and performance targets.](storage-files-scale-targets.md#azure-file-sync-scale-targets)

> [!TIP]
> La sincronizzazione iniziale di uno spazio dei nomi è un'operazione che richiede un utilizzo intensivo ed è consigliabile allocare più memoria fino al completamento della sincronizzazione iniziale. Questo non è obbligatorio, ma, può accelerare la sincronizzazione iniziale. 
> 
> La varianza tipica è lo 0,5% della modifica dello spazio dei nomi al giorno. Per livelli più elevati di varianza, prendere in considerazione l'aggiunta di più CPU. 

- Un volume collegato al computer locale formattato con il file system NTFS.

### <a name="evaluation-cmdlet"></a>Cmdlet di valutazione
Prima di distribuire Sincronizzazione file di Azure, è necessario valutare se è compatibile con il sistema usando il cmdlet di valutazione sincronizzazione file di Azure.Before deploying Azure File Sync, you should evaluate whether it is compatible with your system using the Azure File Sync evaluation cmdlet. Questo cmdlet verifica la presenza di potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o una versione del sistema operativo non supportata. I suoi controlli coprono la maggior parte, ma non tutte le caratteristiche di cui sopra; si consiglia di leggere attentamente il resto di questa sezione per assicurarsi che la distribuzione vada senza intoppi. 

Il cmdlet di valutazione può essere installato installando il modulo Az PowerShell, che può essere installato seguendo le istruzioni riportate di seguito: [Installare e configurare Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-Az-ps)

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

### <a name="file-system-compatibility"></a>Compatibilità del file system
Sincronizzazione file di Azure è supportata solo nei volumi NTFS collegati direttamente. L'archiviazione collegata direttamente, o DAS, in Windows Server significa che il sistema operativo Windows Server è proprietario del file system. DAS può essere fornito tramite il collegamento fisico di schietto al file server, collegando dischi virtuali a una macchina virtuale del file server (ad esempio una macchina virtuale ospitata da Hyper-V) o anche tramite ISCSI.

Sono supportati solo i volumi NTFS; ReFS, FAT, FAT32 e altri file system non sono supportati.

Nella tabella seguente viene illustrato lo stato di interoperabilità delle funzionalità del file system NTFS: 

| Funzionalità | Stato del supporto | Note |
|---------|----------------|-------|
| Elenchi di controllo di accesso (ACL) | supporto completo | Gli elenchi di controllo di accesso discrezionale in stile Windows vengono mantenuti da Sincronizzazione file di Azure e vengono applicati da Windows Server negli endpoint server. Gli ACL possono essere applicati anche quando si monta direttamente la condivisione file di Azure, tuttavia ciò richiede una configurazione aggiuntiva. Vedere la [sezione Identità](#identity) per ulteriori informazioni. |
| Collegamenti reali | Operazione ignorata | |
| Collegamenti simbolici | Operazione ignorata | |
| Punti di montaggio | Parzialmente supportato | I punti di montaggio possono corrispondere alla radice di un endpoint server, ma vengono ignorati se sono contenuti nello spazio dei nomi di un endpoint server. |
| Giunzioni | Operazione ignorata | Ad esempio, le cartelle DfrsrPrivate e DFSRoots del file system distribuito. |
| Punti di analisi | Operazione ignorata | |
| Compressione NTFS | supporto completo | |
| File sparse | supporto completo | I file sparse vengono sincronizzati (non bloccati), ma vengono sincronizzati nel cloud come file completi. Se il contenuto del file viene modificato nel cloud (o in un altro server), il file non è più di tipo sparse quando viene scaricata la modifica. |
| Flussi di dati alternativi (ADS) | Mantenuti, ma non sincronizzati | Ad esempio, i tag di classificazione creati tramite Infrastruttura di classificazione file non vengono sincronizzati. I tag di classificazione esistenti sui file in ognuno degli endpoint server non subiscono variazioni. |

<a id="files-skipped"></a>Sincronizzazione file di Azure ignorerà anche alcuni file temporanei e le cartelle di sistema:Azure File Sync will also skip certain temporary files and system folders:

| File/cartella | Note |
|-|-|
| pagefile.sys | File specifico del sistema |
| Desktop.ini | File specifico del sistema |
| thumbs.db | File temporaneo per anteprime |
| ehthumbs.db | File temporaneo per le miniature multimediali |
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
**Windows Server 2016 e Windows Server 2019**   
La deduplicazione dei dati è supportata nei volumi con cloud a livelli abilitato in Windows Server 2016 e Windows Server 2019. L'abilitazione della deduplicazione dei dati in un volume con la suddivisione in livelli cloud abilitata consente di memorizzare nella cache più file in locale senza eseguire il provisioning di più spazio di archiviazione. 

Quando la deduplicazione dei dati è abilitata in un volume con la suddivisione in livelli cloud abilitata, i file ottimizzati di Dedup all'interno della posizione dell'endpoint server verranno suddivisi in livelli in modo simile a un file normale in base alle impostazioni dei criteri di suddivisione in livelli cloud. Una volta che i file ottimizzati Dedup sono stati suddivisi in livelli, il processo di Garbage Collection di Deduplicazione dati verrà eseguito automaticamente per recuperare spazio su disco rimuovendo i blocchi non necessari a cui non fanno più riferimento altri file nel volume.

Si noti che il risparmio di volume si applica solo al server; I dati nella condivisione file di Azure non verranno sottoposti a dedup.

> [!Note]  
> Per supportare la deduplicazione dei dati nei volumi con la suddivisione in livelli cloud abilitata in Windows Server 2019, è necessario installare l'aggiornamento di Windows [KB4520062](https://support.microsoft.com/help/4520062) ed è necessario l'agente di Sincronizzazione file di Azure versione 9.0.0.0 o successiva.

**Windows Server 2012 R2**  
Sincronizzazione file di Azure non supporta la deduplicazione dei dati e la suddivisione in livelli cloud nello stesso volume in Windows Server 2012 R2. Se la deduplicazione dei dati è abilitata in un volume, la suddivisione in livelli cloud deve essere disabilitata. 

**Note**
- Se la deduplicazione dei dati è installata prima di installare l'agente di Sincronizzazione file di Azure, è necessario riavviare il sistema per supportare la deduplicazione dei dati e la suddivisione in livelli cloud nello stesso volume.
- Se la deduplicazione dei dati è abilitata in un volume dopo l'abilitazione della suddivisione in livelli cloud, il processo di ottimizzazione della deduplicazione iniziale ottimizzerà i file nel volume che non sono già a livelli e avranno l'impatto seguente sulla suddivisione in livelli cloud:If Data Deduplication is enabled on a volume after cloud tiering is enabled, the initial Deduplication optimization job will optimize files on the volume that are not already tiered and will have the following impact on cloud tiering:
    - I criteri di spazio libero continueranno a livellire i file in base allo spazio libero sul volume utilizzando la mappa termica.
    - I criteri data ignoreranno la suddivisione in livelli dei file che potrebbero essere stati altrimenti idonei per la suddivisione in livelli a causa del processo di ottimizzazione deduplicazione che accede ai file.
- Per i processi di ottimizzazione della deduplicazione in corso, il cloud tiering con criteri di data verrà ritardato dall'impostazione Diduplicazione dati [MinimumFileAgeDays,](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) se il file non è già a livelli. 
    - Esempio: se l'impostazione MinimumFileAgeDays è di sette giorni e i criteri di data di suddivisione in livelli cloud sono 30 giorni, il criterio di data sarà il tiering dei file dopo 37 giorni.
    - Nota: dopo che un file è suddiviso in livelli da Sincronizzazione file di Azure, il processo di ottimizzazione della deduplicazione ignorerà il file.
- Se un server che esegue Windows Server 2012 R2 con l'agente di sincronizzazione file di Azure installato viene aggiornato a Windows Server 2016 o Windows Server 2019, è necessario eseguire la procedura seguente per supportare la deduplicazione dei dati e la suddivisione in livelli cloud nello stesso volume:  
    - Disinstallare l'agente di Sincronizzazione file di Azure per Windows Server 2012 R2 e riavviare il server.
    - Scaricare l'agente di sincronizzazione file di Azure per la nuova versione del sistema operativo server (Windows Server 2016 o Windows Server 2019).
    - Installare l'agente di Sincronizzazione file di Azure e riavviare il server.  
    
    Nota: le impostazioni di configurazione di Sincronizzazione file di Azure nel server vengono mantenute quando l'agente viene disinstallato e reinstallato.

### <a name="distributed-file-system-dfs"></a>File system distribuito (DFS)
Sincronizzazione file di Azure supporta l'interoperabilità con Spazi dei nomi DFS (DFS-N) e Replica DFS (DFS-R).

**Spazi dei nomi DFS (DFS-N)**: Sincronizzazione file di Azure è completamente supportato nei server DFS-N. È possibile installare l'agente Sincronizzazione file di Azure in uno o più membri DFS-N per sincronizzare i dati tra gli endpoint server e l'endpoint cloud. Per altre informazioni, vedere [Informazioni generali su Spazi dei nomi DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replica DFS (DFS-R):** poiché DFS-R e Sincronizzazione file di Azure sono entrambe soluzioni di replica, nella maggior parte dei casi è consigliabile sostituire DFS-R con Sincronizzazione file di Azure. Esistono tuttavia diversi scenari in cui si desidera usare DFS-R e Azure File Sync insieme:There are however several scenarios where you would want to use DFS-R and Azure File Sync together:

- Si esegue la migrazione da una distribuzione di DFS-R a una distribuzione di Sincronizzazione file di Azure. Per altre informazioni, vedere [Eseguire la migrazione di una distribuzione di Replica DFS (DFS-R) in Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Non tutti i server locali che necessitano di una copia dei dati dei file possono essere connessi direttamente a Internet.
- I server di succursale consolidano i dati in un unico server di hub per cui si vorrebbe usare Sincronizzazione file di Azure.

Per il funzionamento affiancato di Sincronizzazione file di Azure e DFS-R:For Azure File Sync and DFS-R to work side by side:

1. Nei volumi con cartelle replicate con DFS-R deve essere disabilitata la suddivisione in livelli cloud di Sincronizzazione file di Azure.
2. Non devono essere configurati endpoint server nelle cartelle di replica di sola lettura di DFS-R.

Per altre informazioni, vedere la [panoramica di Replica DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
L'uso di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportato e può portare a risultati imprevisti. L'installazione dell'agente e la registrazione del server devono avvenire dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="windows-search"></a>Ricerca di Windows
Se in un endpoint server è abilitata la suddivisione in livelli nel cloud, i file suddivisi in livelli vengono ignorati e non vengono indicizzati dalla ricerca di Windows. I file non suddivisi in livelli vengono indicizzati correttamente.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Altre soluzioni di gestione dell'archiviazione gerarchica
Con Sincronizzazione file di Azure non devono essere usate altre soluzioni di gestione dell'archiviazione gerarchica.

## <a name="identity"></a>Identità
Sincronizzazione file di Azure funziona con l'identità standard basata su Active Directory senza alcuna configurazione speciale oltre alla configurazione della sincronizzazione. Quando si usa Sincronizzazione file di Azure, l'aspettativa generale è che la maggior parte degli accessi passa attraverso i server di memorizzazione nella cache di Sincronizzazione file di Azure, anziché tramite la condivisione file di Azure.When you are using Azure File Sync, the general expectation is that most accesses go through the Azure File Sync caching servers, rather than through the Azure file share. Poiché gli endpoint server si trovano in Windows Server e Windows Server ha supportato gli ACL di Tipo Active Directory e Windows per un periodo di tempo molto lungo, non è necessario nulla se non garantire che i file server di Windows registrati con il servizio di sincronizzazione archiviazione siano aggiunti a un dominio. Sincronizzazione file di Azure archivierà gli ACL nei file nella condivisione file di Azure e li replicherà in tutti gli endpoint server.

Anche se la sincronizzazione delle modifiche apportate direttamente alla condivisione file di Azure richiederà più tempo nel gruppo di sincronizzazione, è anche possibile assicurarsi di poter applicare le autorizzazioni di Active Directory anche nella condivisione file direttamente nel cloud. A tale scopo, è necessario aggiungere l'account di archiviazione all'ad ES locale, proprio come i file server di Windows vengono aggiunti a un dominio. Per altre informazioni sull'aggiunta al dominio dell'account di archiviazione ad un'Active Directory di proprietà del cliente, vedere [Panoramica](storage-files-active-directory-overview.md)di Azure Files Active Directory .

> [!Important]  
> L'aggiunta del dominio all'account di archiviazione ad Active Directory non è necessaria per distribuire correttamente Sincronizzazione file di Azure.Domain joining your storage account to Active Directory is not required to deploy Azure File Sync. Si tratta di un passaggio strettamente facoltativo che consente alla condivisione file di Azure di applicare ACL locali quando gli utenti montano direttamente la condivisione file di Azure.This is strictly optional step that allows the Azure file share to enforce on-premises ACLs when users mount the Azure file share directly.

## <a name="networking"></a>Rete
L'agente di Sincronizzazione file di Azure comunica con il servizio di sincronizzazione archiviazione e la condivisione file di Azure usando il protocollo REST di Sincronizzazione file di Azure e il protocollo FileREST, entrambi che usano sempre HTTPS tramite la porta 443. SMB non viene mai usato per caricare o scaricare dati tra Windows Server e la condivisione file di Azure.SMB is never used to upload or download data between your Windows Server and the Azure file share. Poiché la maggior parte delle organizzazioni consente il traffico HTTPS sulla porta 443, come requisito per visitare la maggior parte dei siti Web, la configurazione di rete speciale non è in genere necessaria per distribuire Sincronizzazione file di Azure.Because most organizations allow HTTPS traffic over port 443, as a requirement for visit most websites, special networking configuration is usually not required to deploy Azure File Sync.

In base ai criteri dell'organizzazione o ai requisiti normativi univoci, potrebbe essere necessaria una comunicazione più restrittiva con Azure e pertanto Sincronizzazione file di Azure offre diversi meccanismi per configurare la rete. In base alle proprie esigenze, è possibile:

- Tunnel sync and file upload/download traffic over your ExpressRoute or Azure VPN. 
- Usare le funzionalità File di Azure e Rete di Azure, ad esempio gli endpoint del servizio e gli endpoint privati.
- Configurare Sincronizzazione file di Azure per supportare il proxy nell'ambiente.
- Limitare l'attività di rete da Sincronizzazione file di Azure.Throttle network activity from Azure File Sync.

Per altre informazioni sulla configurazione della funzionalità di rete di Sincronizzazione file di Azure, vedere:To learn more about configuring the networking functionality of Azure File Sync, see:
- [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md)
- [Corretta integrazione di Sincronizzazione file di Azure nel data center](storage-sync-files-server-registration.md)

## <a name="encryption"></a>Crittografia
Quando si usa Sincronizzazione file di Azure, è necessario considerare tre diversi livelli di crittografia: crittografia nell'archiviazione inattivi di Windows Server, crittografia in transito tra l'agente di sincronizzazione file di Azure e Azure e crittografia al riposo dei dati nella condivisione file di Azure.When using Azure File Sync, there are three different layers of encryption to consider: encryption on the at-rest storage of Windows Server, encryption in transit between the Azure File Sync agent and Azure, and encryption at rest of your data in the Azure file share. 

### <a name="windows-server-encryption-at-rest"></a>Crittografia di Windows Server inattivi 
Esistono due strategie per crittografare i dati in Windows Server che funzionano in genere con Sincronizzazione file di Azure: la crittografia sotto il file system in modo che il file system e tutti i dati scritti in esso vengano crittografati e crittografia all'interno del formato di file stesso. Questi metodi non si escludono a vicenda; possono essere utilizzati insieme se lo si desidera poiché lo scopo della crittografia è diverso.

Per fornire la crittografia sotto il file system, Windows Server fornisce la posta in arrivo di BitLocker. BitLocker is fully transparent to Azure File Sync. Il motivo principale per utilizzare un meccanismo di crittografia come BitLocker consiste nell'impedire l'esfiltrazione fisica dei dati dal data center locale da parte di un utente che ruba i dischi e di impedire il sideload di un sistema operativo non autorizzato per eseguire letture/scritture non autorizzate nei dati. Per ulteriori informazioni su BitLocker, vedere [Cenni preliminari](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)su BitLocker .

I prodotti di terze parti che funzionano in modo simile a BitLocker, in quanto si trovano al di sotto del volume NTFS, dovrebbero funzionare in modo completamente trasparente con Sincronizzazione file di Azure.Third-party products which work similarly to BitLocker, in that they sit beneath the NTFS volume, should similarly work fully transparently with Azure File Sync. 

L'altro metodo principale per crittografare i dati consiste nel crittografare il flusso di dati del file quando l'applicazione salva il file. Alcune applicazioni possono eseguire questa operazione in modo nativo, tuttavia questo non è in genere il caso. Un esempio di metodo per crittografare il flusso di dati del file è Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. Il motivo principale per utilizzare un meccanismo di crittografia come AIP/RMS consiste nell'impedire l'esfiltrazione dei dati dalla condivisione file da parte di utenti che li copiano in percorsi alternativi, ad esempio in un'unità flash o inviandoli tramite posta elettronica a una persona non autorizzata. Quando il flusso di dati di un file viene crittografato come parte del formato di file, questo file continuerà a essere crittografato nella condivisione file di Azure.When a file's data stream is encrypted as part of the file format, this file will continue to be encrypted on the Azure file share. 

Sincronizzazione file di Azure non interagisce con NTFS Encrypted File System (NTFS EFS) o soluzioni di crittografia di terze parti che si esibisce sopra il file system ma al di sotto del flusso di dati del file. 

### <a name="encryption-in-transit"></a>Crittografia in transito
L'agente di Sincronizzazione file di Azure comunica con il servizio di sincronizzazione archiviazione e la condivisione file di Azure usando il protocollo REST di Sincronizzazione file di Azure e il protocollo FileREST, entrambi che usano sempre HTTPS tramite la porta 443. Sincronizzazione file di Azure non invia richieste non crittografate su HTTP. 

Gli account di archiviazione di Azure contengono un'opzione per richiedere la crittografia in transito, che è abilitata per impostazione predefinita. Anche se l'opzione a livello di account di archiviazione è disabilitata, ovvero sono possibili connessioni non crittografate alle condivisioni file di Azure, Sincronizzazione file di Azure utilizzerà comunque solo i canali crittografati per accedere alla condivisione file.

Il motivo principale per disabilitare la crittografia in transito per l'account di archiviazione consiste nel supportare un'applicazione legacy che deve essere eseguita in un sistema operativo precedente, ad esempio Windows Server 2008 R2 o una distribuzione Linux precedente, parlando direttamente con una condivisione file di Azure.The primary reason to disable encryption in transit for the storage account is to support a legacy application that must be run on an older operating system, such as Windows Server 2008 R2 or older Linux distribution, talking with an Azure file share directly. Se l'applicazione legacy comunica con la cache di Windows Server della condivisione file, l'impostazione di disattivazione di questa impostazione non avrà alcun effetto. 

È consigliabile garantire l'abilitazione della crittografia dei dati in transito.

Per altre informazioni sulla crittografia in transito, vedere [Richiedere il trasferimento sicuro in Archiviazione di Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Crittografia della condivisione file di Azure inattiviAzure file share encryption at rest
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Livelli di archiviazione
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Consentire alle condivisioni file standard di estendersi fino a 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilità a livello di area
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Disponibilità dell'area di sincronizzazione dei file di AzureAzure file sync region availability
Sincronizzazione file di Azure è disponibile nelle aree seguenti:Azure File Sync is available in the following regions:

| Cloud di Azure | Area geografica | Area di Azure | Codice regione |
|-------------|-------------------|--------------|-------------|
| Pubblico | Asia | Asia orientale | `eastasia` |
| Pubblico | Asia | Asia sud-orientale | `southeastasia` |
| Pubblico | Australia | Australia orientale | `australiaeast` |
| Pubblico | Australia | Australia sud-orientale | `australiasoutheast` |
| Pubblico | Brasile | Brasile meridionale | `brazilsouth` |
| Pubblico | Canada | Canada centrale | `canadacentral` |
| Pubblico | Canada | Canada orientale | `canadaeast` |
| Pubblico | Europa | Europa settentrionale | `northeurope` |
| Pubblico | Europa | Europa occidentale | `westeurope` |
| Pubblico | Francia | Francia centrale | `francecentral` |
| Pubblico | Francia | Francia a Sud | `francesouth` |
| Pubblico | India | India centrale | `centralindia` |
| Pubblico | India | India meridionale | `southindia` |
| Pubblico | Giappone | Giappone orientale | `japaneast` |
| Pubblico | Giappone | Giappone occidentale | `japanwest` |
| Pubblico | Corea del Sud | Corea centrale | `koreacentral` |
| Pubblico | Corea del Sud | Corea meridionale | `koreasouth` |
| Pubblico | Sud Africa | Sudafrica settentrionale | `southafricanorth` |
| Pubblico | Sud Africa | Sud Africa occidentale | `southafricawest` |
| Pubblico | Emirati Arabi Uniti | Emirati Arabi Uniti Centrale | `uaecentral` |
| Pubblico | Emirati Arabi Uniti | Emirati Arabi Uniti settentrionali | `uaenorth` |
| Pubblico | Regno Unito | Regno Unito meridionale | `uksouth` |
| Pubblico | Regno Unito | Regno Unito occidentale | `ukwest` |
| Pubblico | Stati Uniti | Stati Uniti centrali | `centralus` |
| Pubblico | Stati Uniti | Stati Uniti orientali | `eastus` |
| Pubblico | Stati Uniti | Stati Uniti orientali 2 | `eastus2` |
| Pubblico | Stati Uniti | Stati Uniti centro-settentrionali | `northcentralus` |
| Pubblico | Stati Uniti | Stati Uniti centro-meridionali | `southcentralus` |
| Pubblico | Stati Uniti | Stati Uniti centro-occidentali | `westcentralus` |
| Pubblico | Stati Uniti | Stati Uniti occidentali | `westus` |
| Pubblico | Stati Uniti | Stati Uniti occidentali 2 | `westus2` |
| US Gov | Stati Uniti | US Gov Arizona | `usgovarizona` |
| US Gov | Stati Uniti | US Gov Texas | `usgovtexas` |
| US Gov | Stati Uniti | US Gov Virginia | `usgovvirginia` |

Sincronizzazione file di Azure supporta solo la sincronizzazione con una condivisione file di Azure nella stessa area del servizio di sincronizzazione archiviazione.

Per le aree contrassegnate con asterischi, è necessario contattare il supporto di Azure per richiedere l'accesso ad Archiviazione di Azure in tali aree. Il processo è delineato in [questo documento](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="redundancy"></a>Ridondanza
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> L'archiviazione con ridondanza geografica e ridondante zona geografica è in grado di eseguire manualmente il failover dell'archiviazione nell'area secondaria. È consigliabile non eseguire questa operazione all'esterno di un'emergenza quando si usa Sincronizzazione file di Azure a causa della maggiore probabilità di perdita di dati. In caso di emergenza in cui si vuole avviare un failover manuale dell'archiviazione, è necessario aprire un caso di supporto con Microsoft per ottenere Sincronizzazione file di Azure per riprendere la sincronizzazione con l'endpoint secondario.

## <a name="migration"></a>Migrazione
Se si dispone di un file server Windows esistente, Sincronizzazione file di Azure può essere installato direttamente sul posto, senza la necessità di spostare i dati in un nuovo server. Se si prevede di eseguire la migrazione a un nuovo file server di Windows come parte dell'adozione di Sincronizzazione file di Azure, esistono diversi approcci possibili per spostare i dati:

- Creare endpoint server per la condivisione file precedente e la nuova condivisione file e consentire a Sincronizzazione file di Azure di sincronizzare i dati tra gli endpoint server. Il vantaggio di questo approccio è che rende molto semplice eseguire l'oversubscription dell'archiviazione nel nuovo file server, poiché Sincronizzazione file di Azure è in grado di riconoscere il tiering cloud. Quando si è pronti, è possibile tagliare gli utenti finali alla condivisione file nel nuovo server e rimuovere l'endpoint server della condivisione file precedente.

- Creare un endpoint server solo nel nuovo file server e copiare i dati dalla condivisione file precedente utilizzando `robocopy`. A seconda della topologia delle condivisioni file nel nuovo server (numero di condivisioni disponibili in ogni volume, quanto è libero ogni `robocopy` volume e così via), potrebbe essere necessario eseguire temporaneamente il provisioning di spazio di archiviazione aggiuntivo, poiché è previsto che dal server precedente al nuovo server all'interno del data center locale verrà completato più velocemente rispetto a Sincronizzazione file di Azure che sposterà i dati in Azure.A to the topology of file shares on your new server are many shares you have on each volume, how free each volume is etc.), you may need to temporarily provision additional storage as it you is expected that from your old server to your new server within your on-premises datacenter will complete faster than Azure File Sync will move data into Azure.

È anche possibile usare Data Box per eseguire la migrazione dei dati in una distribuzione di Sincronizzazione file di Azure.It is also possible to use Data Box to migrate data into an Azure File Sync deployment. Nella maggior parte dei casi, quando i clienti desiderano utilizzare Data Box per l'inserimento dei dati, lo fanno perché pensano che aumenterà la velocità della distribuzione o perché aiuterà con scenari di larghezza di banda vincolata. Anche se è vero che l'uso di una casella di dati per l'inserimento di dati nella distribuzione di Sincronizzazione file di Azure ridurrà l'utilizzo della larghezza di banda, sarà probabilmente più veloce per la maggior parte degli scenari per perseguire un caricamento di dati online tramite uno dei metodi descritti in precedenza. Per altre informazioni su come usare La casella di dati per inserire dati nella distribuzione di Sincronizzazione file di Azure, vedere Eseguire la migrazione dei dati in Sincronizzazione file di Azure con Azure Data Box.To learn more about how to use Data Box to ingest data into your Azure File Sync deployment, [see Migrate data into Azure File Sync with Azure Data Box.](storage-sync-offline-data-transfer.md)

Un errore comune commesso dai clienti durante la migrazione dei dati nella nuova distribuzione di Sincronizzazione file di Azure consiste nel copiare i dati direttamente nella condivisione file di Azure anziché nei file server Windows.A common mistake users make when migrating data into their new Azure File Sync deployment is to copy data directly into the Azure file share, rather than on their Windows file servers. Anche se Sincronizzazione file di Azure identificherà tutti i nuovi file nella condivisione file di Azure e li sincronizzerà con le condivisioni file di Windows, questa operazione è in genere notevolmente più lenta rispetto al caricamento dei dati tramite il file server di Windows. Molti strumenti di copia di Azure, ad esempio AzCopy, hanno lo svantaggio aggiuntivo di non copiare tutti i metadati importanti di un file, ad esempio timestamp e ACL.

## <a name="antivirus"></a>Antivirus
Un antivirus esegue l'analisi dei file alla ricerca di codice dannoso noto e può quindi causare il richiamo di file archiviati a livelli. Nella versione 4.0 e successive dell'agente di Sincronizzazione file di Azure, per i file archiviati a livelli è impostato l'attributo sicuro di Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS. È consigliabile consultare il fornitore del software per ottenere informazioni su come configurare la soluzione in modo che non legga i file per cui è impostato questo attributo (molte lo fanno automaticamente). 

Le soluzioni antivirus Microsoft, Windows Defender e System Center Endpoint Protection (SCEP), escludono automaticamente dalla lettura i file con questo attributo. Un test su entrambe le soluzioni ha identificato un problema secondario: quando si aggiunge un server a un gruppo di sincronizzazione esistente, i file di dimensioni inferiori a 800 byte vengono richiamati (scaricati) nel nuovo server. Questi file rimangono nel nuovo server e non vengono suddivisi in livelli, poiché non soddisfano il requisito relativo alle dimensioni della suddivisione in livelli (> 64 KB).

> [!Note]  
> I fornitori di antivirus possono verificare la compatibilità tra il prodotto e Sincronizzazione file di Azure utilizzando [Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322), disponibile per il download nell'Area download Microsoft.

## <a name="backup"></a>Backup 
Come le soluzioni antivirus, le soluzioni di backup possono causare il richiamo di file archiviati a livelli. È consigliabile usare una soluzione di backup nel cloud per eseguire il backup della condivisione file di Azure anziché usare un prodotto di backup locale.

Se si usa una soluzione di backup locale, i backup devono essere eseguiti in un server nel gruppo di sincronizzazione con il tiering cloud disabilitato. Quando si esegue un ripristino, usare le opzioni di ripristino a livello di volume o a livello di file. I file ripristinati tramite l'opzione di ripristino a livello di file vengono sincronizzati con tutti gli endpoint del gruppo di sincronizzazione e i file esistenti vengono sostituiti dalla versione ripristinata dal backup.  Nel ripristino a livello di volume i file non vengono sostituiti dalla versione più recente nella condivisione file di Azure o in altri endpoint server.

> [!Note]  
> Il ripristino bare metal può avere risultati imprevisti e non è attualmente supportato.

> [!Note]  
> Con la versione 9 dell'agente di Sincronizzazione file di Azure, gli snapshot VSS (inclusa la scheda Versioni precedenti) sono ora supportati nei volumi in cui è abilitato il tiering cloud. Tuttavia, è necessario abilitare la compatibilità delle versioni precedenti tramite PowerShell.However, you must enable previous version compatibility through PowerShell. [Informazioni](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md)
* [Pianificazione per la distribuzione dei file di Azure](storage-files-planning.md)
* [Distribuire i file di Azure](storage-files-deployment-guide.md)
* [Distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
* [Monitorare Sincronizzazione file di Azure](storage-sync-files-monitoring.md)