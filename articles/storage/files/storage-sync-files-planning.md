---
title: Pianificazione per la distribuzione di Sincronizzazione file di Azure | Microsoft Docs
description: Informazioni sugli aspetti da considerare quando si pianifica una distribuzione di File di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f09e84d20b1a3c568eea015d92b93a99b8cf024e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036795"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Pianificazione per la distribuzione di Sincronizzazione file di Azure

:::row:::
    :::column:::
        [![Intervista e demo di presentazione di Sincronizzazione file di Azure - Fare clic per riprodurre.](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Sincronizzazione file di Azure è un servizio che consente di memorizzare nella cache un certo numero di condivisioni file di Azure in una macchina virtuale locale Windows Server o cloud. 
        
        Questo articolo introduce i concetti e le funzionalità di Sincronizzazione file di Azure. Dopo aver acquisito familiarità con Sincronizzazione file di Azure, si può seguire la [guida alla distribuzione di Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md) per provare questo servizio.        
    :::column-end:::
:::row-end:::

I file verranno archiviati nel cloud in [condivisioni file di Azure](storage-files-introduction.md). Le condivisioni file di Azure si possono usare in due modi: montando direttamente queste condivisioni file di Azure serverless (SMB) o memorizzandole nella cache locale con Sincronizzazione file di Azure. L'opzione di distribuzione scelta determina gli aspetti da considerare quando si pianifica la distribuzione. 

- **Montaggio diretto di una condivisione file di Azure**: Poiché File di Azure offre l'accesso tramite SMB, è possibile montare le condivisioni file di Azure in locale o nel cloud usando il client SMB standard disponibile in Windows, macOS e Linux. Poiché le condivisioni file di Azure sono serverless, la distribuzione per gli scenari di produzione non richiede la gestione di dispositivi NAS o file server. Ciò significa che non è necessario applicare patch software o sostituire dischi fisici. 

- **Memorizzare nella cache locale una condivisione file di Azure con Sincronizzazione file di Azure**: Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma un'istanza locale o cloud di Windows Server in una cache rapida della condivisione file di Azure. 

## <a name="management-concepts"></a>Concetti relativi alla gestione
Una distribuzione di Sincronizzazione file di Azure si basa su tre oggetti di gestione:

- **Condivisione file di Azure**: una condivisione file di Azure è una condivisione file serverless nel cloud che fornisce l'*endpoint cloud* di una relazione di sincronizzazione di Sincronizzazione file di Azure. I file in una condivisione file di Azure sono direttamente accessibili tramite i protocolli SMB o FileREST, ma quando la condivisione viene usata con Sincronizzazione file di Azure è preferibile accedere ai file principalmente tramite la cache di Windows Server. Il motivo è che, al contrario di Windows Server, attualmente File di Azure non dispone di un meccanismo di rilevamento delle modifiche efficiente. Di conseguenza, la propagazione agli endpoint server delle modifiche apportate direttamente alla condivisione file di Azure richiederà del tempo.
- **Endpoint server**: è il percorso nell'istanza di Windows Server sincronizzata con una condivisione file di Azure. Può trattarsi di una cartella specifica in un volume o della radice del volume. Possono esistere più endpoint server nello stesso volume se i relativi spazi dei nomi non si sovrappongono.
- **Gruppo di sincronizzazione**: l'oggetto che definisce la relazione di sincronizzazione tra un **endpoint cloud**, o condivisione file di Azure, e un endpoint server. Gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Se si hanno due set distinti di file da gestire con Sincronizzazione file di Azure, ad esempio, si creeranno due gruppi di sincronizzazione e si aggiungeranno endpoint diversi a ognuno.

### <a name="azure-file-share-management-concepts"></a>Concetti relativi alla gestione delle condivisioni file di Azure
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Concetti relativi alla gestione di Sincronizzazione file di Azure
I gruppi di sincronizzazione vengono distribuiti in **servizi di sincronizzazione archiviazione**, oggetti di livello superiore che registrano i server da usare con Sincronizzazione file di Azure e contengono le relazioni dei gruppi di sincronizzazione. Il servizio di sincronizzazione archiviazione è un peer della risorsa account di archiviazione e può essere distribuito in modo analogo a questa nei gruppi di risorse di Azure. Un servizio di sincronizzazione archiviazione può creare gruppi di sincronizzazione che contengono condivisioni file di Azure ubicate in più account di archiviazione e più istanze di Windows Server registrate.

Prima di poter creare un gruppo di sincronizzazione in un servizio di sincronizzazione archiviazione, è necessario registrare un'istanza di Windows Server nel servizio di sincronizzazione archiviazione. In questo modo si crea un oggetto **server registrato**, che rappresenta una relazione di trust tra il server (o cluster) e il servizio di sincronizzazione archiviazione. Per registrare un servizio di sincronizzazione archiviazione, è prima necessario installare l'agente di Sincronizzazione file di Azure nel server. È possibile registrare un server o un cluster con un solo servizio di sincronizzazione archiviazione alla volta.

Un gruppo di sincronizzazione contiene un endpoint cloud, una condivisione file di Azure e almeno un endpoint server. L'oggetto endpoint server contiene le impostazioni che configurano la capacità del **cloud a livelli**, che fornisce la funzionalità di memorizzazione nella cache di Sincronizzazione file di Azure. Per eseguire la sincronizzazione con una condivisione file di Azure, l'account di archiviazione che contiene la condivisione file di Azure deve trovarsi nella stessa area di Azure del servizio di sincronizzazione archiviazione.

### <a name="management-guidance"></a>Linee guida per la gestione
Quando si distribuisce Sincronizzazione file di Azure, è consigliabile seguire queste indicazioni:

- Distribuire le condivisioni file di Azure in un rapporto di 1:1 con le condivisioni file di Windows. L'oggetto endpoint server offre un elevato livello di flessibilità rispetto alla configurazione della topologia di sincronizzazione sul lato server della relazione di sincronizzazione. Per semplificare la gestione, fare in modo che il percorso dell'endpoint server corrisponda al percorso della condivisione file di Windows. 

- Usare il minor numero possibile di servizi di sincronizzazione archiviazione. Questo semplifica la gestione quando sono presenti gruppi di sincronizzazione che contengono più endpoint server, perché un'istanza di Windows Server può essere registrata in un solo servizio di sincronizzazione archiviazione alla volta. 

- Quando si distribuiscono condivisioni file di Azure, prestare attenzione alle limitazioni di un account di archiviazione in termini di operazioni di I/O al secondo. Idealmente, si dovrebbe mantenere un mapping 1:1 tra condivisioni file e account di archiviazione, ma questo non sempre è possibile, a causa dei diversi limiti e restrizioni imposti dall'organizzazione e da Azure. Quando non è possibile distribuire una sola condivisione file per account di archiviazione, valutare quali condivisioni saranno particolarmente attive e quali lo saranno meno, per assicurarsi di non inserire le condivisioni file più utilizzate nello stesso account di archiviazione.

## <a name="windows-file-server-considerations"></a>Considerazioni relative al file server Windows
Per abilitare la funzionalità di sincronizzazione in Windows Server, è necessario installare l'agente scaricabile di Sincronizzazione file di Azure. L'agente di Sincronizzazione file di Azure fornisce due componenti principali: `FileSyncSvc.exe`, il servizio di Windows in background responsabile del monitoraggio delle modifiche negli endpoint server e dell'avvio delle sessioni di sincronizzazione, e `StorageSync.sys`, un filtro del file system che abilita il cloud a livelli e il ripristino di emergenza rapido.  

### <a name="operating-system-requirements"></a>Requisiti del sistema operativo
Sincronizzazione file di Azure è supportato con le versioni di Windows Server seguenti:

| Versione | SKU supportati | Opzioni di distribuzione supportate |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standard e IoT | Full e Core |
| Windows Server 2016 | Datacenter, Standard e Storage Server | Full e Core |
| Windows Server 2012 R2 | Datacenter, Standard e Storage Server | Full e Core |

Le versioni future di Windows Server verranno aggiunte non appena verranno rilasciate.

> [!Important]  
> È consigliabile mantenere aggiornati tutti i server usati con Sincronizzazione file di Azure in base agli aggiornamenti più recenti disponibili in Windows Update. 

### <a name="minimum-system-resources"></a>Risorse di sistema minime
Sincronizzazione file di Azure richiede un server, fisico o virtuale, con almeno una CPU e un minimo di 2 GiB di memoria.

> [!Important]  
> Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.

Per la maggior parte dei carichi di lavoro di produzione, non è consigliabile configurare un server di sincronizzazione di Sincronizzazione file di Azure con requisiti minimi. Per altre informazioni, vedere [Risorse di sistema consigliate](#recommended-system-resources).

### <a name="recommended-system-resources"></a>Risorse di sistema consigliate
Come per qualsiasi applicazione o funzionalità server, i requisiti relativi alle risorse di sistema per Sincronizzazione file di Azure sono determinati dalla scala della distribuzione. Le distribuzioni di dimensioni maggiori in un server richiedono più risorse di sistema. Per Sincronizzazione file di Azure, la scala è determinata dal numero di oggetti negli endpoint server e dalla varianza del set di dati. Un singolo server può avere endpoint server in più gruppi di sincronizzazione e il numero di oggetti elencati nella tabella seguente tiene conto dell'intero spazio dei nomi a cui un server è collegato. 

Ad esempio, endpoint server A con 10 milioni di oggetti + endpoint server B con 10 milioni di oggetti = 20 milioni di oggetti. Per questa distribuzione di esempio si consigliano 8 CPU, 16 GiB di memoria per lo stato stabile e, se possibile, 48 GiB di memoria per la migrazione iniziale.
 
I dati dello spazio dei nomi vengono archiviati in memoria per motivi di prestazioni. Per questo motivo, gli spazi dei nomi più grandi richiedono una maggiore quantità di memoria per garantire prestazioni ottimali e una maggiore varianza richiede più CPU per l'elaborazione. 
 
La tabella seguente indica sia le dimensioni dello spazio dei nomi, sia una conversione in capacità per le condivisioni file tipiche per utilizzo generico, in cui la dimensione media dei file è 512 KiB. Se i file nel proprio ambiente sono di dimensioni inferiori, valutare la possibilità di aggiungere ulteriore memoria per la stessa quantità di capacità. Basare la configurazione della memoria sulle dimensioni dello spazio dei nomi.

| Dimensioni dello spazio dei nomi - File e directory (milioni)  | Capacità tipica (TiB)  | Core CPU  | Memoria consigliata (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (sincronizzazione iniziale)/2 (varianza tipica)      |
| 5        | 2.3     | 2        | 16 (sincronizzazione iniziale)/4 (varianza tipica)    |
| 10       | 4.7     | 4        | 32 (sincronizzazione iniziale)/8 (varianza tipica)   |
| 30       | 14,0    | 8        | 48 (sincronizzazione iniziale)/16 (varianza tipica)   |
| 50       | 23,3    | 16       | 64 (sincronizzazione iniziale)/32 (varianza tipica)  |
| 100*     | 46,6    | 32       | 128 (sincronizzazione iniziale)/32 (varianza tipica)  |

\*Al momento non è consigliabile sincronizzare più di 100 milioni di file e directory. Si tratta di un limite flessibile, basato sulle soglie testate. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per File di Azure](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> La sincronizzazione iniziale di uno spazio dei nomi è un'operazione impegnativa ed è consigliabile allocare ulteriore memoria fino al completamento della sincronizzazione iniziale. Questo non è obbligatorio, ma può velocizzare la sincronizzazione iniziale. 
> 
> La varianza tipica è un cambiamento dello 0,5% dello spazio dei nomi ogni giorno. Per livelli di varianza più elevati, provare ad aggiungere CPU. 

- Un volume collegato al computer locale formattato con il file system NTFS.

### <a name="evaluation-cmdlet"></a>Cmdlet di valutazione
Prima di distribuire Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il proprio sistema tramite il cmdlet di valutazione di Sincronizzazione file di Azure. Questo cmdlet consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. I controlli coprono la maggior parte delle funzionalità indicate di seguito, ma non tutte. È consigliabile leggere tutta la sezione con attenzione per assicurarsi che la distribuzione avvenga senza correttamente. 

È possibile installare il cmdlet di valutazione installando il modulo Azure PowerShell seguendo queste istruzioni: [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

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
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
Per visualizzare i risultati in CSV:
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path
    C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>Compatibilità del file system
Sincronizzazione file di Azure è supportato solo nei volumi NTFS collegati direttamente. In Windows Server, archiviazione collegata direttamente, o DAS (Direct-Attached Storage), significa che il sistema operativo Windows Server è proprietario del file system. Un sistema DAS si può realizzare collegando fisicamente i dischi al file server, collegando dischi virtuali a una macchina virtuale file server, ad esempio una macchina virtuale ospitata da Hyper-V, o anche tramite ISCSI.

Sono supportati solo i volumi NTFS. ReFS, FAT, FAT32 e gli altri file system non sono supportati.

La tabella seguente illustra lo stato di interoperabilità delle funzionalità del file system NTFS: 

| Funzionalità | Stato del supporto | Note |
|---------|----------------|-------|
| Elenchi di controllo di accesso (ACL) | supporto completo | Gli elenchi di controllo di accesso discrezionale di Windows vengono mantenuti da Sincronizzazione file di Azure e vengono applicati da Windows Server negli endpoint server. Gli ACL possono anche essere applicati quando si monta direttamente la condivisione file di Azure, ma questa operazione richiede una configurazione aggiuntiva. Per altre informazioni, vedere le sezione [Identità](#identity). |
| Collegamenti reali | Operazione ignorata | |
| Collegamenti simbolici | Operazione ignorata | |
| Punti di montaggio | Parzialmente supportato | I punti di montaggio possono corrispondere alla radice di un endpoint server, ma vengono ignorati se sono contenuti nello spazio dei nomi di un endpoint server. |
| Giunzioni | Operazione ignorata | Ad esempio, le cartelle DfrsrPrivate e DFSRoots del file system distribuito. |
| Punti di analisi | Operazione ignorata | |
| Compressione NTFS | supporto completo | |
| File sparse | supporto completo | I file sparse vengono sincronizzati (non bloccati), ma vengono sincronizzati nel cloud come file completi. Se il contenuto del file viene modificato nel cloud (o in un altro server), il file non è più di tipo sparse quando viene scaricata la modifica. |
| Flussi di dati alternativi (ADS) | Mantenuti, ma non sincronizzati | Ad esempio, i tag di classificazione creati tramite Infrastruttura di classificazione file non vengono sincronizzati. I tag di classificazione esistenti sui file in ognuno degli endpoint server non subiscono variazioni. |

<a id="files-skipped"></a>Sincronizzazione file di Azure ignorerà inoltre alcuni file temporanei e cartelle di sistema:

| File/cartella | Note |
|-|-|
| pagefile.sys | File specifico del sistema |
| Desktop.ini | File specifico del sistema |
| thumbs.db | File temporaneo per anteprime |
| ehthumbs.db | File temporaneo per anteprime multimediali |
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
La deduplicazione dei dati è supportata nei volumi con cloud a livelli abilitato in Windows Server 2016 e Windows Server 2019. Abilitando Deduplicazione dati in un volume in cui è abilitata la funzionalità cloud a livelli, è possibile memorizzare nella cache un numero maggiore di file in locale senza effettuare il provisioning di altro spazio di archiviazione. 

Quando si abilita Deduplicazione dati in un volume in cui è abilitato il cloud a livelli, i file ottimizzati dalla deduplicazione nel percorso dell'endpoint server verranno suddivisi in livelli come se si trattasse di un normale file, in base alle impostazioni dei criteri del cloud a livelli. Una volta suddivisi in livelli i file ottimizzati dalla deduplicazione, il processo di Garbage Collection di Deduplicazione dati viene eseguito automaticamente, per recuperare spazio su disco rimuovendo i blocchi non necessari che non sono più referenziati da altri file sul volume.

Si noti che il risparmio di spazio nel volume si applica solo al server. I dati nella condivisione file di Azure non verranno deduplicati.

> [!Note]  
> Per supportare Deduplicazione dati nei volumi con cloud a livelli abilitato in Windows Server 2019, è necessario installare l'aggiornamento di Windows [KB4520062](https://support.microsoft.com/help/4520062) ed è richiesto l'agente di Sincronizzazione file di Azure versione 9.0.0.0 o successiva.

**Windows Server 2012 R2**  
Sincronizzazione file di Azure non supporta la deduplicazione dei dati e il cloud a livelli nello stesso volume di Windows Server 2012 R2. Se in un volume è abilitata la deduplicazione dei dati, la funzionalità cloud a livelli deve essere disabilitata. 

**Note**
- Se la funzionalità Deduplicazione dati viene installata prima di installare l'agente di Sincronizzazione file di Azure, per supportare la deduplicazione e il cloud a livelli nello stesso volume è necessario il riavvio.
- Se si abilita la deduplicazione in un volume dopo aver abilitato il cloud a livelli, il processo iniziale di ottimizzazione di Deduplicazione dati ottimizzerà i file nel volume che non sono già suddivisi in livelli. L'effetto sul cloud a livelli sarà il seguente:
    - Il criterio di spazio disponibile continuerà a suddividere i file in livelli in base allo spazio libero nel volume usando la mappa termica.
    - Il criterio di data ignorerà la suddivisione in livelli di file che altrimenti sarebbero stati idonei, a causa del processo di ottimizzazione di Deduplicazione dati che accede ai file.
- Per i processi di ottimizzazione di Deduplicazione dati, la suddivisione nel cloud a livelli con i criteri di data verrà ritardata in base all'impostazione [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps), se il file non è già suddiviso in livelli. 
    - Esempio: Se l'impostazione MinimumFileAgeDays è sette giorni e il criterio di data del cloud a livelli è 30 giorni, il criterio di data suddividerà i file in livelli dopo 37 giorni.
    - Nota: quando un file è suddiviso in livelli da Sincronizzazione file di Azure, il processo di ottimizzazione di Deduplicazione dati ignorerà il file.
- Se un server che esegue Windows Server 2012 R2 con l'agente di Sincronizzazione file di Azure installato viene aggiornato a Windows Server 2016 o Windows Server 2019, è necessario eseguire i passaggi seguenti per supportare la deduplicazione dei dati e il cloud a livelli nello stesso volume:  
    - Disinstallare l'agente di Sincronizzazione file di Azure per Windows Server 2012 R2 e riavviare il server.
    - Scaricare l'agente di Sincronizzazione file di Azure per la nuova versione del sistema operativo server (Windows Server 2016 o Windows Server 2019).
    - Installare l'agente di Sincronizzazione file di Azure e riavviare il server.  
    
    Nota: le impostazioni di configurazione di Sincronizzazione file di Azure nel server vengono mantenute quando l'agente viene disinstallato e reinstallato.

### <a name="distributed-file-system-dfs"></a>File system distribuito (DFS)
Sincronizzazione file di Azure supporta l'interoperabilità con Spazi dei nomi DFS (DFS-N) e Replica DFS (DFS-R).

**Spazi dei nomi DFS (DFS-N)** : Sincronizzazione file di Azure è completamente supportato nei server DFS-N. È possibile installare l'agente Sincronizzazione file di Azure in uno o più membri DFS-N per sincronizzare i dati tra gli endpoint server e l'endpoint cloud. Per altre informazioni, vedere [Informazioni generali su Spazi dei nomi DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replica DFS (DFS-R)** : dato che DFS-R e Sincronizzazione file di Azure sono entrambi soluzioni di replica, nella maggior parte dei casi è consigliabile sostituire DFS-R con Sincronizzazione file di Azure. Esistono tuttavia diversi scenari in cui può essere opportuno usare insieme DFS-R e Sincronizzazione file di Azure:

- Si esegue la migrazione da una distribuzione di DFS-R a una distribuzione di Sincronizzazione file di Azure. Per altre informazioni, vedere [Eseguire la migrazione di una distribuzione di Replica DFS (DFS-R) in Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Non tutti i server locali in cui è necessaria una copia dei dati dei file possono essere connessi direttamente a Internet.
- I server di succursale consolidano i dati in un unico server di hub per cui si vorrebbe usare Sincronizzazione file di Azure.

Per il funzionamento in parallelo di Sincronizzazione file di Azure e DFS-R:

1. Nei volumi con cartelle replicate con DFS-R deve essere disabilitata la suddivisione in livelli cloud di Sincronizzazione file di Azure.
2. Non devono essere configurati endpoint server nelle cartelle di replica di sola lettura di DFS-R.

Per altre informazioni, vedere la [panoramica di Replica DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'installazione dell'agente e la registrazione del server devono avvenire dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="windows-search"></a>Ricerca di Windows
Se in un endpoint server è abilitata la suddivisione in livelli nel cloud, i file suddivisi in livelli vengono ignorati e non vengono indicizzati dalla ricerca di Windows. I file non suddivisi in livelli vengono indicizzati correttamente.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Altre soluzioni di gestione dell'archiviazione gerarchica
Con Sincronizzazione file di Azure non devono essere usate altre soluzioni di gestione dell'archiviazione gerarchica.

## <a name="identity"></a>Identità
Sincronizzazione file di Azure funziona con l'identità basata su AD standard senza bisogno di alcuna configurazione speciale oltre alla configurazione della sincronizzazione. Quando si usa Sincronizzazione file di Azure, in genere ci si aspetta che la maggior parte degli accessi passi attraverso i server di memorizzazione nella cache di Sincronizzazione file di Azure anziché tramite la condivisione file di Azure. Poiché gli endpoint server si trovano in Windows Server e Windows Server supporta da molto tempo gli ACL di Active Directory e di Windows, non occorre fare altro oltre a verificare che i file server Windows registrati con il servizio di sincronizzazione archiviazione siano aggiunti a un dominio. Sincronizzazione file di Azure archivierà gli ACL nei file nella condivisione file di Azure e li replicherà in tutti gli endpoint server.

Anche se le modifiche apportate direttamente alla condivisione file di Azure richiederanno più tempo per la sincronizzazione con gli endpoint server nel gruppo di sincronizzazione, può essere utile assicurarsi di poter applicare le autorizzazioni di Active Directory sulla condivisione file anche direttamente nel cloud. A questo scopo, è necessario aggiungere il proprio account di archiviazione ad AD locale, esattamente come sono aggiunti a un dominio i file server Windows. Per altre informazioni sull'aggiunta dell'account di archiviazione a un dominio di Active Directory di proprietà del cliente, vedere [Azure Files Active Directory overview](storage-files-active-directory-overview.md) (Panoramica di Active Directory per File di Azure).

> [!Important]  
> L'aggiunta dell'account di archiviazione a un dominio di Active Directory non è indispensabile per distribuire correttamente Sincronizzazione file di Azure. Si tratta di un passaggio facoltativo, che consente alla condivisione file di Azure di applicare ACL locali quando gli utenti montano direttamente la condivisione file di Azure.

## <a name="networking"></a>Rete
L'agente di Sincronizzazione file di Azure comunica con il servizio di sincronizzazione archiviazione e la condivisione file di Azure tramite il protocollo FileREST e il protocollo REST di Sincronizzazione file di Azure, che usano entrambi HTTPS sulla porta 443. SMB non viene mai usato per caricare o scaricare dati tra Windows Server e la condivisione file di Azure. Poiché la maggior parte delle organizzazioni consente il traffico HTTPS sulla porta 443, come requisito per visitare la maggior parte dei siti Web, in genere non è necessaria una configurazione di rete speciale per distribuire Sincronizzazione file di Azure.

In base ai criteri dell'organizzazione o a requisiti normativi specifici, potrebbe essere necessaria una comunicazione più restrittiva con Azure e, di conseguenza, Sincronizzazione file di Azure offre diversi meccanismi per configurare la rete. In base ai propri requisiti, è possibile:

- Effettuare il tunneling del traffico di sincronizzazione e di caricamento/download di file attraverso la rete VPN di Azure o ExpressRoute. 
- Usare le funzionalità di File di Azure e Rete di Azure, ad esempio endpoint di servizio ed endpoint privati.
- Configurare Sincronizzazione file di Azure per supportare il proxy in uso nell'ambiente.
- Limitare l'attività di rete da Sincronizzazione file di Azure.

Per ulteriori informazioni su Sincronizzazione file di Azure e sulla rete, vedere [considerazioni sulla rete sincronizzazione file di Azure](storage-sync-files-networking-overview.md).

## <a name="encryption"></a>Crittografia
Quando si usa Sincronizzazione file di Azure, esistono tre diversi livelli di crittografia da considerare: la crittografia dei dati inattivi di Windows Server, la crittografia dei dati in transito tra l'agente di Sincronizzazione file di Azure e Azure e la crittografia dei dati inattivi nella condivisione file di Azure. 

### <a name="windows-server-encryption-at-rest"></a>Crittografia dei dati inattivi di Windows Server 
Esistono due strategie per crittografare i dati in Windows Server che funzionano in generale con Sincronizzazione file di Azure: crittografia sottostante al file system, per crittografare il file system e tutti i dati scritti su di esso, e crittografia nel formato di file stesso. Questi metodi non si escludono a vicenda. Possono essere usati insieme, se necessario, poiché lo scopo della crittografia è diverso.

Per fornire la crittografia sotto il file system, Windows Server offre la posta in arrivo BitLocker. BitLocker è completamente trasparente per Sincronizzazione file di Azure. Il motivo principale per usare un meccanismo di crittografia come BitLocker è impedire l'esfiltrazione di dati dal data center locale a seguito della sottrazione fisica dei dischi e impedire il sideload di sistema operativo non autorizzato per eseguire letture/scritture non autorizzate sui dati. Per alte informazioni su BitLocker, vedere [Panoramica di BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview).

I prodotti di terze parti che funzionano in modo analogo a BitLocker, per il fatto di essere sottostanti al volume NTFS, dovrebbero funzionare in modo completamente trasparente con Sincronizzazione file di Azure. 

L'altro metodo principale per la crittografia dei dati consiste nel crittografare il flusso di dati del file quando l'applicazione salva il file. Alcune applicazioni possono eseguire questa operazione in modo nativo, ma di solito non è così. Azure Information Protection (AIP), Azure Rights Management Services (Azure RMS) e Active Directory RMS sono esempi di metodi per crittografare il flusso di dati del file. Il motivo principale per usare un meccanismo di crittografia come AIP/RMS è impedire l'esfiltrazione di dati dalla condivisione file da parte di utenti che li copiano in posizioni alternative, ad esempio in un'unità flash, o che li inviano tramite posta elettronica a un utente non autorizzato. Quando il flusso di dati di un file viene crittografato come parte del formato di file, il file continuerà a essere crittografato nella condivisione file di Azure. 

Sincronizzazione file di Azure non interagisce con NTFS EFS (NTFS Encrypted File System) o con soluzioni di crittografia di terze parti che si trovano sopra il file system, ma sotto il flusso di dati del file. 

### <a name="encryption-in-transit"></a>Crittografia in transito

> [!NOTE]
> Il 1° agosto 2020, il servizio Sincronizzazione file di Azure ritirerà il supporto per TLS 1.0 e 1.1. Tutte le versioni dell'agente di Sincronizzazione file di Azure supportate usano già TLS 1.2 per impostazione predefinita. Potrebbe verificarsi l'uso di una versione precedente di TLS se TLS 1.2 è stato disabilitato nel server o se si usa un proxy. Se si usa un proxy, è consigliabile controllare la configurazione del proxy. Le aree del servizio Sincronizzazione file di Azure aggiunte dopo il 1/5/2020 supporteranno solo TLS 1.2 e il supporto per TLS 1.0 e 1.1 verrà rimosso dalle aree esistenti il 1° agosto 2020.  Per altre informazioni, vedere la [guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync).

L'agente di Sincronizzazione file di Azure comunica con il servizio di sincronizzazione archiviazione e la condivisione file di Azure tramite il protocollo FileREST e il protocollo REST di Sincronizzazione file di Azure, che usano entrambi HTTPS sulla porta 443. Sincronizzazione file di Azure non invia richieste non crittografate tramite HTTP. 

Gli account di archiviazione di Azure contengono un'opzione per richiedere la crittografia in transito, abilitata per impostazione predefinita. Anche se l'opzione a livello di account di archiviazione è disabilitata, vale a dire che sono possibili connessioni non crittografate alle condivisioni file di Azure, Sincronizzazione file di Azure userà comunque soltanto canali crittografati per accedere alla condivisione file.

La crittografia in transito per l'account di archiviazione viene in genere disabilitata principalmente per supportare un'applicazione legacy che deve essere eseguita in un sistema operativo meno recente, ad esempio Windows Server 2008 R2 o una distribuzione Linux precedente, che comunica direttamente con una condivisione file di Azure. Se l'applicazione legacy comunica con la cache Windows Server della condivisione file, la modifica di questa impostazione non avrà alcun effetto. 

È fortemente consigliabile verificare che la crittografia dei dati in transito sia abilitata.

Per altre informazioni sulla crittografia in transito, vedere [Richiedere il trasferimento sicuro in Archiviazione di Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Crittografia dei dati inattivi della condivisione file di Azure
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Livelli di archiviazione
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Abilitare condivisioni file standard fino a 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilità a livello di area
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Disponibilità a livello di area per Sincronizzazione file di Azure
Sincronizzazione file di Azure è disponibile nelle aree seguenti:

| Cloud di Azure | Area geografica | Area di Azure | Codice area geografica |
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
| Pubblico | Francia | Francia meridionale* | `francesouth` |
| Pubblico | India | India centrale | `centralindia` |
| Pubblico | India | India meridionale | `southindia` |
| Pubblico | Giappone | Giappone orientale | `japaneast` |
| Pubblico | Giappone | Giappone occidentale | `japanwest` |
| Pubblico | Corea del Sud | Corea centrale | `koreacentral` |
| Pubblico | Corea del Sud | Corea meridionale | `koreasouth` |
| Pubblico | Sud Africa | Sudafrica settentrionale | `southafricanorth` |
| Pubblico | Sud Africa | Sudafrica occidentale* | `southafricawest` |
| Pubblico | Emirati Arabi Uniti | Emirati Arabi Uniti centrali* | `uaecentral` |
| Pubblico | Emirati Arabi Uniti | Emirati Arabi Uniti settentrionali | `uaenorth` |
| Pubblico | Regno Unito | Regno Unito meridionale | `uksouth` |
| Pubblico | Regno Unito | Regno Unito occidentale | `ukwest` |
| Pubblico | US | Stati Uniti centrali | `centralus` |
| Pubblico | US | Stati Uniti orientali | `eastus` |
| Pubblico | US | Stati Uniti orientali 2 | `eastus2` |
| Pubblico | US | Stati Uniti centro-settentrionali | `northcentralus` |
| Pubblico | US | Stati Uniti centro-meridionali | `southcentralus` |
| Pubblico | US | Stati Uniti centro-occidentali | `westcentralus` |
| Pubblico | US | Stati Uniti occidentali | `westus` |
| Pubblico | US | Stati Uniti occidentali 2 | `westus2` |
| US Gov | Stati Uniti | US Gov Arizona | `usgovarizona` |
| US Gov | Stati Uniti | US Gov Texas | `usgovtexas` |
| US Gov | Stati Uniti | US Gov Virginia | `usgovvirginia` |

Sincronizzazione file di Azure supporta solo la sincronizzazione con una condivisione file di Azure nella stessa area del servizio di sincronizzazione archiviazione.

Per le aree contrassegnate con asterischi, è necessario contattare il supporto di Azure per richiedere l'accesso ad Archiviazione di Azure in tali aree. Il processo è descritto in [questo documento](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="redundancy"></a>Ridondanza
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> L'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica della zona consentono di eseguire manualmente il failover dell'archiviazione nell'area secondaria. Si consiglia di non eseguire questa operazione al di fuori di una situazione di emergenza quando si usa Sincronizzazione file di Azure, per la maggiore probabilità che si verifichi una perdita di dati. In caso di emergenza, se si vuole avviare un failover manuale dell'archiviazione è necessario aprire un caso di supporto presso Microsoft per fare in modo che Sincronizzazione file di Azure riprenda la sincronizzazione con l'endpoint secondario.

## <a name="migration"></a>Migrazione
Se si ha un file server Windows esistente, è possibile installare Sincronizzazione file di Azure direttamente, senza bisogno di spostare i dati in un nuovo server. Se si sta pianificando la migrazione a un nuovo file server Windows nell'ambito dell'adozione di Sincronizzazione file di Azure, esistono diversi possibili approcci per spostare i dati:

- Creare endpoint server per la vecchia condivisione file e la nuova condivisione file e consentire a Sincronizzazione file di Azure di sincronizzare i dati tra gli endpoint server. Il vantaggio di questo approccio è che rende molto semplice eseguire l'oversubscription dell'archiviazione nel nuovo file server, dal momento che Sincronizzazione file di Azure supporta il cloud a livelli. Quando si è pronti, è possibile trasferire gli utenti finali alla condivisione file nel nuovo server e rimuovere l'endpoint server della condivisione file precedente.

- Creare un endpoint server solo sul nuovo file server e copiarvi i dati dalla condivisione file precedente usando `robocopy`. A seconda della topologia delle condivisioni file nel nuovo server (il numero di condivisioni presenti in ogni volume, la quantità di spazio disponibile in ogni volume e così via), potrebbe essere necessario effettuare temporaneamente il provisioning di spazio di archiviazione aggiuntivo, perché è previsto che l'operazione `robocopy` dal server precedente al nuovo server nel data center locale termini prima dello spostamento dei dati in Azure da parte di Sincronizzazione file di Azure.

È anche possibile usare Data Box per eseguire la migrazione dei dati in una distribuzione di Sincronizzazione file di Azure. Nella maggior parte dei casi, quando i clienti vogliono usare Data Box per inserire i dati, lo fanno perché ritengono che aumenterà la velocità della distribuzione o che sia utile per gli scenari di larghezza di banda limitata. Anche se è vero che l'uso di un Data Box per inserire dati nella distribuzione di Sincronizzazione file di Azure ridurrà l'utilizzo della larghezza di banda, per la maggior parte degli scenari sarà probabilmente più veloce caricare i dati online tramite uno dei metodi descritti in precedenza. Per altre informazioni su come usare Data Box per inserire dati nella distribuzione di Sincronizzazione file di Azure, vedere [Eseguire la migrazione di dati in blocco a Sincronizzazione file di Azure con Azure DataBox](storage-sync-offline-data-transfer.md).

Un errore comune dei clienti quando eseguono la migrazione dei dati nella nuova distribuzione di Sincronizzazione file di Azure è copiare i dati direttamente nella condivisione file di Azure, anziché nei file server Windows. Anche se Sincronizzazione file di Azure identificherà tutti i nuovi file nella condivisione file di Azure e li sincronizzerà con le condivisioni file di Windows, questa operazione è in genere molto più lenta rispetto al caricamento dei dati tramite il file server Windows. Quando si usano gli strumenti di copia di Azure, ad esempio AzCopy, è importante usare la versione più recente. Controllare la [tabella strumenti copia file](storage-files-migration-overview.md#file-copy-tools) per ottenere una panoramica degli strumenti di copia di Azure per assicurarsi che sia possibile copiare tutti i metadati importanti di un file, ad esempio timestamp e ACL.

## <a name="antivirus"></a>Antivirus
Poiché l'antivirus funziona analizzando i file per il codice dannoso noto, un prodotto antivirus potrebbe causare il richiamo di file a più livelli, ottenendo così un elevato costo in uscita. Nella versione 4.0 e successive dell'agente di Sincronizzazione file di Azure, per i file archiviati a livelli è impostato l'attributo sicuro di Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS. È consigliabile consultare il fornitore del software per ottenere informazioni su come configurare la soluzione in modo che non legga i file per cui è impostato questo attributo (molte lo fanno automaticamente). 

Le soluzioni antivirus Microsoft, Windows Defender e System Center Endpoint Protection (SCEP), escludono automaticamente dalla lettura i file con questo attributo. Un test su entrambe le soluzioni ha identificato un problema secondario: quando si aggiunge un server a un gruppo di sincronizzazione esistente, i file di dimensioni inferiori a 800 byte vengono richiamati (scaricati) nel nuovo server. Questi file rimangono nel nuovo server e non vengono suddivisi in livelli, poiché non soddisfano il requisito relativo alle dimensioni della suddivisione in livelli (> 64 KB).

> [!Note]  
> I fornitori di software antivirus possono controllare la compatibilità tra il prodotto e Sincronizzazione file di Azure usando [Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322), disponibile nell'Area download Microsoft.

## <a name="backup"></a>Backup 
Se è abilitata la suddivisione in livelli nel cloud, le soluzioni che eseguono direttamente il backup dell'endpoint server o di una macchina virtuale in cui si trova l'endpoint server non devono essere usate. La suddivisione in livelli nel cloud fa sì che solo un subset dei dati sia archiviato nell'endpoint server, con il set di dati completo che risiede nella condivisione file di Azure. A seconda della soluzione di backup utilizzata, i file a livelli verranno ignorati e non vengono sottoposti a backup (poiché hanno il FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS set di attributi) o verranno richiamati su disco, ottenendo un elevato costo in uscita. È consigliabile usare una soluzione di backup cloud per eseguire il backup diretto della condivisione file di Azure. Per altre informazioni, vedere [informazioni sul backup di condivisioni file di Azure](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json) o contattare il provider di backup per verificare se supporta il backup di condivisioni file di Azure.

Se si preferisce usare una soluzione di backup locale, è necessario eseguire i backup in un server del gruppo di sincronizzazione con la suddivisione in livelli nel cloud disabilitata. Quando si esegue un ripristino, usare le opzioni di ripristino a livello di volume o a livello di file. I file ripristinati tramite l'opzione di ripristino a livello di file vengono sincronizzati con tutti gli endpoint del gruppo di sincronizzazione e i file esistenti vengono sostituiti dalla versione ripristinata dal backup.  Nel ripristino a livello di volume i file non vengono sostituiti dalla versione più recente nella condivisione file di Azure o in altri endpoint server.

> [!Note]  
> Il ripristino bare metal può avere risultati imprevisti e non è attualmente supportato.

> [!Note]  
> Con la versione 9 dell'agente di Sincronizzazione file di Azure, gli snapshot VSS (inclusa la scheda Versioni precedenti) sono ora supportati nei volumi che hanno il cloud a livelli abilitato. Tuttavia, è necessario abilitare la compatibilità con le versioni precedenti tramite PowerShell. [Informazioni](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md)
* [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
* [Come distribuire i file di Azure](storage-files-deployment-guide.md)
* [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
* [Monitorare Sincronizzazione file di Azure](storage-sync-files-monitoring.md)
