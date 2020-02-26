---
title: Pianificazione per la distribuzione di Sincronizzazione file di Azure | Microsoft Docs
description: Informazioni sugli aspetti da considerare quando si pianifica una distribuzione di File di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0684f626553946619a0db2cd895df39576bd17b9
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598256"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Pianificazione per la distribuzione di Sincronizzazione file di Azure
[File di Azure](storage-files-introduction.md) può essere distribuito in due modi principali: montando direttamente le condivisioni file di Azure senza server o memorizzando nella cache le condivisioni file di Azure in locale usando sincronizzazione file di Azure. L'opzione di distribuzione scelta cambia gli elementi che è necessario prendere in considerazione durante la pianificazione della distribuzione. 

- **Montaggio diretto di una condivisione file di Azure**: poiché file di Azure fornisce l'accesso SMB, è possibile montare condivisioni file di Azure in locale o nel cloud usando il client SMB standard disponibile in Windows, MacOS e Linux. Poiché le condivisioni file di Azure sono senza server, la distribuzione per gli scenari di produzione non richiede la gestione di un dispositivo file server o NAS. Ciò significa che non è necessario applicare patch software o scambiare dischi fisici. 

- **Memorizzare nella cache la condivisione file di Azure in locale con sincronizzazione file di Azure**: sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in file di Azure, garantendo al tempo stesso la flessibilità, le prestazioni e la compatibilità di un file server locale. Sincronizzazione file di Azure trasforma un server Windows locale (o cloud) in una cache rapida della condivisione file di Azure. 

Questo articolo riguarda principalmente le considerazioni sulla distribuzione per la distribuzione di Sincronizzazione file di Azure. Per pianificare una distribuzione di condivisioni file di Azure da montare direttamente da un client locale o cloud, vedere [pianificazione di una distribuzione di file di Azure](storage-files-planning.md).

## <a name="management-concepts"></a>Concetti relativi alla gestione
Una distribuzione Sincronizzazione file di Azure dispone di tre oggetti di gestione fondamentali:

- **Condivisione file di Azure**: una condivisione file di Azure è una condivisione file cloud senza server che fornisce l' *endpoint cloud* di una relazione di sincronizzazione sincronizzazione file di Azure. È possibile accedere ai file in una condivisione file di Azure direttamente con SMB o il protocollo filerest, anche se si consiglia di accedere principalmente ai file tramite la cache di Windows Server quando la condivisione file di Azure viene usata con Sincronizzazione file di Azure. Ciò è dovuto al fatto che File di Azure attualmente non dispone di un meccanismo di rilevamento delle modifiche efficiente, ad esempio Windows Server, pertanto le modifiche alla condivisione file di Azure verranno propagate di nuovo agli endpoint server.
- **Endpoint server**: il percorso in Windows Server sincronizzato con una condivisione file di Azure. Può trattarsi di una cartella specifica in un volume o nella radice del volume. Possono esistere più endpoint server nello stesso volume se i relativi spazi dei nomi non si sovrappongono.
- **Gruppo di sincronizzazione**: oggetto che definisce la relazione di sincronizzazione tra un **endpoint cloud**o una condivisione file di Azure e un endpoint server. Gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Se, ad esempio, si dispone di due set distinti di file che si desidera gestire con Sincronizzazione file di Azure, è necessario creare due gruppi di sincronizzazione e aggiungere endpoint diversi a ogni gruppo di sincronizzazione.

### <a name="azure-file-share-management-concepts"></a>Concetti relativi alla gestione delle condivisioni file di Azure
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Concetti relativi alla gestione di Sincronizzazione file di Azure
I gruppi di sincronizzazione vengono distribuiti nei **servizi di sincronizzazione archiviazione**, che sono oggetti di livello superiore che registrano i server da usare con sincronizzazione file di Azure e contengono le relazioni dei gruppi di sincronizzazione. Il servizio di sincronizzazione archiviazione è un peer della risorsa account di archiviazione e può essere distribuito in modo analogo a questa nei gruppi di risorse di Azure. Un servizio di sincronizzazione archiviazione può creare gruppi di sincronizzazione che contengono condivisioni file di Azure tra più account di archiviazione e più server Windows registrati.

Prima di poter creare un gruppo di sincronizzazione in un servizio di sincronizzazione archiviazione, è necessario innanzitutto registrare un server Windows con il servizio di sincronizzazione archiviazione. Viene creato un oggetto **server registrato** che rappresenta una relazione di trust tra il server o il cluster e il servizio di sincronizzazione archiviazione. Per registrare un servizio di sincronizzazione archiviazione, è prima necessario installare l'agente di Sincronizzazione file di Azure nel server. Un singolo server o cluster può essere registrato con un solo servizio di sincronizzazione archiviazione alla volta.

