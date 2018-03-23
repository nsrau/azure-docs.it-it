---
title: Procedure consigliate relative alle prestazioni per SQL Server in Azure | Microsoft Docs
description: In questo argomento vengono fornite le procedure consigliate per ottimizzare le prestazioni di SQL Server in Macchine virtuali di Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2018
ms.author: jroth
ms.openlocfilehash: 3458e2f1a09b597c50c01d59eb6522b3fa521310
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure

## <a name="overview"></a>Panoramica

Questo articolo illustra le procedure consigliate per ottimizzare le prestazioni di SQL Server in Macchine virtuali di Microsoft Azure. Durante l'esecuzione di SQL Server in Macchine virtuali di Azure, è consigliabile continuare a usare le stesse opzioni di ottimizzazione delle prestazioni dei database applicabili a SQL Server nell'ambiente server locale. Tuttavia, le prestazioni di un database relazionale in un cloud pubblico dipendono da molti fattori, ad esempio dalle dimensioni della macchina virtuale e dalla configurazione dei dischi dati.

Durante la creazione di immagini di SQL Server, [è possibile eseguire il provisioning delle VM nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md). Le macchine virtuali di SQL Server con provisioning nel Portale con Gestione risorse seguono le procedure consigliate.

Questo articolo riporta informazioni su come ottenere le *migliori* prestazioni per SQL Server nelle VM Azure. Se il carico di lavoro è contenuto, potrebbero non essere necessarie tutte le ottimizzazione elencate di seguito. Prendere in considerazione le esigenze di prestazioni e i modelli di carico di lavoro durante la valutazione di queste indicazioni.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Elenco di controllo rapido

Di seguito è riportato un elenco controllo rapido per ottimizzare le prestazioni di SQL Server in Macchine virtuali di Azure:

| Area | Ottimizzazioni |
| --- | --- |
| [Dimensioni macchina virtuale](#vm-size-guidance) |[DS3](../sizes-memory.md) o superiore per SQL Enterprise.<br/><br/>[DS2](../sizes-memory.md) o superiore per SQL Standard Edition e Web Edition. |
| [Archiviazione](#storage-guidance) |Usare [Archiviazione Premium](../premium-storage.md). Archiviazione Standard è consigliata solo per i carichi di lavoro di sviluppo/test.<br/><br/>Mantenere l'[account di archiviazione](../../../storage/common/storage-create-storage-account.md) e la macchina virtuale di SQL Server nella stessa area.<br/><br/>Disabilitare l' [archiviazione con ridondanza geografica](../../../storage/common/storage-redundancy.md) (replica geografica) di Azure nell'account di archiviazione. |
| [Dischi](#disks-guidance) |Usare almeno 2 [dischi P30](../premium-storage.md#scalability-and-performance-targets) (1 per i file di log, 1 per i file di dati e TempDB).<br/><br/>Evitare l'uso del sistema operativo o di dischi temporanei per la registrazione o l'archiviazione di database.<br/><br/>Abilitare la lettura della cache sui dischi che ospitano i file di dati e TempDB.<br/><br/>Non abilitare la memorizzazione nella cache sui dischi che ospitano il file di log.<br/><br/>Importante: arrestare SQL Server quando si modificano le impostazioni della cache per un disco di una macchina virtuale di Azure.<br/><br/>Eseguire lo striping di più dischi dati di Azure per ottenere una maggiore velocità effettiva I/O.<br/><br/>Formattare con dimensioni di allocazione documentate. |
| [I/O](#io-guidance) |Abilitare la compressione di pagina di database.<br/><br/>Abilitare l'inizializzazione immediata dei file per i file di dati.<br/><br/>Limitare o disabilitare l'aumento automatico delle dimensioni per il database.<br/><br/>Disabilitare la compattazione automatica per il database.<br/><br/>Spostare tutti i database su dischi dati, inclusi i database di sistema.<br/><br/>Spostare le directory dei file di traccia e dei log degli errori di SQL Server sui dischi dati.<br/><br/>Configurare il percorso predefinito del file di backup e del file di database.<br/><br/>Abilitare le pagine bloccate.<br/><br/>Applicare le correzioni delle prestazioni di SQL Server. |
| [Elementi specifici delle funzionalità](#feature-specific-guidance) |Eseguire il backup direttamente nell'archivio BLOB. |

Per altre informazioni su *come* e *perché* eseguire queste ottimizzazioni, vedere i dettagli e le linee guida riportate nelle sezioni seguenti.

## <a name="vm-size-guidance"></a>Linee guida per le dimensioni delle VM

Per le applicazioni sensibili alle prestazioni, è consigliabile usare le seguenti [dimensioni per le macchine virtuali](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json):

* **SQL Server Enterprise Edition**: DS3 o superiore
* **SQL Server Standard e Web Edition**: DS2 o superiore

## <a name="storage-guidance"></a>Linee guida per l'archiviazione

Le VM serie DS, oltre alle serie DSv2 e GS, supportano [Archiviazione Premium](../premium-storage.md). Archiviazione Premium è l'impostazione consigliata per tutti i carichi di lavoro di produzione.

> [!WARNING]
> Archiviazione Standard è caratterizzata da latenze e larghezze di banda variabili ed è consigliata solo per i carichi di lavoro di sviluppo e test. Per i carichi di lavoro di produzione si consiglia di usare Archiviazione Premium.

È inoltre consigliabile creare account di archiviazione di Azure nello stesso data center delle macchine virtuali di SQL Server per ridurre i tempi di trasferimento. Quando si crea un account di archiviazione, disabilitare la replica geografica in quanto l'ordine di scrittura coerente su più dischi non è garantito. In alternativa, si consiglia di configurare una tecnologia di ripristino di emergenza di SQL Server tra due data center di Azure. Per altre informazioni, vedere [Disponibilità elevata e ripristino di emergenza per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Linee guida per i dischi

Esistono tre tipi di disco principali in una VM Azure:

* **Disco del sistema operativo**: quando si crea una macchina virtuale Azure, la piattaforma associa almeno un disco, contrassegnato come unità **C**, alla VM per il disco del sistema operativo. Si tratta di un disco rigido virtuale memorizzato come BLOB di pagine nell'archivio.
* **Disco temporaneo**: le macchine virtuali di Azure contengono un altro disco, chiamato disco temporaneo, contrassegnato come unità **D**. Questo è un disco sul nodo che può essere usato come area scratch.
* **Dischi dati**: è possibile associare dischi aggiuntivi alla macchina virtuale nella forma di dischi dati, che vengono memorizzati nell'archivio come BLOB di pagine.

Nelle sezioni seguenti sono riportati alcuni consigli per l'uso di questi diversi dischi.

### <a name="operating-system-disk"></a>Disco del sistema operativo

Per disco del sistema operativo si intende un disco rigido virtuale che è possibile avviare e montare come versione in esecuzione di un sistema operativo ed è etichettato come unità **C** .

I criteri predefiniti di caching per il disco del sistema operativo predefinito sono **Lettura/Scrittura**. Per le applicazioni sensibili alle prestazioni, è consigliabile usare dischi dati anziché il disco del sistema operativo. Vedere la sezione relativa ai dischi di dati riportata di seguito.

### <a name="temporary-disk"></a>Disco temporaneo

L'unità di archiviazione temporanea, etichettata come unità **D**:, non è persistente nell'archiviazione BLOB di Azure. Non archiviare i file del database utente o i file di log delle transazioni utente nell'unità **D**.

Per le VM serie D, Dv2 e G, l'unità temporanea è basata su SSD. Se il carico di lavoro usa TempDB in modo intensivo, ad esempio per gli oggetti temporanei o join complessi, l'archiviazione di TempDB nell'unità **D** potrebbe comportare una maggiore velocità effettiva e una minore latenza di TempDB.

Per le macchine virtuali che supportano Archiviazione Premium (serie DS, DSv2 e GS), si consiglia di archiviare TempDB su un disco che supporta Archiviazione Premium con il caching di lettura attivato. Esiste un'eccezione a questo consiglio: se TempDB è soggetto a uso intenso in scrittura, è possibile migliorare le prestazioni archiviando TempDB nell'unità locale **D** , che in macchine di queste dimensioni è anche basata su SSD.

### <a name="data-disks"></a>Dischi dati

* **Usare i dischi dati per i file di dati e di log**: se non si usa lo striping del disco, usare 2 [dischi P30](../premium-storage.md#scalability-and-performance-targets) con Archiviazione Premium di cui uno contenente i file di log e l'altro contenente i file di dati e TempDB. Ogni disco di Archiviazione Premium dispone di un numero di IOPs e larghezza di banda (MB/s) a seconda delle dimensioni, come descritto nell'articolo seguente: [Uso di Archiviazione Premium per dischi](../premium-storage.md). Se si usa una tecnica di striping del disco, ad esempio Spazi di archiviazione, è necessario inserire tutti i file nella stessa unità.

   > [!NOTE]
   > Quando si esegue il provisioning di una VM SQL Server nel portale, è possibile modificare la configurazione di archiviazione. A seconda della configurazione, Azure configura uno o più dischi. Più dischi sono combinati in un pool di archiviazione singolo con striping. Entrambi i file di dati e di log risiedono insieme in questa configurazione. Per altre informazioni, vedere [Configurazione dell'archiviazione per le VM di SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Striping del disco**: per una maggiore velocità effettiva, è possibile aggiungere ulteriori dischi dati e usare lo striping del disco. Per determinare il numero di dischi dati, è necessario analizzare il numero di IOPS e larghezza di banda necessari per i file di log e i file di dati e TempDB. Si noti che a seconda delle dimensioni delle VM i limiti nel numero di IOPs e larghezza di banda supportati cambiano. Vedere le tabelle relative agli IOPS per [dimensione di VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Usare le linee guida seguenti:

  * Per Windows 8 e Windows Server 2012 o versioni successive, usare [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739.aspx) applicando le indicazioni seguenti:

      1. Impostare l'interleave (dimensione di striping) su 64 KB (65536 byte) per carichi di lavoro OLTP e su 256 KB (262144 KB) per carichi di lavoro di data warehouse, in modo da evitare effetti sulle prestazioni a causa del mancato allineamento delle partizioni. Questo valore deve essere impostato con PowerShell.
      1. Impostare il numero di colonne sul numero di dischi fisici. Usare PowerShell (e non l'interfaccia utente di Server Manager) per configurare più di 8 dischi. 

    Ad esempio, lo script di PowerShell seguente crea un nuovo pool di archiviazione con la dimensione di interleave impostata su 64 KB e il numero di colonne impostato su 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Per Windows 2008 R2 o versioni precedenti, è possibile usare i dischi dinamici (volumi con striping del sistema operativo) e la dimensione di striping è sempre di 64 KB. Si noti che questa opzione è deprecata a partire da Windows 8 e Windows Server 2012. Per informazioni, vedere l'informativa di supporto relativa al [passaggio dal servizio dischi virtuali all'API di gestione archiviazione di Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Se si usa [Direct degli spazi di archiviazione (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) con uno scenario simile a [Istanze del cluster di failover di SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md), è necessario configurare un singolo pool. Si noti che anche se si possono creare volumi diversi nel pool singolo, tutti condivideranno le stesse caratteristiche, ad esempio gli stessi criteri di memorizzazione nella cache. 

  * Determinare il numero di dischi associati al pool di archiviazione dell'utente in base alle aspettative di carico. Tenere presente che le diverse dimensioni di macchine virtuali consentono diversi numeri di dischi dati associati. Per altre informazioni, vedere [Dimensioni delle macchine virtuali in Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Se non si usa Archiviazione Premium (scenari di sviluppo e test), è consigliabile aggiungere il numero massimo di dischi dati supportato dalle [dimensioni della VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e usare lo striping del disco.

* **Criteri di caching**: per i dischi dati con Archiviazione Premium, attivare il caching di lettura solo sui dischi dati che ospitano i file di dati e TempDB. Se non si usa Archiviazione Premium, non abilitare la memorizzazione nella cache per i dischi dati. Per istruzioni sulla configurazione della memorizzazione nella cache su disco, vedere gli articoli seguenti. Per il modello di distribuzione classico, ovvero ASM, vedere: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Per il modello di distribuzione Azure Resource Manager vedere: [Set-AzureRMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-4.4.1) e [Set-AzureRMVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdatadisk?view=azurermps-4.4.1).

  > [!WARNING]
  > Arrestare il servizio SQL Server quando si modifica l'impostazione della cache dei dischi della VM di Azure per evitare danneggiamenti al database.

* **Dimensioni unità di allocazione NTFS**: quando si formatta il disco dati, è consigliabile usare una dimensione di unità di allocazione di 64 KB per file di log e dati, nonché per TempDB.

* **Procedure consigliate per la gestione del disco**: quando si rimuove un disco dati o si modifica il relativo tipo di cache, arrestare il servizio SQL Server durante la modifica. Quando vengono modificate le impostazioni del caching sul disco del sistema operativo, Azure arresta la VM, cambia il tipo di cache e riavvia la VM. Quando vengono modificate le impostazioni della cache di un disco dati, la VM non viene arrestata, ma il disco dati viene scollegato dalla VM durante la modifica e quindi ricollegato.

  > [!WARNING]
  > Il mancato arresto del servizio SQL Server durante queste operazioni può danneggiare il database.

## <a name="io-guidance"></a>Linee guida per l'I/O

* Quando l'applicazione e le richieste vengono eseguite in parallelo, si ottengono i migliori risultati con Archiviazione Premium. Archiviazione Premium è progettato per scenari in cui la profondità della coda I/O è maggiore di 1, pertanto si noteranno eventualmente impercettibili miglioramenti delle prestazioni per le richieste seriali a thread singolo (anche se usano in modo intensivo l'archiviazione). Ad esempio, ciò potrebbe influire sui risultati di test a thread singolo degli strumenti di analisi delle prestazioni, ad esempio SQLIO.

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

* Se si esegue SQL Server 2012, installare l'aggiornamento cumulativo 10 del Service Pack 1. Questo aggiornamento contiene la correzione per le prestazioni ridotte delle operazioni I/O quando si esegue select nell'istruzione di una tabella temporanea in SQL Server 2012. Per altre informazioni, vedere questo [articolo della Knowledge Base](http://support.microsoft.com/kb/2958012).

* È consigliabile comprimere i file di dati durante il trasferimento in entrata e in uscita di Azure.

## <a name="feature-specific-guidance"></a>Guida specifica delle funzionalità

Alcune distribuzioni possono ottenere ulteriori miglioramenti delle prestazioni usando tecniche di configurazione più avanzate. Nell'elenco seguente vengono evidenziate alcune funzionalità di SQL Server che consentono di ottenere prestazioni migliori:

* **Backup nell'archiviazione di Azure**: quando si eseguono backup per SQL Server in esecuzione in macchine virtuali di Azure, è possibile usare [Backup di SQL Server nell'URL](https://msdn.microsoft.com/library/dn435916.aspx). Questa funzionalità è disponibile a partire da SQL Server 2012 SP1 CU2 ed è consigliata per il backup su dischi dati associati. Quando si esegue il backup o il ripristino da e verso l'archiviazione di Azure, seguire le indicazioni offerte in [Procedure consigliate e risoluzione dei problemi per il backup di SQL Server nell'URL e Ripristino da backup archiviati nell'archiviazione di Azure](https://msdn.microsoft.com/library/jj919149.aspx). È anche possibile automatizzare i backup usando [Backup automatizzato per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-automated-backup.md).

    Prima di SQL Server 2012, usare lo [strumento di backup di SQL Server in Azure](https://www.microsoft.com/download/details.aspx?id=40740). Questo strumento consente di aumentare la velocità effettiva di backup usando più destinazioni di backup in striping.

* **File di dati di SQL Server in Azure**: questa nuova funzionalità ( [file di dati di SQL Server in Azure](https://msdn.microsoft.com/library/dn385720.aspx)) è disponibile a partire da SQL Server 2014. L'esecuzione di SQL Server con file di dati in Azure dimostra le caratteristiche di prestazioni paragonabili a quelle dei dischi dati di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per le procedure consigliate relative alla sicurezza, vedere [Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-security.md).

Esaminare altri articoli relativi alle macchine virtuali di SQL Server in [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md). In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](virtual-machines-windows-sql-server-iaas-faq.md).
