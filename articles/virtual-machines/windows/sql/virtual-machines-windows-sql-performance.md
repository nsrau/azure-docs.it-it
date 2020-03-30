---
title: Linee guida relative alle prestazioni per SQL Server in Azure | Microsoft Docs
description: Questo argomento descrive le linee guida per ottimizzare le prestazioni di SQL Server in Macchine virtuali di Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 880f1c601cf4132fdec9e5d25b1bf1f2ff175ab7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650538"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Linee guida sulle prestazioni per SQL Server in Macchine virtuali di Azure

## <a name="overview"></a>Panoramica

Questo articolo include linee guida per ottimizzare le prestazioni di SQL Server in Macchine virtuali di Microsoft Azure. Durante l'esecuzione di SQL Server in Macchine virtuali di Azure, è consigliabile continuare a usare le stesse opzioni di ottimizzazione delle prestazioni dei database applicabili a SQL Server nell'ambiente server locale. Tuttavia, le prestazioni di un database relazionale in un cloud pubblico dipendono da molti fattori, ad esempio dalle dimensioni della macchina virtuale e dalla configurazione dei dischi dati.

Le [immagini di SQL Server con provisioning nel portale di Azure](quickstart-sql-vm-create-portal.md) seguono le procedure consigliate generali di configurazione dell'archiviazione (per ulteriori informazioni sulla configurazione dell'archiviazione, vedere [Configurazione dell'archiviazione per le VM di SQL Server](virtual-machines-windows-sql-server-storage-configuration.md)). Dopo il provisioning, è possibile applicare le altre ottimizzazioni descritte in questo articolo. Basare le scelte sul carico di lavoro e verificarle tramite test.

> [!TIP]
> Vi è in genere un compromesso tra l'ottimizzazione per i costi e l'ottimizzazione per le prestazioni. Questo articolo riporta informazioni su come ottenere le *migliori* prestazioni per SQL Server nelle VM Azure. Se il carico di lavoro è contenuto, potrebbero non essere necessarie tutte le ottimizzazione elencate di seguito. Prendere in considerazione le esigenze di prestazione, i costi e i modelli di carico di lavoro durante la valutazione di questi elementi consigliati.

## <a name="quick-check-list"></a>Elenco di controllo rapido

Di seguito è riportato un elenco controllo rapido per ottimizzare le prestazioni di SQL Server in Macchine virtuali di Azure:

| Area | Ottimizzazioni |
| --- | --- |
| [Dimensioni della macchina virtuale](#vm-size-guidance) | - Usare le dimensioni delle macchine virtuali con 4 o più vCPU come [E4S_v3](../../ev3-esv3-series.md) o superiore o [DS12_v2](../../dv2-dsv2-series-memory.md) o superiore.<br/><br/> - [Es, Eas, Ds e Das Series](../../sizes-general.md) offrono il rapporto ottimale tra memoria e vCPU necessario per le prestazioni del carico di lavoro OLTP. <br/><br/> - [La serie M](../../m-series.md) offre il rapporto tra memoria e vCPU più elevato richiesto per le prestazioni mission-critical ed è ideale per i carichi di lavoro del data warehouse. <br/><br/> - Raccogliere i requisiti di [IOPS,](../premium-storage-performance.md#iops) [velocità effettiva](../premium-storage-performance.md#throughput) e [latenza](../premium-storage-performance.md#latency) del carico di lavoro di destinazione nelle ore di punta seguendo l'elenco di controllo dei [requisiti di prestazioni dell'applicazione](../premium-storage-performance.md#application-performance-requirements-checklist) e quindi selezionare le [dimensioni della macchina virtuale](../sizes-general.md) che possono essere ridimensionate in base ai requisiti di prestazioni del carico di lavoro.|
| [Archiviazione](#storage-guidance) | - Per test dettagliati delle prestazioni di SQL Server nelle macchine virtuali di Azure con TPC-E e benchmark di TPC_C, vedere il blog [Optimize OLTP performance](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> - Utilizzare [SSD premium](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) per i migliori vantaggi di prezzo / prestazioni. Configurare la [cache ReadOnly](../premium-storage-performance.md#disk-caching) per i file di dati e nessuna cache per il file di registro. <br/><br/> - Utilizzare [ultradischi](../disks-types.md#ultra-disk) se il carico di lavoro richiede meno di 1 ms di latenze di archiviazione.- Use Ultra Disks if less than 1 ms storage tencies are required by the workload. <br/><br/> - Raccogliere i requisiti di latenza di archiviazione per i file di dati, log e database temporaneo di SQL Server [monitorando l'applicazione](../premium-storage-performance.md#application-performance-requirements-checklist) prima di scegliere il tipo di disco.- Collect the storage latency requirements for SQL Server data, log, and Temp DB files by monitoring the application before choosing the disk type. Se sono necessarie latenze di archiviazione <1 ms, utilizzare dischi Ultra, altrimenti utilizzare SSD premium. Se le latenze basse sono necessarie solo per il file di log e non per i file di dati, [eseguire il provisioning del disco Ultra al](../disks-enable-ultra-ssd.md) numero di i/o al secondo e alla velocità effettiva necessari solo per il file di log. <br/><br/> -  [Le condivisioni file Premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) sono consigliate come archiviazione condivisa per un'istanza del cluster di failover di SQL Server.Premium file shares are recommended as shared storage for a SQL Server failover cluster Instance. Le condivisioni file Premium non supportano la memorizzazione nella cache e offrono prestazioni limitate rispetto ai dischi SSD Premium. Scegliere i dischi gestiti da SSD premium rispetto alle condivisioni file premium per le istanze SQL autonome. ma sfrutta le condivisioni file premium per l'archiviazione condivisa dell'istanza del cluster di failover per semplificare la manutenzione e la scalabilità flessibile. <br/><br/> - L'archiviazione standard è consigliata solo a scopo di sviluppo e test o per i file di backup e non deve essere usata per i carichi di lavoro di produzione.- Standard storage is recommended only for development and test purposes or for backup files and should not be used for production workloads. <br/><br/> - Mantenere l'[account di archiviazione](../../../storage/common/storage-create-storage-account.md) e la macchina virtuale di SQL Server nella stessa area.<br/><br/> - Disabilitare [l'archiviazione con ridondanza geografica](../../../storage/common/storage-redundancy.md) di Azure (replica geografica) nell'account di archiviazione.- Disable Azure geo-redundant storage (geo-replica) on the storage account.  |
| [Dischi](#disks-guidance) | - Utilizzare un minimo di 2 [dischi SSD premium](../disks-types.md#premium-ssd) (1 per il file di registro e 1 per i file di dati). <br/><br/> - Per i carichi di lavoro che richiedono <latenze di I/O da 1 ms, abilitare l'acceleratore di scrittura per la serie M e prendere in considerazione l'utilizzo di dischi Ultra SSD per le serie Es e DS.- For workloads requiring <1 ms IO latencies, enable write accelerator for M series and consider using Ultra SSD disks for Es and DS series. <br/><br/> - Abilitare la [memorizzazione nella cache di sola lettura](../premium-storage-performance.md#disk-caching) sui dischi che ospitano i file di dati.- Enable read only caching on the disk(s) hosting the data files.<br/><br/> - Aggiungere una capacità di IOPS/velocità effettiva premium aggiuntiva del 20% rispetto al carico di lavoro necessario per la configurazione dell'archiviazione per i file di dati, log e TempDB di SQL Server- Add additional 20% premium IOPS/throughput capacity than your workload requires when [configuring storage for SQL Server data, log, and TempDB files](virtual-machines-windows-sql-server-storage-configuration.md) <br/><br/> - Evitare l'uso del sistema operativo o di dischi temporanei per la registrazione o l'archiviazione di database.<br/><br/> - Non abilitare la memorizzazione nella cache sui dischi che ospitano il file di log.  **Importante:** arrestare il servizio SQL Server quando si modificano le impostazioni della cache per un disco della macchina virtuale di Azure.Important : Stop the SQL Server service when changing the cache settings for an Azure VM disk.<br/><br/> - Eseguire lo striping di più dischi dati di Azure per aumentare la velocità effettiva di archiviazione.<br/><br/> - Formattare con dimensioni di allocazione documentate. <br/><br/> - Inserire TempDB nell'unità `D:\` SSD locale per carichi di lavoro di SQL Server mission-critical (dopo aver scelto le dimensioni corrette della macchina virtuale). Se si crea la macchina virtuale dal portale di Azure o dai modelli di avvio rapido di Azure e si posiziona noto il [database temporaneo nel disco locale,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) non sono necessarie ulteriori azioni. per tutti gli altri casi seguire i passaggi nel blog per l'utilizzo di [SSD per archiviare TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) per evitare errori dopo il riavvio. Se la capacità dell'unità locale non è sufficiente per le dimensioni del database temporaneo, posizionare il database temporaneo in un pool di archiviazione [rimosso](../premium-storage-performance.md) su dischi SSD premium con [memorizzazione nella cache](../premium-storage-performance.md#disk-caching)di sola lettura . |
| [I/O](#io-guidance) |- Abilitare la compressione di pagina del database.<br/><br/> - Abilitare l'inizializzazione immediata per i file di dati.<br/><br/> - Limitare l'aumento automatico delle dimensioni per il database.<br/><br/> - Disabilitare la compattazione automatica del database.<br/><br/> - Spostare tutti i database su dischi dati, inclusi i database di sistema.<br/><br/> - Spostare le directory dei file di traccia e dei log degli errori di SQL Server sui dischi dati.<br/><br/> - Configurare i percorsi predefiniti dei file di backup e di database.<br/><br/> - [Abilita pagine bloccate in memoria](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> - Applicare le correzioni delle prestazioni di SQL Server. |
| [Elementi specifici delle funzionalità](#feature-specific-guidance) | - Eseguire il backup direttamente nell'archivio BLOB.<br/><br/>- Utilizzare [i backup snapshot](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) di file per i database di dimensioni superiori a 12 TB. <br/><br/>- Utilizzare più file Temp DB, 1 file per core, fino a 8 file.<br/><br/>- Impostare la memoria massima del server al 90% o fino a 50 GB rimanenti per il sistema operativo. <br/><br/>- Abilitare numA morbido. |

Per ulteriori informazioni su *come* e *perché* effettuare queste ottimizzazioni, esaminare i dettagli e le indicazioni fornite nelle sezioni seguenti.

## <a name="vm-size-guidance"></a>Linee guida per le dimensioni delle VM

Iniziare raccogliendo i requisiti di velocità effettiva di cpu, memoria e archiviazione del carico di lavoro nelle ore di punta. È possibile utilizzare i contatori delle prestazioni :LogicalDisk/Letture disco/sec e - Disco logico/Sec per raccogliere i requisiti di IOPS di lettura e scrittura e il contatore 'LogicalDisk-Byte/Sec/sec può essere utilizzato per raccogliere i requisiti di [velocità effettiva](../premium-storage-performance.md#disk-caching) di archiviazione per i file di dati, log e database temporaneo. Dopo aver definito i requisiti di IOPS e velocità effettiva al picco, valutare le dimensioni delle macchine virtuali che offrono tale capacità. Ad esempio, se il carico di lavoro richiede 20 K iOPS di lettura e 10K iOPS di scrittura al picco, è possibile scegliere E16s_v3 (con 32 K memorizzati nella cache e 25600 IOPS non memorizzati nella cache) o M16_s (con un massimo di 20 K memorizzati nella cache e 10K uncached IOPS) con 2 dischi P30. Assicurarsi di comprendere i requisiti di velocità effettiva e IOPS del carico di lavoro poiché le macchine virtuali hanno limiti di scalabilità diversi per le operazioni di I/O al secondo e la velocità effettiva.<br/><br/>[DSv_3](../../dv3-dsv3-series.md) e [Es_v3 serie](../../ev3-esv3-series.md) sono ospitati su hardware di uso generale con processori Intel Haswell o Broadwell. [La serie M](../../m-series.md) offre il numero di vCPU e la memoria più elevati per i carichi di lavoro di SQL Server più grandi e ospitata su hardware ottimizzato per la memoria con la famiglia di processori Skylake. Queste serie di macchine virtuali supportano l'archiviazione Premium, consigliata per ottenere prestazioni ottimali con la cache di lettura a livello di host. Sia Es_v3 che serie M sono disponibili anche in [dimensioni core vincolate,](../../windows/constrained-vcpu.md)il che consente di risparmiare denaro per i carichi di lavoro con minori richieste di elaborazione e capacità di storage elevate. 

## <a name="storage-guidance"></a>Linee guida per l'archiviazione

Per test dettagliati delle prestazioni di SQL Server nelle macchine virtuali di Azure con TPC-E e benchmark di TPC_C, fare riferimento al blog [Ottimizzare le prestazioni OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

La cache BLOB di Azure con sSD premium è consigliata per tutti i carichi di lavoro di produzione. 

> [!WARNING]
> Le unità HDD e SSD Standard sono caratterizzate da latenze e larghezze di banda variabili e sono consigliate solo per i carichi di lavoro di sviluppo e test. Per i carichi di lavoro di produzione si consiglia di usare le unità SSD Premium.

È inoltre consigliabile creare account di archiviazione di Azure nello stesso data center delle macchine virtuali di SQL Server per ridurre i tempi di trasferimento. Quando si crea un account di archiviazione, disabilitare la replica geografica in quanto l'ordine di scrittura coerente su più dischi non è garantito. In alternativa, si consiglia di configurare una tecnologia di ripristino di emergenza di SQL Server tra due data center di Azure. Per altre informazioni, vedere [Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Linee guida per i dischi

Esistono tre tipi di disco principali in una VM Azure:

* **Disco del sistema operativo**: quando si crea una macchina virtuale Azure, la piattaforma associa almeno un disco, contrassegnato come unità **C**, alla VM per il disco del sistema operativo. Si tratta di un disco rigido virtuale memorizzato come BLOB di pagine nell'archivio.
* **Disco temporaneo**: le macchine virtuali di Azure contengono un altro disco, chiamato disco temporaneo, contrassegnato come unità **D**. Questo è un disco sul nodo che può essere usato come area scratch.
* **Dischi dati**: è possibile associare dischi aggiuntivi alla macchina virtuale nella forma di dischi dati, che vengono memorizzati nell'archivio come BLOB di pagine.

Nelle sezioni seguenti sono riportati alcuni consigli per l'uso di questi diversi dischi.

### <a name="operating-system-disk"></a>Disco del sistema operativo

Un disco del sistema operativo è un disco rigido virtuale (VHD) che è possibile avviare e montare come versione in esecuzione di un sistema operativo, etichettato come unità **C**.

Il criterio di memorizzazione nella cache predefinito per il disco del sistema operativo è **Read/Write**. Per le applicazioni sensibili alle prestazioni, è consigliabile usare dischi dati anziché il disco del sistema operativo. Vedere la sezione relativa ai dischi di dati riportata di seguito.

### <a name="temporary-disk"></a>Disco temporaneo

L'unità di archiviazione temporanea, etichettata come unità **D,** non è persistente nell'archiviazione BLOB di Azure.The temporary storage drive, labeled as the D drive, is not persisted to Azure blob storage. Non archiviare i file del database utente o i file di log delle transazioni utente nell'unità **D**.

Posizionare TempDB nell'unità `D:\` SSD locale per carichi di lavoro di SQL Server mission-critical (dopo aver scelto le dimensioni corrette della macchina virtuale). Se si crea la macchina virtuale dal portale di Azure o dai modelli di guida rapida di Azure e si posiziona il database [temporaneo nel disco locale,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)non sono necessarie ulteriori azioni. per tutti gli altri casi seguire i passaggi nel blog per l'utilizzo di [SSD per archiviare TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) per evitare errori dopo il riavvio. Se la capacità dell'unità locale non è sufficiente per le dimensioni del database temporaneo, posizionare il database temporaneo in un pool di archiviazione [rimosso](../premium-storage-performance.md) su dischi SSD premium con [memorizzazione nella cache](../premium-storage-performance.md#disk-caching)di sola lettura .

Per le macchine virtuali che supportano SSD premium, è anche possibile archiviare TempDB in un disco che supporta sSD premium con la memorizzazione nella cache di lettura abilitata.


### <a name="data-disks"></a>Dischi dati

* **Utilizzare dischi SSD premium per**i file di dati e di log : se non si utilizza lo striping dei dischi, utilizzare due dischi SSD premium in cui un disco contiene il file di registro e l'altro contiene i dati. Ogni SSD premium fornisce un numero di operazioni di I/O al secondo e larghezza di banda (MB/s) a seconda delle dimensioni, come illustrato nell'articolo [Selezionare un tipo](../disks-types.md)di disco . Se si utilizza una tecnica di striping del disco, come Spazi di archiviazione, si ottengono prestazioni ottimali con due pool, uno per il/i file di log e l'altro per i file di dati. Tuttavia, se si prevede di utilizzare le istanze del cluster di failover di SQL ServerSQL Server , è necessario configurare un pool o utilizzare invece [condivisioni file](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) Premium.However, if you plan to use SQL Server failover cluster instances (FCI), you must configure one pool, or utilize premium file shares instead.

   > [!TIP]
   > - Per risultati del test in diverse configurazioni del disco e del carico di lavoro, vedere il post di blog seguente: [Linee guida di configurazione dell'archiviazione per SQL Server in macchine virtuali di Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - Per prestazioni di importanza strategica per istanze di SQL Server che necessitano di circa 50.000 operazioni di I/O al secondo, provare a sostituire 10 dischi -P30 con un'unità Ultra SSD. Per ulteriori informazioni, vedere il seguente post di blog: [Prestazioni mission-critical con Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Quando si esegue il provisioning di una VM SQL Server nel portale, è possibile modificare la configurazione di archiviazione. A seconda della configurazione, Azure configura uno o più dischi. Più dischi sono combinati in un pool di archiviazione singolo con striping. Entrambi i file di dati e di log risiedono insieme in questa configurazione. Per altre informazioni, vedere [Configurazione dell'archiviazione per le VM di SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Striping del disco**: per una maggiore velocità effettiva, è possibile aggiungere ulteriori dischi dati e usare lo striping del disco. Per determinare il numero di dischi dati, è necessario analizzare il numero di IOPS e larghezza di banda necessari per i file di log e i file di dati e TempDB. Si noti che a seconda delle dimensioni delle VM i limiti nel numero di IOPs e larghezza di banda supportati cambiano. Vedere le tabelle relative agli IOPS per [dimensione di VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Usare le linee guida seguenti:

  * Per Windows 8 e Windows Server 2012 o versioni successive, usare [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739.aspx) applicando le indicazioni seguenti:

      1. Impostare l'interfoliazione (dimensione stripe) su 64 KB (65.536 byte) per i carichi di lavoro OLTP e 256 KB (262.144 byte) per i carichi di lavoro di data warehousing per evitare l'impatto sulle prestazioni a causa di un disallineamento delle partizioni. Questo valore deve essere impostato con PowerShell.
      2. Impostare il numero di colonne sul numero di dischi fisici. Usare PowerShell (e non l'interfaccia utente di Server Manager) per configurare più di 8 dischi. 

    Ad esempio, lo script di PowerShell seguente crea un nuovo pool di archiviazione con la dimensione di interleave impostata su 64 KB e il numero di colonne impostato su 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Per Windows 2008 R2 o versioni precedenti, è possibile usare i dischi dinamici (volumi con striping del sistema operativo) e la dimensione di striping è sempre di 64 KB. Questa opzione è deprecata a partire da Windows 8/Windows Server 2012. Per informazioni, vedere l'informativa di supporto relativa al [passaggio dal servizio dischi virtuali all'API di gestione archiviazione di Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Se si usa [Spazi di archiviazione diretta (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) con [Istanze del cluster di failover di SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md), è necessario configurare un singolo pool. Sebbene sia possibile creare volumi diversi in tale singolo pool, condivideranno tutti le stesse caratteristiche, ad esempio gli stessi criteri di memorizzazione nella cache.

  * Determinare il numero di dischi associati al pool di archiviazione dell'utente in base alle aspettative di carico. Tenere presente che le diverse dimensioni di macchine virtuali consentono diversi numeri di dischi dati associati. Per ulteriori informazioni, vedere [Dimensioni per le macchine virtuali](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Se non si usano unità SSD Premium (scenari di sviluppo e test), è consigliabile aggiungere il numero massimo di dischi dati supportato dalle [dimensioni della macchina virtuale](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e usare lo striping del disco.

* **Memorizzazione nella cache dei criteri**: tenere presente le raccomandazioni seguenti per la memorizzazione nella cache dei criteri a seconda della configurazione di archiviazione.

  * Se si usano dischi separati per i dati e file di log, abilitare la memorizzazione nella cache di lettura sui dischi dati che ospitano i propri file di dati e i file di dati di TempDB. Ciò può comportare un miglioramento significativo delle prestazioni. Non abilitare la memorizzazione nella cache sul disco che contiene il file di log, perché questo causa una riduzione delle prestazioni.

  * Se si usa lo striping del disco in un singolo pool di archiviazione, la maggior parte dei carichi di lavoro trarrà vantaggio dalla memorizzazione nella cache. Se si dispone di pool di archiviazione separato per i file di log e di dati, abilitare la cache di lettura solo nel pool di archiviazione per i file di dati. Per alcuni carichi di lavoro di scrittura pesanti, è possibile ottenere prestazioni migliori senza la memorizzazione nella cache. Ciò può essere determinato solo tramite test.

  * Le raccomandazioni precedenti si applicano alle unità SSD Premium. Se non si usano unità SSD Premium, non abilitare la memorizzazione nella cache per i dischi dati.

  * Per istruzioni sulla configurazione della memorizzazione nella cache su disco, vedere gli articoli seguenti. Per il modello di distribuzione classico, ovvero ASM, vedere: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Per il modello di distribuzione di Azure Resource Manager, vedere: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) e [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Arrestare il servizio SQL Server quando si modifica l'impostazione della cache dei dischi della VM di Azure per evitare danneggiamenti al database.

* **Dimensioni unità di allocazione NTFS**: quando si formatta il disco dati, è consigliabile usare una dimensione di unità di allocazione di 64 KB per file di log e dati, nonché per TempDB. Se TempDB viene posizionato sul disco temporaneo (D: unità) le prestazioni ottenute sfruttando questa unità superano la necessità di una dimensione dell'unità di allocazione di 64K. 

* **Procedure consigliate per la gestione del disco**: quando si rimuove un disco dati o si modifica il relativo tipo di cache, arrestare il servizio SQL Server durante la modifica. Quando vengono modificate le impostazioni del caching sul disco del sistema operativo, Azure arresta la VM, cambia il tipo di cache e riavvia la VM. Quando vengono modificate le impostazioni della cache di un disco dati, la VM non viene arrestata, ma il disco dati viene scollegato dalla VM durante la modifica e quindi ricollegato.

  > [!WARNING]
  > Il mancato arresto del servizio SQL Server durante queste operazioni può danneggiare il database.


## <a name="io-guidance"></a>Linee guida per l'I/O

* Quando l'applicazione e le richieste vengono eseguite in parallelo, si ottengono i migliori risultati con le unità SSD Premium. Le unità SSD Premium sono progettate per scenari in cui la profondità della coda I/O è maggiore di 1, pertanto si noteranno eventualmente impercettibili miglioramenti delle prestazioni per le richieste seriali a thread singolo (anche se usano in modo intensivo l'archiviazione). Ad esempio, ciò potrebbe influire sui risultati di test a thread singolo degli strumenti di analisi delle prestazioni, ad esempio SQLIO.

* È consigliabile usare la [compressione di pagina del database](https://msdn.microsoft.com/library/cc280449.aspx) in quanto consente di migliorare le prestazioni dei carichi di lavoro con utilizzo intensivo di I/O. Tuttavia, la compressione dei dati potrebbe incrementare l'utilizzo della CPU nel server di database.

* È possibile abilitare l'inizializzazione immediata dei file per ridurre il tempo necessario per l'allocazione iniziale dei file. Per sfruttare al meglio l'inizializzazione immediata dei file, concedere all'account di servizio SQL Server (MSSQLSERVER) SE_MANAGE_VOLUME_NAME e aggiungerlo ai criteri di sicurezza **Esecuzione operazioni di manutenzione volume**. Se si usa un'immagine della piattaforma SQL Server per Azure, l'account di servizio predefinito (NT Service\MSSQLSERVER) non viene aggiunto ai criteri di sicurezza **Esecuzione operazioni di manutenzione volume**. In altre parole, l'inizializzazione file immediata non è abilitata in un'immagine della piattaforma Server SQL di Azure. Dopo avere aggiunto l'account di servizio SQL Server ai criteri di sicurezza **Esecuzione operazioni di manutenzione volume**, riavviare il servizio SQL Server. Potrebbero essere presenti indicazioni sulla sicurezza da considerare per l'utilizzo di questa funzionalità. Per altre informazioni, vedere [Inizializzazione di file di database](https://msdn.microsoft.com/library/ms175935.aspx).

* **aumento automatico delle dimensioni** è considerato una semplice contingenza di crescita imprevista. La crescita dei dati e dei log non viene gestita quotidianamente con l'aumento automatico delle dimensioni. Se si usa questa funzionalità, impostare la precrescita del file mediante l'opzione Dimensioni.

* Verificare che la **compattazione automatica** sia disabilitata per evitare inutili sovraccarichi che possono influire negativamente sulle prestazioni.

* Spostare tutti i database su dischi dati, inclusi i database di sistema. Per altre informazioni vedere l'articolo [Spostare i database di sistema](https://msdn.microsoft.com/library/ms345408.aspx).

* Spostare le directory dei file di traccia e dei log degli errori di SQL Server sui dischi dati. È possibile farlo in Gestione configurazione SQL Server facendo clic con il pulsante destro del mouse sull'istanza di SQL Server e selezionando Proprietà. Le impostazioni del log degli errori e del file di traccia possono essere modificate nella scheda **Parametri di avvio.** La directory di dump è specificata nella scheda **Avanzate.** Nella schermata seguente viene illustrato dove cercare il parametro di avvio del log degli errori.

    ![Schermata del log degli errori di SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Configurare il percorso predefinito del file di backup e del file di database. Seguire i consigli elencati in questo articolo ed eseguire le modifiche nella finestra Proprietà server. Per le istruzioni, vedere l'articolo dedicato alla [Visualizzazione o modifica dei percorsi predefiniti per i file di dati e di log (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Le schermata seguente illustra come apportare tali modifiche.

    ![File di log e di backup di SQL](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Attivare le pagine bloccate per ridurre le operazioni di I/O e le attività di paging. Per altre informazioni, vedere l'articolo [Abilitazione dell'opzione Blocco di pagine in memoria (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Se si esegue SQL Server 2012, installare l'aggiornamento cumulativo 10 del Service Pack 1. Questo aggiornamento contiene la correzione per le prestazioni ridotte delle operazioni I/O quando si esegue select nell'istruzione di una tabella temporanea in SQL Server 2012. Per altre informazioni, vedere questo [articolo della Knowledge Base](https://support.microsoft.com/kb/2958012).

* È consigliabile comprimere i file di dati durante il trasferimento in entrata e in uscita di Azure.

## <a name="feature-specific-guidance"></a>Guida specifica delle funzionalità

Alcune distribuzioni possono ottenere ulteriori miglioramenti delle prestazioni usando tecniche di configurazione più avanzate. Nell'elenco seguente vengono evidenziate alcune funzionalità di SQL Server che consentono di ottenere prestazioni migliori:

### <a name="back-up-to-azure-storage"></a>Eseguire il backup in Archiviazione di AzureBack up to Azure Storage
quando si eseguono backup per SQL Server in esecuzione in macchine virtuali di Azure, è possibile usare [Backup di SQL Server nell'URL](https://msdn.microsoft.com/library/dn435916.aspx). Questa funzionalità è disponibile a partire da SQL Server 2012 SP1 CU2 ed è consigliata per il backup su dischi dati associati. Quando si esegue il backup o il ripristino da e verso l'archiviazione di Azure, seguire le indicazioni offerte in [Procedure consigliate e risoluzione dei problemi per il backup di SQL Server nell'URL e Ripristino da backup archiviati nell'archiviazione di Azure](https://msdn.microsoft.com/library/jj919149.aspx). È anche possibile automatizzare i backup usando [Backup automatizzato per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-automated-backup.md).

Prima di SQL Server 2012, usare lo [strumento di backup di SQL Server in Azure](https://www.microsoft.com/download/details.aspx?id=40740). Questo strumento consente di aumentare la velocità effettiva di backup usando più destinazioni di backup in striping.

### <a name="sql-server-data-files-in-azure"></a>SQL Server Data files in Azure

questa nuova funzionalità ([File di dati di SQL Server in Azure](https://msdn.microsoft.com/library/dn385720.aspx)) è disponibile a partire da SQL Server 2014. L'esecuzione di SQL Server con file di dati in Azure dimostra le caratteristiche di prestazioni paragonabili a quelle dei dischi dati di Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Istanza del cluster di failover e spazi di archiviazione

Se si utilizza Spazi di archiviazione, quando si aggiungono nodi al cluster nella pagina **Conferma** deselezionare la casella di controllo **Aggiungi tutte le risorse di archiviazione idonee al cluster**. 

![Deselezionare lo spazio di archiviazione idoneo](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Se si usa Spazi di archiviazione e non si deseleziona **Aggiungi tutte le risorse di archiviazione idonee al cluster**, Windows rende non visibili i dischi virtuali durante il processo di clustering. Di conseguenza, tali dischi non vengono visualizzati in Gestione disco o in Esplora risorse fino a quando gli spazi di archiviazione non vengono rimossi dal cluster e ricollegati usando PowerShell. Spazi di archiviazione consente di raggruppare più dischi in pool di archiviazione. Per ulteriori informazioni, vedere [Spazi di archiviazione](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su archiviazione e prestazioni, vedere [Linee guida di configurazione dell'archiviazione per SQL Server in macchine virtuali di Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Per le procedure consigliate relative alla sicurezza, vedere [Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-security.md).

Esaminare altri articoli relativi alle macchine virtuali di SQL Server in [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md). In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](virtual-machines-windows-sql-server-iaas-faq.md).