Un gruppo di sincronizzazione contiene un endpoint cloud, una condivisione file di Azure e almeno un endpoint server. L'oggetto endpoint server contiene le impostazioni che configurano la funzionalità di suddivisione in **livelli nel cloud** , che fornisce la funzionalità di memorizzazione nella cache di sincronizzazione file di Azure. Per eseguire la sincronizzazione con una condivisione file di Azure, l'account di archiviazione che contiene la condivisione file di Azure deve trovarsi nella stessa area di Azure del servizio di sincronizzazione archiviazione.

### <a name="management-guidance"></a>Linee guida per la gestione
Quando si distribuisce Sincronizzazione file di Azure, è consigliabile:

- Distribuzione di condivisioni file di Azure 1:1 con condivisioni file di Windows. L'oggetto endpoint server offre un elevato livello di flessibilità per la configurazione della topologia di sincronizzazione sul lato server della relazione di sincronizzazione. Per semplificare la gestione, fare in modo che il percorso dell'endpoint server corrisponda al percorso della condivisione file di Windows. 

- Usare il minor numero possibile di servizi di sincronizzazione archiviazione. Questo consente di semplificare la gestione quando sono presenti gruppi di sincronizzazione che contengono più endpoint server, perché un server Windows può essere registrato solo in un servizio di sincronizzazione archiviazione alla volta. 

- Prestare attenzione alle limitazioni IOPS di un account di archiviazione durante la distribuzione di condivisioni file di Azure. Idealmente, è consigliabile eseguire il mapping delle condivisioni file 1:1 con gli account di archiviazione, ma ciò potrebbe non essere sempre possibile a causa di diversi limiti e restrizioni, sia dall'organizzazione che da Azure. Quando non è possibile avere una sola condivisione file distribuita in un account di archiviazione, valutare quali condivisioni saranno molto attive e quali condivisioni saranno meno attive per assicurarsi che le condivisioni file più calde non vengano inserite nello stesso account di archiviazione.

## <a name="windows-file-server-considerations"></a>Considerazioni su Windows file server
Per abilitare la funzionalità di sincronizzazione in Windows Server, è necessario installare il Sincronizzazione file di Azure agente scaricabile. L'agente di Sincronizzazione file di Azure fornisce due componenti principali: `FileSyncSvc.exe`, il servizio Windows in background responsabile del monitoraggio delle modifiche negli endpoint server e dell'avvio delle sessioni di sincronizzazione e la `StorageSync.sys`, un filtro file system che consente la suddivisione in livelli cloud e il ripristino di emergenza veloce.  

### <a name="operating-system-requirements"></a>Requisiti del sistema operativo
Sincronizzazione file di Azure è supportato con le seguenti versioni di Windows Server:

| Versione | SKU supportati | Opzioni di distribuzione supportate |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, standard e Internet | Full e Core |
| Windows Server 2016 | Datacenter, standard e server di archiviazione | Full e Core |
| Windows Server 2012 R2 | Datacenter, standard e server di archiviazione | Full e Core |

Le versioni future di Windows Server verranno aggiunte non appena verranno rilasciate.

> [!Important]  
> È consigliabile mantenere aggiornati tutti i server usati con Sincronizzazione file di Azure in base agli aggiornamenti più recenti disponibili in Windows Update. 

### <a name="minimum-system-resources"></a>Risorse di sistema minime
Sincronizzazione file di Azure richiede un server, fisico o virtuale, con almeno una CPU e un minimo di 2 GiB di memoria.

> [!Important]  
> Se il server è in esecuzione in una macchina virtuale con la memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.

