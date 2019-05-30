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
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c1f40c62fce61ba16dfdf289d54cd19c3739ce21
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393747"
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
| [Dimensioni macchina virtuale](#vm-size-guidance) | - [DS3_v2](../sizes-general.md) o successiva per SQL Enterprise Edition.<br/><br/> - [DS2_v2](../sizes-general.md) o successiva per SQL Standard Edition e Web Edition. |
| [Archiviazione](#storage-guidance) | - Usare [unità SSD Premium](../disks-types.md). Archiviazione Standard è consigliata solo per i carichi di lavoro di sviluppo/test.<br/><br/> - Mantenere l'[account di archiviazione](../../../storage/common/storage-create-storage-account.md) e la macchina virtuale di SQL Server nella stessa area.<br/><br/> * Disabilitare l'[archiviazione con ridondanza geografica](../../../storage/common/storage-redundancy.md) (replica geografica) di Azure nell'account di archiviazione. |
| [Dischi](#disks-guidance) | - Usare almeno 2 [dischi P30](../disks-types.md#premium-ssd) (1 per i file di log, 1 per i file di dati inclusi TempDB). Per i carichi di lavoro che richiedono circa 50.000 operazioni di I/O al secondo, è consigliabile usare un'unità Ultra SSD. <br/><br/> - Evitare l'uso del sistema operativo o di dischi temporanei per la registrazione o l'archiviazione di database.<br/><br/> - Abilitare la memorizzazione nella cache di lettura sui dischi che ospitano i file di dati e i file di dati di TempDB.<br/><br/> - Non abilitare la memorizzazione nella cache sui dischi che ospitano il file di log.  **Importante**: arrestare SQL Server quando si modificano le impostazioni della cache per un disco di una macchina virtuale di Azure.<br/><br/> - Eseguire lo striping di più dischi dati di Azure per ottenere una maggiore velocità effettiva I/O.<br/><br/> - Formattare con dimensioni di allocazione documentate. <br/><br/> - Posizionare TempDB sull'unità SSD locale per carichi di lavoro SQL Server di importanza strategica (dopo aver scelto le dimensioni della macchina virtuale corrette). |
| [I/O](#io-guidance) |- Abilitare la compressione di pagina del database.<br/><br/> - Abilitare l'inizializzazione immediata per i file di dati.<br/><br/> - Limitare l'aumento automatico delle dimensioni per il database.<br/><br/> - Disabilitare la compattazione automatica del database.<br/><br/> - Spostare tutti i database su dischi dati, inclusi i database di sistema.<br/><br/> - Spostare le directory dei file di traccia e dei log degli errori di SQL Server sui dischi dati.<br/><br/> - Configurare il percorso predefinito del file di backup e del file di database.<br/><br/> - Abilitare le pagine bloccate.<br/><br/> - Applicare le correzioni delle prestazioni di SQL Server. |
| [Elementi specifici delle funzionalità](#feature-specific-guidance) | - Eseguire il backup direttamente nell'archivio BLOB. |

Per altre informazioni su *come* e *perché* eseguire queste ottimizzazioni, vedere i dettagli e le linee guida riportate nelle sezioni seguenti.

## <a name="vm-size-guidance"></a>Linee guida per le dimensioni delle VM

Per le applicazioni sensibili alle prestazioni, è consigliabile usare le seguenti [dimensioni per le macchine virtuali](../sizes.md):

* **SQL Server Enterprise Edition**: DS3_v2 o versione successiva
* **SQL Server Standard e Web Edition**: DS2_v2 o versione successiva

Le macchine virtuali della [serie DSv2](../sizes-general.md#dsv2-series) supportano l'archiviazione premium, consigliata per prestazioni ottimali. Le dimensioni consigliate di seguito sono di base, ma le dimensioni effettive della macchina dipendono dalle esigenze del carico di lavoro richiesto. Le macchine virtuali della serie DSv2 sono macchine virtuali per utilizzo generico, adatte per un'ampia gamma di carichi di lavoro, mentre altre dimensioni delle macchine sono ottimizzate per carichi di lavoro specifici. Ad esempio, la [serie M](../sizes-memory.md#m-series) offre il maggior numero di vCPU e memoria per i carichi di lavoro più grandi di SQL Server. La [serie GS](../sizes-memory.md#gs-series) e la [serie DSv2 11-15](../sizes-memory.md#dsv2-series-11-15) sono ottimizzate per i requisiti di memoria di grandi dimensioni. Entrambe le serie sono disponibili anche in [dimensioni core vincolate](../../windows/constrained-vcpu.md), consentendo di risparmiare denaro per i carichi di lavoro con inferiori richieste di calcolo. Le macchine [serie Ls](../sizes-storage.md) sono ottimizzate per elevata velocità effettiva del disco e I/O. È importante prendere in considerazione il carico di lavoro specifico di SQL Server per la selezione della serie e delle dimensioni della macchina virtuale.

## <a name="storage-guidance"></a>Linee guida per l'archiviazione

Le macchine virtuali serie DS, oltre alle serie DSv2 e GS, supportano le [unità SSD Premium](../disks-types.md). Le unità SSD Premium sono consigliate per tutti i carichi di lavoro di produzione.

> [!WARNING]
> Le unità HDD e SSD Standard sono caratterizzate da latenze e larghezze di banda variabili e sono consigliate solo per i carichi di lavoro di sviluppo e test. Per i carichi di lavoro di produzione si consiglia di usare le unità SSD Premium.

È inoltre consigliabile creare account di archiviazione di Azure nello stesso data center delle macchine virtuali di SQL Server per ridurre i tempi di trasferimento. Quando si crea un account di archiviazione, disabilitare la replica geografica in quanto l'ordine di scrittura coerente su più dischi non è garantito. In alternativa, si consiglia di configurare una tecnologia di ripristino di emergenza di SQL Server tra due data center di Azure. Per altre informazioni, vedere [Disponibilità elevata e ripristino di emergenza per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Linee guida per i dischi

Esistono tre tipi di disco principali in una VM Azure:

* **Disco del sistema operativo**: quando si crea una macchina virtuale di Azure, la piattaforma associa almeno un disco, contrassegnato come unità **C**, alla macchina virtuale per il disco del sistema operativo. Si tratta di un disco rigido virtuale memorizzato come BLOB di pagine nell'archivio.
* **Disco temporaneo**: le macchine virtuali di Azure contengono un altro disco, chiamato disco temporaneo, contrassegnato come unità **D**. Questo è un disco sul nodo che può essere usato come area scratch.
* **Dischi dati**: È possibile associare dischi aggiuntivi alla macchina virtuale come dischi dati che vengono memorizzati nell'archivio come BLOB di pagine.

Nelle sezioni seguenti sono riportati alcuni consigli per l'uso di questi diversi dischi.

### <a name="operating-system-disk"></a>Disco del sistema operativo

Per disco del sistema operativo si intende un disco rigido virtuale che è possibile avviare e montare come versione in esecuzione di un sistema operativo ed è etichettato come unità **C** .

I criteri predefiniti di caching per il disco del sistema operativo predefinito sono **Lettura/Scrittura**. Per le applicazioni sensibili alle prestazioni, è consigliabile usare dischi dati anziché il disco del sistema operativo. Vedere la sezione relativa ai dischi di dati riportata di seguito.

### <a name="temporary-disk"></a>Disco temporaneo

L'unità di archiviazione temporanea, etichettata come unità **D**:, non è persistente nell'archiviazione BLOB di Azure. Non archiviare i file del database utente o i file di log delle transazioni utente nell'unità **D**.

Per le VM serie D, Dv2 e G, l'unità temporanea è basata su SSD. Se il carico di lavoro usa TempDB in modo intensivo, ad esempio per gli oggetti temporanei o join complessi, l'archiviazione di TempDB nell'unità **D** potrebbe comportare una maggiore velocità effettiva e una minore latenza di TempDB. Per uno scenario di esempio, vedere la discussione relativa a TempDB nel post di blog seguente: [Storage Configuration Guidelines for SQL Server on Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm) (Linee guida per la configurazione dell'archiviazione per SQL Server nelle macchine virtuali di Azure).

Per le macchine virtuali che supportano le unità SSD Premium (serie DS, DSv2 e GS), si consiglia di archiviare TempDB su un disco che supporta le unità SSD Premium con la memorizzazione nella cache di lettura abilitata.

Esiste un'eccezione a questo consiglio: _se TempDB è soggetto a un utilizzo intenso in scrittura, è possibile migliorare le prestazioni archiviando TempDB nell'unità locale **D**, che in macchine di queste dimensioni è anche basata su SSD._

### <a name="data-disks"></a>Dischi dati

* **Usare i dischi dati per i file di dati e di log**: se non si usa lo striping del disco, usare due dischi P30 SSD Premium di cui uno contenente i file di log e l'altro contenente i file di dati e TempDB. Ogni unità SSD Premium offre un numero di operazioni di I/O al secondo e larghezza di banda (MB/s) a seconda delle dimensioni, come illustrato nell'articolo [Selezionare un tipo di disco](../disks-types.md). Se si utilizza una tecnica di striping del disco, come Spazi di archiviazione, si ottengono prestazioni ottimali con due pool, uno per il/i file di log e l'altro per i file di dati. Tuttavia, se si prevede di usare le istanze cluster di failover (FCI) di SQL Server, è necessario configurare un pool.

   > [!TIP]
   > - Per i risultati dei test in diverse configurazioni del disco e del carico di lavoro, vedere il post di blog seguente: [Storage Configuration Guidelines for SQL Server on Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/) (Linee guida per la configurazione dell'archiviazione per SQL Server nelle macchine virtuali di Azure).
   > - Per prestazioni di importanza strategica per istanze di SQL Server che necessitano di circa 50.000 operazioni di I/O al secondo, provare a sostituire 10 dischi -P30 con un'unità Ultra SSD. Per altre informazioni, vedere il post di blog seguente: [Mission critical performance with Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/) (Prestazioni di importanza strategica con un'unità Ultra SSD).

   > [!NOTE]
   > Quando si esegue il provisioning di una VM SQL Server nel portale, è possibile modificare la configurazione di archiviazione. A seconda della configurazione, Azure configura uno o più dischi. Più dischi sono combinati in un pool di archiviazione singolo con striping. Entrambi i file di dati e di log risiedono insieme in questa configurazione. Per altre informazioni, vedere [Configurazione dell'archiviazione per le VM di SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Striping del disco**: per una maggiore velocità effettiva, è possibile aggiungere altri dischi dati e usare lo striping del disco. Per determinare il numero di dischi dati, è necessario analizzare il numero di IOPS e larghezza di banda necessari per i file di log e i file di dati e TempDB. Si noti che a seconda delle dimensioni delle VM i limiti nel numero di IOPs e larghezza di banda supportati cambiano. Vedere le tabelle relative agli IOPS per [dimensione di VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Usare le linee guida seguenti:

  * Per Windows 8 e Windows Server 2012 o versioni successive, usare [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739.aspx) applicando le indicazioni seguenti:

      1. Impostare l'interleave (dimensione di striping) su 64 KB (65536 byte) per carichi di lavoro OLTP e su 256 KB (262144 KB) per carichi di lavoro di data warehouse, in modo da evitare effetti sulle prestazioni a causa del mancato allineamento delle partizioni. Questo valore deve essere impostato con PowerShell.
      2. Impostare il numero di colonne sul numero di dischi fisici. Usare PowerShell (e non l'interfaccia utente di Server Manager) per configurare più di 8 dischi. 

    Ad esempio, lo script di PowerShell seguente crea un nuovo pool di archiviazione con la dimensione di interleave impostata su 64 KB e il numero di colonne impostato su 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Per Windows 2008 R2 o versioni precedenti, è possibile usare i dischi dinamici (volumi con striping del sistema operativo) e la dimensione di striping è sempre di 64 KB. Si noti che questa opzione è deprecata a partire da Windows 8 e Windows Server 2012. Per informazioni, vedere l'informativa di supporto relativa al [passaggio dal servizio dischi virtuali all'API di gestione archiviazione di Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Se si usa [Spazi di archiviazione diretta (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) con [Istanze del cluster di failover di SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md), è necessario configurare un singolo pool. Si noti che anche se si possono creare volumi diversi nel pool singolo, tutti condivideranno le stesse caratteristiche, ad esempio gli stessi criteri di memorizzazione nella cache.

  * Determinare il numero di dischi associati al pool di archiviazione dell'utente in base alle aspettative di carico. Tenere presente che le diverse dimensioni di macchine virtuali consentono diversi numeri di dischi dati associati. Per altre informazioni, vedere [Dimensioni delle macchine virtuali in Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Se non si usano unità SSD Premium (scenari di sviluppo e test), è consigliabile aggiungere il numero massimo di dischi dati supportato dalle [dimensioni della macchina virtuale](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e usare lo striping del disco.

* **Memorizzazione dei criteri nella cache**: tenere presenti le raccomandazioni seguenti per la memorizzazione dei criteri nella cache a seconda della configurazione di archiviazione.

  * Se si usano dischi separati per i dati e file di log, abilitare la memorizzazione nella cache di lettura sui dischi dati che ospitano i propri file di dati e i file di dati di TempDB. Ciò può comportare un miglioramento significativo delle prestazioni. Non abilitare la memorizzazione nella cache sul disco che contiene il file di log, perché questo causa una riduzione delle prestazioni.

  * Se si usa lo striping del disco in un singolo pool di archiviazione, la maggior parte dei carichi di lavoro trarrà vantaggio dalla memorizzazione nella cache. Se si dispone di pool di archiviazione separato per i file di log e di dati, abilitare la cache di lettura solo nel pool di archiviazione per i file di dati. Per alcuni carichi di lavoro di scrittura pesanti, è possibile ottenere prestazioni migliori senza la memorizzazione nella cache. Ciò può essere determinato solo tramite test.

  * Le raccomandazioni precedenti si applicano alle unità SSD Premium. Se non si usano unità SSD Premium, non abilitare la memorizzazione nella cache per i dischi dati.

  * Per istruzioni sulla configurazione della memorizzazione nella cache su disco, vedere gli articoli seguenti. Per il modello di distribuzione (ASM) classica, vedere: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Per il modello di distribuzione di Azure Resource Manager, vedere: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) e [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Arrestare il servizio SQL Server quando si modifica l'impostazione della cache dei dischi della VM di Azure per evitare danneggiamenti al database.

* **Dimensioni unità di allocazione NTFS**: quando si formatta il disco dati, è consigliabile usare una dimensione di unità di allocazione di 64 kB per file di log e dati, nonché per TempDB.

* **Procedure consigliate per la gestione del disco**: quando si rimuove un disco dati o si modifica il relativo tipo di cache, arrestare il servizio SQL Server durante la modifica. Quando vengono modificate le impostazioni del caching sul disco del sistema operativo, Azure arresta la VM, cambia il tipo di cache e riavvia la VM. Quando vengono modificate le impostazioni della cache di un disco dati, la VM non viene arrestata, ma il disco dati viene scollegato dalla VM durante la modifica e quindi ricollegato.

  > [!WARNING]
  > Il mancato arresto del servizio SQL Server durante queste operazioni può danneggiare il database.


## <a name="io-guidance"></a>Linee guida per l'I/O

* Quando l'applicazione e le richieste vengono eseguite in parallelo, si ottengono i migliori risultati con le unità SSD Premium. Le unità SSD Premium sono progettate per scenari in cui la profondità della coda I/O è maggiore di 1, pertanto si noteranno eventualmente impercettibili miglioramenti delle prestazioni per le richieste seriali a thread singolo (anche se usano in modo intensivo l'archiviazione). Ad esempio, ciò potrebbe influire sui risultati di test a thread singolo degli strumenti di analisi delle prestazioni, ad esempio SQLIO.

* È consigliabile usare la [compressione di pagina del database](https://msdn.microsoft.com/library/cc280449.aspx) in quanto consente di migliorare le prestazioni dei carichi di lavoro con utilizzo intensivo di I/O. Tuttavia, la compressione dei dati potrebbe incrementare l'utilizzo della CPU nel server di database.

* È possibile abilitare l'inizializzazione immediata dei file per ridurre il tempo necessario per l'allocazione iniziale dei file. Per sfruttare l'inizializzazione immediata dei file, concedere SE_MANAGE_VOLUME_NAME all'account di servizio SQL Server (MSSQLSERVER) e aggiungerlo ai criteri di sicurezza **Esecuzione attività di manutenzione volume**. Se si usa un'immagine della piattaforma SQL Server per Azure, l'account del servizio predefinito (NT Service\MSSQLSERVER) non viene aggiunto ai criteri di sicurezza **Esecuzione attività di manutenzione volume**. In altre parole, l'inizializzazione file immediata non è abilitata in un'immagine della piattaforma Server SQL di Azure. Dopo aver aggiunto l'account del servizio SQL Server ai criteri di sicurezza **Esecuzione attività di manutenzione volume** , riavviare il servizio SQL Server. Potrebbero essere presenti indicazioni sulla sicurezza da considerare per l'utilizzo di questa funzionalità. Per altre informazioni, vedere [Inizializzazione di file di database](https://msdn.microsoft.com/library/ms175935.aspx).

* **aumento automatico delle dimensioni** è considerato una semplice contingenza di crescita imprevista. La crescita dei dati e dei log non viene gestita quotidianamente con l'aumento automatico delle dimensioni. Se si usa questa funzionalità, impostare la precrescita del file mediante l'opzione Dimensioni.

* Verificare che la **compattazione automatica** sia disabilitata per evitare inutili sovraccarichi che possono influire negativamente sulle prestazioni.

* Spostare tutti i database su dischi dati, inclusi i database di sistema. Per altre informazioni vedere l'articolo [Spostare i database di sistema](https://msdn.microsoft.com/library/ms345408.aspx).

* Spostare le directory dei file di traccia e dei log degli errori di SQL Server sui dischi dati. È possibile farlo in Gestione configurazione SQL Server facendo clic con il pulsante destro del mouse sull'istanza di SQL Server e selezionando Proprietà. Le impostazioni per il log degli errori e il file di traccia possono essere modificate nella scheda **Parametri di avvio** . La Directory dump si specifica nella scheda **Avanzate** . La schermata illustra dove cercare il parametro di avvio del log degli errori.

    ![Schermata del log degli errori di SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Configurare il percorso predefinito del file di backup e del file di database. Seguire i consigli elencati in questo articolo ed eseguire le modifiche nella finestra Proprietà server. Per le istruzioni, vedere l'articolo dedicato alla [Visualizzazione o modifica dei percorsi predefiniti per i file di dati e di log (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Le schermata seguente illustra come apportare tali modifiche.

    ![File di log e di backup di SQL](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Attivare le pagine bloccate per ridurre le operazioni di I/O e le attività di paging. Per altre informazioni, vedere l'articolo [Abilitazione dell'opzione Blocco di pagine in memoria (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Se si esegue SQL Server 2012, installare l'aggiornamento cumulativo 10 del Service Pack 1. Questo aggiornamento contiene la correzione per le prestazioni ridotte delle operazioni I/O quando si esegue select nell'istruzione di una tabella temporanea in SQL Server 2012. Per altre informazioni, vedere questo [articolo della Knowledge Base](https://support.microsoft.com/kb/2958012).

* È consigliabile comprimere i file di dati durante il trasferimento in entrata e in uscita di Azure.

## <a name="feature-specific-guidance"></a>Guida specifica delle funzionalità

Alcune distribuzioni possono ottenere ulteriori miglioramenti delle prestazioni usando tecniche di configurazione più avanzate. Nell'elenco seguente vengono evidenziate alcune funzionalità di SQL Server che consentono di ottenere prestazioni migliori:

### <a name="backup-to-azure-storage"></a>Backup nell'archiviazione di Azure
quando si eseguono backup per SQL Server in esecuzione in macchine virtuali di Azure, è possibile usare [Backup di SQL Server nell'URL](https://msdn.microsoft.com/library/dn435916.aspx). Questa funzionalità è disponibile a partire da SQL Server 2012 SP1 CU2 ed è consigliata per il backup su dischi dati associati. Quando si esegue il backup o il ripristino da e verso l'archiviazione di Azure, seguire le indicazioni offerte in [Procedure consigliate e risoluzione dei problemi per il backup di SQL Server nell'URL e Ripristino da backup archiviati nell'archiviazione di Azure](https://msdn.microsoft.com/library/jj919149.aspx). È anche possibile automatizzare i backup usando [Backup automatizzato per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-automated-backup.md).

Prima di SQL Server 2012, usare lo [strumento di backup di SQL Server in Azure](https://www.microsoft.com/download/details.aspx?id=40740). Questo strumento consente di aumentare la velocità effettiva di backup usando più destinazioni di backup in striping.

### <a name="sql-server-data-files-in-azure"></a>File di dati di SQL Server in Azure

questa nuova funzionalità ([File di dati di SQL Server in Azure](https://msdn.microsoft.com/library/dn385720.aspx)) è disponibile a partire da SQL Server 2014. L'esecuzione di SQL Server con file di dati in Azure dimostra le caratteristiche di prestazioni paragonabili a quelle dei dischi dati di Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Istanza del cluster di failover e spazi di archiviazione

Se si usa spazi di archiviazione, quando si aggiungono nodi al cluster sul **conferma** pagina, deselezionare la casella di controllo **aggiungere tutte le risorse di archiviazione idonee al cluster**. 

![Deselezionare l'opzione di archiviazione idonee](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Se si usa Spazi di archiviazione e non si deseleziona **Aggiungi tutte le risorse di archiviazione idonee al cluster**, Windows rende non visibili i dischi virtuali durante il processo di clustering. Di conseguenza, tali dischi non vengono visualizzati in Gestione disco o in Esplora risorse fino a quando gli spazi di archiviazione non vengono rimossi dal cluster e ricollegati usando PowerShell. Spazi di archiviazione consente di raggruppare più dischi in pool di archiviazione. Per altre informazioni, vedere [Spazi di archiviazione](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su archiviazione e prestazioni, vedere [Linee guida di configurazione dell'archiviazione per SQL Server in macchine virtuali di Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Per le procedure consigliate relative alla sicurezza, vedere [Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-security.md).

Esaminare altri articoli relativi alle macchine virtuali di SQL Server in [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md). In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](virtual-machines-windows-sql-server-iaas-faq.md).