Per la maggior parte dei carichi di lavoro di produzione, non è consigliabile configurare un server di sincronizzazione Sincronizzazione file di Azure con solo i requisiti minimi. Per ulteriori informazioni, vedere [risorse di sistema consigliate](#recommended-system-resources) .

### <a name="recommended-system-resources"></a>Risorse di sistema consigliate
Analogamente a qualsiasi funzionalità o applicazione server, i requisiti delle risorse di sistema per Sincronizzazione file di Azure sono determinati dalla scalabilità della distribuzione. le distribuzioni di dimensioni maggiori in un server richiedono più risorse di sistema. Per Sincronizzazione file di Azure, la scala è determinata dal numero di oggetti negli endpoint server e dalla varianza del set di dati. Un singolo server può avere endpoint server in più gruppi di sincronizzazione e il numero di oggetti elencati nella tabella seguente include gli account per lo spazio dei nomi completo a cui è collegato un server. 

Ad esempio, endpoint server A con 10 milioni oggetti + endpoint server B con oggetti 10 milioni = 20 milioni oggetti. Per questo esempio di distribuzione, si consigliano 8 CPU, 16 GiB di memoria per lo stato stabile e (se possibile) 48 GiB di memoria per la migrazione iniziale.
 
I dati dello spazio dei nomi vengono archiviati in memoria per motivi di prestazioni. Per questo motivo, gli spazi dei nomi più grandi richiedono una maggiore quantità di memoria per garantire prestazioni ottimali e una maggiore varianza richiede più CPU per l'elaborazione. 
 
Nella tabella seguente sono state fornite sia la dimensione dello spazio dei nomi sia una conversione alla capacità per le condivisioni file di uso generico tipiche, in cui la dimensione media del file è 512 KiB. Se le dimensioni del file sono inferiori, è consigliabile aggiungere memoria aggiuntiva per la stessa quantità di capacità. Basare la configurazione della memoria sulle dimensioni dello spazio dei nomi.

| Dimensioni spazio dei nomi-file & Directory (milioni)  | Capacità tipica (TiB)  | Core CPU  | Memoria consigliata (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (sincronizzazione iniziale)/2 (varianza tipica)      |
| 5        | 2.3     | 2        | 16 (sincronizzazione iniziale)/4 (varianza tipica)    |
| 10       | 4.7     | 4        | 32 (sincronizzazione iniziale)/8 (varianza tipica)   |
| 30       | 14,0    | 8        | 48 (sincronizzazione iniziale)/16 (varianza tipica)   |
| 50       | 23,3    | 16       | 64 (sincronizzazione iniziale)/32 (varianza tipica)  |
| 100*     | 46,6    | 32       | 128 (sincronizzazione iniziale)/32 (varianza tipica)  |

\*la sincronizzazione di più di 100 milioni file & Directory non è consigliabile in questo momento. Si tratta di un limite flessibile in base alle soglie testate. Per ulteriori informazioni, vedere [obiettivi di scalabilità e prestazioni file di Azure](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> La sincronizzazione iniziale di uno spazio dei nomi è un'operazione complessa ed è consigliabile allocare ulteriore memoria fino al completamento della sincronizzazione iniziale. Questa operazione non è obbligatoria, ma può velocizzare la sincronizzazione iniziale. 
> 
> La varianza tipica è il 0,5% dello spazio dei nomi che cambia al giorno. Per livelli più elevati di varianza, provare ad aggiungere altro CPU. 

- Un volume collegato al computer locale formattato con il file system NTFS.

### <a name="evaluation-cmdlet"></a>Cmdlet Evaluation
Prima di distribuire Sincronizzazione file di Azure, è necessario valutare se è compatibile con il sistema utilizzando il cmdlet Sincronizzazione file di Azure Evaluation. Questo cmdlet controlla la presenza di potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o una versione non supportata del sistema operativo. I controlli coprono la maggior parte, ma non tutte le funzionalità indicate di seguito. è consigliabile leggere attentamente la parte restante di questa sezione per assicurarsi che la distribuzione venga applicata senza problemi. 

Il cmdlet Evaluation può essere installato installando il modulo AZ PowerShell, che può essere installato seguendo le istruzioni riportate qui: [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

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

### <a name="file-system-compatibility"></a>Compatibilità del file System
Sincronizzazione file di Azure è supportato solo su volumi NTFS collegati direttamente. Direct Attached Storage (DAS) in Windows Server significa che il sistema operativo Windows Server è proprietario del file system. È possibile fornire DAS tramite il fissaggio fisico dei dischi al file server, connettendo i dischi virtuali a una VM file server, ad esempio una macchina virtuale ospitata da Hyper-V, o anche tramite ISCSI.

Sono supportati solo i volumi NTFS. ReFS, FAT, FAT32 e altri file System non sono supportati.

La tabella seguente illustra lo stato di interoperabilità delle funzionalità di file system NTFS: 

| Funzionalità | Stato del supporto | Note |
|---------|----------------|-------|
| Elenchi di controllo di accesso (ACL) | supporto completo | Gli elenchi di controllo di accesso discrezionale di tipo Windows sono conservati da Sincronizzazione file di Azure e vengono applicati da Windows Server negli endpoint server. Gli ACL possono anche essere applicati quando si monta direttamente la condivisione file di Azure, ma questa operazione richiede una configurazione aggiuntiva. Per ulteriori informazioni, vedere la sezione relativa all' [identità](#identity) . |
| Collegamenti reali | Operazione ignorata | |
| Collegamenti simbolici | Operazione ignorata | |
| Punti di montaggio | Parzialmente supportato | I punti di montaggio possono corrispondere alla radice di un endpoint server, ma vengono ignorati se sono contenuti nello spazio dei nomi di un endpoint server. |
| Giunzioni | Operazione ignorata | Ad esempio, le cartelle DfrsrPrivate e DFSRoots del file system distribuito. |
| Punti di analisi | Operazione ignorata | |
| Compressione NTFS | supporto completo | |
| File sparse | supporto completo | I file sparse vengono sincronizzati (non bloccati), ma vengono sincronizzati nel cloud come file completi. Se il contenuto del file viene modificato nel cloud (o in un altro server), il file non è più di tipo sparse quando viene scaricata la modifica. |
| Flussi di dati alternativi (ADS) | Mantenuti, ma non sincronizzati | Ad esempio, i tag di classificazione creati tramite Infrastruttura di classificazione file non vengono sincronizzati. I tag di classificazione esistenti sui file in ognuno degli endpoint server non subiscono variazioni. |

<a id="files-skipped"></a>Sincronizzazione file di Azure ignoreranno anche alcuni file temporanei e le cartelle di sistema:

| File/cartella | Note |
|-|-|
| pagefile.sys | File specifico del sistema |
| Desktop.ini | File specifico del sistema |
| thumbs.db | File temporaneo per anteprime |
| ehthumbs.db | File temporaneo per le anteprime dei supporti |
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
**Windows server 2016 e Windows server 2019**   
La deduplicazione dei dati è supportata nei volumi con cloud a livelli abilitato in Windows Server 2016 e Windows Server 2019. L'abilitazione della deduplicazione dati in un volume con la suddivisione in livelli cloud abilitata consente di memorizzare nella cache più file in locale senza effettuare il provisioning di più spazio di archiviazione. 

Quando la deduplicazione dei dati è abilitata in un volume con la suddivisione in livelli nel cloud abilitata, i file ottimizzati per deduplicazione all'interno del percorso dell'endpoint server verranno suddivisi a livelli in un file normale in base alle impostazioni dei criteri di suddivisione in livelli nel cloud. Una volta a livelli i file ottimizzati per deduplicazione, il processo di deduplicazione dati Garbage Collection verrà eseguito automaticamente per recuperare spazio su disco rimuovendo blocchi non necessari a cui gli altri file del volume non fanno più riferimento.

Si noti che il risparmio del volume si applica solo al server; i dati nella condivisione file di Azure non verranno deduplicati.

> [!Note]  
> Per supportare la deduplicazione dei dati nei volumi con la suddivisione in livelli nel cloud abilitata in Windows Server 2019, è necessario installare Windows Update [KB4520062](https://support.microsoft.com/help/4520062) ed è necessario sincronizzazione file di Azure versione dell'agente 9.0.0.0 o successiva.

**Windows Server 2012 R2**  
Sincronizzazione file di Azure non supporta la deduplicazione dei dati e la suddivisione in livelli nel cloud nello stesso volume di Windows Server 2012 R2. Se la deduplicazione dei dati è abilitata in un volume, la suddivisione in livelli cloud deve essere disabilitata. 

**Note**
- Se la deduplicazione dei dati viene installata prima di installare l'agente di Sincronizzazione file di Azure, è necessario riavviare il servizio per supportare la deduplicazione dei dati e la suddivisione in livelli nel cloud nello stesso volume.
- Se la deduplicazione dei dati è abilitata in un volume dopo l'abilitazione della suddivisione in livelli nel cloud, il processo di ottimizzazione della deduplicazione iniziale ottimizza i file nel volume che non sono già a livelli e avrà l'effetto seguente sulla suddivisione in livelli nel cloud:
    - I criteri di spazio libero continueranno a essere suddivisi in file di livello in base allo spazio disponibile nel volume usando mappa termica.
    - I criteri di data ignoreranno la suddivisione in livelli dei file che potrebbero essere stati altrimenti idonei per la suddivisione in livelli a causa del processo di ottimizzazione della deduplicazione per l'accesso ai file.
- Per i processi di ottimizzazione della deduplicazione in corso, la suddivisione in livelli cloud con i criteri di data verrà posticipata dall'impostazione [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) Deduplicazione dati, se il file non è già a livelli. 
    - Esempio: se l'impostazione MinimumFileAgeDays è sette giorni e i criteri di data di suddivisione in livelli nel cloud sono di 30 giorni, i criteri di data effettueranno il livello dei file dopo 37 giorni.
    - Nota: quando un file è suddiviso in livelli per Sincronizzazione file di Azure, il processo di ottimizzazione della deduplicazione ignorerà il file.
- Se un server che esegue Windows Server 2012 R2 con l'agente di Sincronizzazione file di Azure installato viene aggiornato a Windows Server 2016 o Windows Server 2019, è necessario eseguire i passaggi seguenti per supportare la deduplicazione dei dati e la suddivisione in livelli nel cloud nello stesso volume:  
    - Disinstallare l'agente di Sincronizzazione file di Azure per Windows Server 2012 R2 e riavviare il server.
    - Scaricare l'agente di Sincronizzazione file di Azure per la nuova versione del sistema operativo del server (Windows Server 2016 o Windows Server 2019).
    - Installare l'agente Sincronizzazione file di Azure e riavviare il server.  
    
    Nota: le impostazioni di configurazione del Sincronizzazione file di Azure nel server vengono mantenute quando l'agente viene disinstallato e reinstallato.

### <a name="distributed-file-system-dfs"></a>File system distribuito (DFS)
Sincronizzazione file di Azure supporta l'interoperabilità con Spazi dei nomi DFS (DFS-N) e Replica DFS (DFS-R).

**Spazi dei nomi DFS (DFS-N)** : Sincronizzazione file di Azure è completamente supportato nei server DFS-N. È possibile installare l'agente Sincronizzazione file di Azure in uno o più membri DFS-N per sincronizzare i dati tra gli endpoint server e l'endpoint cloud. Per altre informazioni, vedere [Informazioni generali su Spazi dei nomi DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replica DFS (DFS-r)** : poiché DFS-r e sincronizzazione file di Azure sono entrambe soluzioni di replica, nella maggior parte dei casi è consigliabile sostituire DFS-r con sincronizzazione file di Azure. Esistono tuttavia diversi scenari in cui si vuole usare DFS-R e Sincronizzazione file di Azure insieme:

- Si esegue la migrazione da una distribuzione di DFS-R a una distribuzione di Sincronizzazione file di Azure. Per altre informazioni, vedere [Eseguire la migrazione di una distribuzione di Replica DFS (DFS-R) in Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Non tutti i server locali che necessitano di una copia dei dati del file possono essere connessi direttamente a Internet.
- I server di succursale consolidano i dati in un unico server di hub per cui si vorrebbe usare Sincronizzazione file di Azure.

Per il funzionamento affiancato di Sincronizzazione file di Azure e DFS-R:

1. Nei volumi con cartelle replicate con DFS-R deve essere disabilitata la suddivisione in livelli cloud di Sincronizzazione file di Azure.
2. Non devono essere configurati endpoint server nelle cartelle di replica di sola lettura di DFS-R.

Per altre informazioni, vedere la [panoramica di Replica DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
L'utilizzo di Sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportato e può causare risultati imprevisti. L'installazione dell'agente e la registrazione del server devono avvenire dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="windows-search"></a>Ricerca di Windows
Se in un endpoint server è abilitata la suddivisione in livelli nel cloud, i file suddivisi in livelli vengono ignorati e non vengono indicizzati dalla ricerca di Windows. I file non suddivisi in livelli vengono indicizzati correttamente.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Altre soluzioni di gestione dell'archiviazione gerarchica
Con Sincronizzazione file di Azure non devono essere usate altre soluzioni di gestione dell'archiviazione gerarchica.

## <a name="identity"></a>Identità
Sincronizzazione file di Azure funziona con l'identità basata su AD standard senza alcuna configurazione speciale oltre alla configurazione della sincronizzazione. Quando si usa Sincronizzazione file di Azure, l'aspettativa generale è che la maggior parte degli accessi attraversino i server di memorizzazione nella cache Sincronizzazione file di Azure, anziché tramite la condivisione file di Azure. Poiché gli endpoint server si trovano in Windows Server e Windows Server ha supportato gli ACL di Active Directory e di Windows per un periodo di tempo molto lungo, non è necessario nulla oltre ad assicurare che i file server Windows registrati con il servizio di sincronizzazione archiviazione siano aggiunti a un dominio. Sincronizzazione file di Azure archivierà gli ACL nei file nella condivisione file di Azure e li eseguirà la replica in tutti gli endpoint server.

Anche se le modifiche apportate direttamente alla condivisione file di Azure possono richiedere più tempo per la sincronizzazione con gli endpoint server nel gruppo di sincronizzazione, è anche possibile assicurarsi di applicare le autorizzazioni di Active Directory sulla condivisione file direttamente nel cloud. A tale scopo, è necessario aggiungere il proprio account di archiviazione a un dominio ad locale, esattamente come il modo in cui i file server Windows sono aggiunti a un dominio. Per ulteriori informazioni sul dominio che aggiunge il proprio account di archiviazione a un Active Directory di proprietà del cliente, vedere [File di Azure Active Directory Overview](storage-files-active-directory-overview.md).

> [!Important]  
> Il dominio che aggiunge l'account di archiviazione a Active Directory non è necessario per distribuire correttamente Sincronizzazione file di Azure. Si tratta di un passaggio rigorosamente facoltativo che consente alla condivisione file di Azure di applicare gli ACL locali quando gli utenti montano direttamente la condivisione file di Azure.

## <a name="networking"></a>Rete
L'agente di Sincronizzazione file di Azure comunica con il servizio di sincronizzazione archiviazione e la condivisione file di Azure usando il protocollo di Sincronizzazione file di Azure REST e il protocollo filerest, entrambi che usano sempre HTTPS sulla porta 443. SMB non viene mai usato per caricare o scaricare dati tra il server Windows e la condivisione file di Azure. Poiché la maggior parte delle organizzazioni consente il traffico HTTPS sulla porta 443, come requisito per visitare la maggior parte dei siti Web, la configurazione di rete speciale non è in genere necessaria per la distribuzione di Sincronizzazione file di Azure.

In base ai criteri dell'organizzazione o ai requisiti normativi specifici, potrebbe essere necessaria una comunicazione più restrittiva con Azure e pertanto Sincronizzazione file di Azure fornisce diversi meccanismi per la configurazione della rete. In base ai requisiti, è possibile:

- Sincronizzazione del tunnel e traffico di caricamento/download di file attraverso la rete VPN di Azure o ExpressRoute. 
- Usare le funzionalità di File di Azure e di rete di Azure, ad esempio endpoint di servizio ed endpoint privati.
- Configurare Sincronizzazione file di Azure per supportare il proxy nell'ambiente in uso.
- Limitare l'attività di rete da Sincronizzazione file di Azure.

Per ulteriori informazioni sulla configurazione della funzionalità di rete di Sincronizzazione file di Azure, vedere:
- [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md)
- [Assicurandosi che Sincronizzazione file di Azure sia una scelta ideale nel Data Center](storage-sync-files-server-registration.md)

## <a name="encryption"></a>Crittografia
Quando si usa Sincronizzazione file di Azure, esistono tre diversi livelli di crittografia da considerare: la crittografia sull'archiviazione inattiva di Windows Server, la crittografia in transito tra l'agente Sincronizzazione file di Azure e Azure e la crittografia dei dati inattivi nella condivisione file di Azure. 

### <a name="windows-server-encryption-at-rest"></a>Crittografia di Windows Server inattivi 
Esistono due strategie per la crittografia dei dati in Windows Server che funzionano in genere con Sincronizzazione file di Azure: la crittografia sotto la file system in modo che il file system e tutti i dati scritti in esso siano crittografati e la crittografia all'interno del formato di file. Questi metodi non si escludono a vicenda. possono essere usati insieme, se necessario, poiché lo scopo della crittografia è diverso.

Per fornire la crittografia sotto la file system, Windows Server fornisce la posta in arrivo di BitLocker. BitLocker è completamente trasparente per Sincronizzazione file di Azure. Il motivo principale per cui si usa un meccanismo di crittografia come BitLocker è quello di impedire la exfiltration fisica dei dati dal Data Center locale da parte di un utente che ruba i dischi e impedisce a sideload un sistema operativo non autorizzato di eseguire letture/scritture non autorizzate sui dati. Per ulteriori informazioni su BitLocker, vedere [Panoramica di BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview).

I prodotti di terze parti che funzionano in modo analogo a BitLocker, in quanto si trovano sotto il volume NTFS, dovrebbero funzionare in modo completamente trasparente con Sincronizzazione file di Azure. 

L'altro metodo principale per la crittografia dei dati consiste nel crittografare il flusso di dati del file quando l'applicazione salva il file. Alcune applicazioni possono eseguire questa operazione in modo nativo, ma questo non è in genere il caso. Un esempio di un metodo per la crittografia del flusso di dati del file è Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. Il motivo principale per cui si usa un meccanismo di crittografia come AIP/RMS è impedire ai dati exfiltration di dati dalla condivisione file da parte degli utenti di copiarli in posizioni alternative, ad esempio in un'unità flash o inviarli tramite posta elettronica a un utente non autorizzato. Quando il flusso di dati di un file viene crittografato come parte del formato di file, il file continuerà a essere crittografato nella condivisione file di Azure. 

Sincronizzazione file di Azure non interagisce con NTFS Encrypted File System (EFS NTFS) o con soluzioni di crittografia di terze parti che si trovano sopra il file system ma sotto il flusso di dati del file. 

### <a name="encryption-in-transit"></a>Crittografia in transito
Sincronizzazione file di Azure agente comunica con il servizio di sincronizzazione archiviazione e la condivisione file di Azure usando il protocollo di Sincronizzazione file di Azure REST e il protocollo filerest, entrambi utilizzano sempre HTTPS sulla porta 443. Sincronizzazione file di Azure non invia richieste non crittografate tramite HTTP. 

Gli account di archiviazione di Azure contengono un'opzione per richiedere la crittografia in transito, abilitata per impostazione predefinita. Anche se l'opzione a livello di account di archiviazione è disabilitata, vale a dire che le connessioni non crittografate alle condivisioni file di Azure sono possibili, Sincronizzazione file di Azure utilizzeranno comunque i canali crittografati solo per accedere alla condivisione file.

Il motivo principale per disabilitare la crittografia in transito per l'account di archiviazione consiste nel supportare un'applicazione legacy che deve essere eseguita in un sistema operativo precedente, ad esempio Windows Server 2008 R2 o una distribuzione Linux precedente, che comunica direttamente con una condivisione file di Azure. Se l'applicazione legacy comunica con la cache di Windows Server della condivisione file, la disattivazione di questa impostazione non avrà alcun effetto. 

Si consiglia vivamente di garantire che la crittografia dei dati in transito sia abilitata.

Per altre informazioni sulla crittografia in transito, vedere [Richiedere il trasferimento sicuro in Archiviazione di Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Crittografia di condivisione file di Azure inattiva
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Livelli di archiviazione
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Consente di estendere le condivisioni file standard fino a 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilità internazionale
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Disponibilità dell'area sincronizzazione file di Azure
Sincronizzazione file di Azure è disponibile nelle aree geografiche seguenti:

| Cloud di Azure | Area geografica | Area di Azure | Codice area |
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
| Pubblico | Francia | Francia meridionale * | `francesouth` |
| Pubblico | India | India centrale | `centralindia` |
| Pubblico | India | India meridionale | `southindia` |
| Pubblico | Giappone | Giappone orientale | `japaneast` |
| Pubblico | Giappone | Giappone occidentale | `japanwest` |
| Pubblico | Corea del Sud | Corea centrale | `koreacentral` |
| Pubblico | Corea del Sud | Corea meridionale | `koreasouth` |
| Pubblico | Sud Africa | Sudafrica settentrionale | `southafricanorth` |
| Pubblico | Sud Africa | Sudafrica occidentale * | `southafricawest` |
| Pubblico | Emirati Arabi Uniti | Emirati Arabi Uniti centrali * | `uaecentral` |
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

Per le aree contrassegnate con asterischi, è necessario contattare il supporto di Azure per richiedere l'accesso ad archiviazione di Azure in tali aree. Il processo è illustrato in [questo documento](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="redundancy"></a>Ridondanza
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> L'archiviazione con ridondanza geografica e con ridondanza geografica è in grado di eseguire manualmente il failover dell'archiviazione nell'area secondaria. Si consiglia di non eseguire questa operazione al di fuori di un'emergenza quando si utilizza Sincronizzazione file di Azure a causa dell'aumento della probabilità di perdita di dati. In caso di emergenza in cui si vuole avviare un failover manuale dello spazio di archiviazione, è necessario aprire un caso di supporto con Microsoft per ottenere Sincronizzazione file di Azure riprendere la sincronizzazione con l'endpoint secondario.

## <a name="migration"></a>Migrazione
Se si dispone di un file server Windows esistente, Sincronizzazione file di Azure possibile installarlo direttamente, senza che sia necessario spostare i dati in un nuovo server. Se si prevede di eseguire la migrazione a una nuova file server di Windows nell'ambito dell'adozione di Sincronizzazione file di Azure, esistono diversi approcci possibili per spostare i dati:

- Creare endpoint server per la condivisione file precedente e la nuova condivisione file e consentire Sincronizzazione file di Azure sincronizzare i dati tra gli endpoint server. Il vantaggio di questo approccio consiste nel fatto che rende molto semplice sottoscrivere lo spazio di archiviazione nel nuovo file server, dal momento che Sincronizzazione file di Azure è in grado di supportare la suddivisione in livelli nel cloud. Quando si è pronti, è possibile ridurre gli utenti finali alla condivisione file nel nuovo server e rimuovere l'endpoint server della condivisione file precedente.

- Creare un endpoint server solo sul nuovo file server e copiare i dati in dalla condivisione file precedente usando `robocopy`. A seconda della topologia delle condivisioni file nel nuovo server (il numero di condivisioni presenti in ogni volume, la quantità di spazio disponibile per ogni volume e così via), potrebbe essere necessario effettuare temporaneamente il provisioning di spazio di archiviazione aggiuntivo, perché si prevede che `robocopy` dal server precedente al nuovo server nel Data Center locale verrà completato più velocemente di quanto Sincronizzazione file di Azure sposti i dati in Azure.

È anche possibile usare Data Box per migrare i dati in una distribuzione di Sincronizzazione file di Azure. Nella maggior parte dei casi, quando i clienti vogliono usare Data Box per inserire i dati, lo fanno perché pensano che aumenteranno la velocità della distribuzione o perché saranno utili per gli scenari di larghezza di banda vincolata. Sebbene sia vero che l'uso di un Data Box per inserire dati nella distribuzione di Sincronizzazione file di Azure ridurrà l'utilizzo della larghezza di banda, sarà probabilmente più veloce per la maggior parte degli scenari eseguire un caricamento di dati online tramite uno dei metodi descritti in precedenza. Per altre informazioni su come usare Data Box per inserire dati nella distribuzione di Sincronizzazione file di Azure, vedere [eseguire la migrazione dei dati in sincronizzazione file di Azure con Azure Data Box](storage-sync-offline-data-transfer.md).

Un errore comune dei clienti quando si esegue la migrazione dei dati nella nuova distribuzione di Sincronizzazione file di Azure consiste nel copiare i dati direttamente nella condivisione file di Azure, anziché nei file server Windows. Anche se Sincronizzazione file di Azure identificherà tutti i nuovi file nella condivisione file di Azure e li sincronizza nuovamente con le condivisioni file di Windows, questo è in genere molto più lento rispetto al caricamento dei dati tramite la file server di Windows. Molti strumenti di copia di Azure, ad esempio AzCopy, hanno lo svantaggio aggiuntivo di non copiare tutti i metadati importanti di un file, ad esempio timestamp e ACL.

## <a name="antivirus"></a>Antivirus
Un antivirus esegue l'analisi dei file alla ricerca di codice dannoso noto e può quindi causare il richiamo di file archiviati a livelli. Nella versione 4.0 e successive dell'agente di Sincronizzazione file di Azure, per i file archiviati a livelli è impostato l'attributo sicuro di Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS. È consigliabile consultare il fornitore del software per ottenere informazioni su come configurare la soluzione in modo che non legga i file per cui è impostato questo attributo (molte lo fanno automaticamente). 

Le soluzioni antivirus Microsoft, Windows Defender e System Center Endpoint Protection (SCEP), escludono automaticamente dalla lettura i file con questo attributo. Un test su entrambe le soluzioni ha identificato un problema secondario: quando si aggiunge un server a un gruppo di sincronizzazione esistente, i file di dimensioni inferiori a 800 byte vengono richiamati (scaricati) nel nuovo server. Questi file rimangono nel nuovo server e non vengono suddivisi in livelli, poiché non soddisfano il requisito relativo alle dimensioni della suddivisione in livelli (> 64 KB).

> [!Note]  
> I fornitori di software antivirus possono controllare la compatibilità tra il prodotto e Sincronizzazione file di Azure utilizzando il [gruppo di test di compatibilità antivirus sincronizzazione file di Azure](https://www.microsoft.com/download/details.aspx?id=58322), disponibile per il download nell'area download Microsoft.

## <a name="backup"></a>Backup 
Come le soluzioni antivirus, le soluzioni di backup possono causare il richiamo di file archiviati a livelli. È consigliabile usare una soluzione di backup nel cloud per eseguire il backup della condivisione file di Azure anziché usare un prodotto di backup locale.

Se si usa una soluzione di backup locale, i backup devono essere eseguiti in un server nel gruppo di sincronizzazione con la suddivisione in livelli nel cloud disabilitata. Quando si esegue un ripristino, usare le opzioni di ripristino a livello di volume o a livello di file. I file ripristinati tramite l'opzione di ripristino a livello di file vengono sincronizzati con tutti gli endpoint del gruppo di sincronizzazione e i file esistenti vengono sostituiti dalla versione ripristinata dal backup.  Nel ripristino a livello di volume i file non vengono sostituiti dalla versione più recente nella condivisione file di Azure o in altri endpoint server.

> [!Note]  
> Il ripristino bare metal può avere risultati imprevisti e non è attualmente supportato.

> [!Note]  
> Con la versione 9 della Sincronizzazione file di Azure Agent, gli snapshot VSS (inclusa la scheda versioni precedenti) sono ora supportati nei volumi in cui è abilitata la suddivisione in livelli nel cloud. Tuttavia, è necessario abilitare la compatibilità delle versioni precedente tramite PowerShell. [Informazioni](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md)
* [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
* [Come distribuire i file di Azure](storage-files-deployment-guide.md)
* [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
* [Monitorare Sincronizzazione file di Azure](storage-sync-files-monitoring.md)