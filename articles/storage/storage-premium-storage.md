---
title: Archiviazione Premium a prestazioni elevate e dischi gestiti di Azure per VM | Documentazione Microsoft
description: Informazioni su Archiviazione Premium a prestazioni elevate e dischi gestiti per le VM di Azure. Le VM di Azure serie DS, DSv2 e GS e Fs supportano Archiviazione Premium.
services: storage
documentationcenter: 
author: ramankumarlive
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: c905bfe672d1027916d7f273ab5ac79ceec9a4d9
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---
# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Archiviazione Premium a prestazioni elevate e dischi gestiti per le VM
Archiviazione Premium di Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali (VM) con carichi di lavoro con I/O intensivo. I dischi delle VM che usano Archiviazione Premium memorizzano i dati in unità a stato solido (SSD). È possibile migrare i dischi delle VM esistenti in Archiviazione Premium di Azure per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi.

In Azure è possibile collegare diversi dischi di Archiviazione Premium a una VM. L'uso di più dischi offre alle applicazioni fino a 256 TB di spazio di archiviazione per ogni VM. Con Archiviazione Premium le applicazioni possono ottenere 80.000 operazioni di I/O al secondo (IOPS) per ogni VM e una velocità effettiva del disco fino a 2.000 megabyte al secondo (Mbps) per ogni VM. Le operazioni di lettura offrono una latenza molto bassa.

Con Archiviazione Premium, Azure offre la possibilità di potenziare e spostare nel cloud le applicazioni aziendali più complesse come Dynamics AX, Dynamics CRM, Exchange Server, SAP Business Suite e farm SharePoint. È possibile eseguire carichi di lavoro del database a prestazioni intensive in applicazioni come SQL Server, Oracle, MongoDB, MySQL e Redis, che richiedono prestazioni elevate e coerenti e una bassa latenza.

> [!NOTE]
> Per ottenere prestazioni ottimali per l'applicazione, è consigliabile eseguire la migrazione in Archiviazione Premium di tutti i dischi di VM che richiedono un numero elevato di operazioni di I/O al secondo. Se il disco non richiede un numero elevato di IOPS, è possibile limitare i costi mantenendolo nel servizio di archiviazione Standard. L'archiviazione Standard archivia i dati dei dischi delle VM in unità disco rigido anziché in unità SSD.
> 

Azure offre due modi per creare dischi di archiviazione premium per le VM:

* **Dischi non gestiti**

    Il metodo originale consiste nell'usare dischi non gestiti. In un disco non gestito si gestiscono gli account di archiviazione utilizzati per archiviare i file di disco rigido virtuale (VHD) che corrispondono ai dischi di VM. I file VHD vengono archiviati come BLOB di pagine in account di archiviazione di Azure. 

* **Dischi gestiti**

    Quando si sceglie [Azure Managed Disks](storage-managed-disks-overview.md), Azure gestisce gli account di archiviazione utilizzati per i dischi della VM. Specificare il tipo di disco (Premium o Standard) e la dimensione del disco necessaria. Azure crea e gestisce il disco automaticamente. Azure gestisce anche l'inserimento dei dischi in più account di archiviazione per rimanere entro i limiti di scalabilità degli account di archiviazione. Azure gestisce tutto questo automaticamente.

Si consiglia di scegliere i dischi gestiti per sfruttare le numerose funzionalità di cui dispongono.

Per iniziare a usare Archiviazione Premium, [creare un account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/). 

Per informazioni sulla migrazione delle VM esistenti in Archiviazione Premium, vedere [Conversione dei dischi di una macchina virtuale da non gestiti a gestiti](../virtual-machines/virtual-machines-windows-convert-unmanaged-to-managed-disks.md) o [Come convertire una macchina virtuale Linux da dischi non gestiti ad Azure Managed Disks](../virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Archiviazione Premium è disponibile in molte aree geografiche. Per un elenco delle aree disponibili, in [Servizi di Azure in base all'area](https://azure.microsoft.com/regions/#services) esaminare le aree in cui sono supportate le VM delle serie con dimensioni specifiche che supportano Archiviazione Premium (VM serie DS, DSV2, GS e Fs).
> 

## <a name="features"></a>Funzionalità

Di seguito sono illustrate alcune delle funzionalità di Archiviazione Premium:

* **Limiti dei dischi di Archiviazione Premium**

    Archiviazione Premium supporta i dischi di VM che possono essere collegati a VM delle serie con dimensioni specifiche. Archiviazione Premium supporta le macchine virtuali serie DS, DSv2, GS e Fs. È possibile scegliere fra sette dimensioni di disco: P4 (32 GB), P6 (64 GB), P10 (128 GB), P20 (512 GB), P30 (1024 GB), P40 (2048 GB), P50 (4095 GB). Attualmente le dimensioni di disco P4 e P6 sono supportate solo per Managed Disks. Ciascuna dimensione di disco ha le proprie specifiche in termini di prestazioni. A seconda dei requisiti dell'applicazione è possibile collegare uno o più dischi alla VM. Le specifiche saranno descritte dettagliatamente nella sezione [Obiettivi di scalabilità e prestazioni di Archiviazione Premium](#premium-storage-scalability-and-performance-targets).

* **BLOB di pagine Premium**

    Archiviazione Premium supporta i BLOB di pagine. Usare i BLOB di pagine per archiviare i dischi persistenti e non gestiti per le VM in Archiviazione Premium. A differenza di Archiviazione Standard, Archiviazione Premium non supporta i BLOB in blocchi, i BLOB di aggiunta, file, tabelle o code. I BLOB di pagine Premium supportano sei dimensioni, da P10 a P50, e P60 (8191 GiB). I BLOB di pagine Premium P60 non supportano il collegamento come dischi di macchine virtuali. 

    Qualsiasi oggetto inserito in un account di Archiviazione Premium è un BLOB di pagine. Il BLOB di pagine si aggancia a una delle dimensioni di provisioning supportate. Questo è il motivo per cui un account di Archiviazione Premium non è destinato all'archiviazione di BLOB di piccole dimensioni.

* **Account di archiviazione Premium**

    Per iniziare a usare Archiviazione Premium, creare un account di archiviazione Premium per dischi non gestiti. Nel [portale di Azure](https://portal.azure.com), per creare un account di Archiviazione Premium, scegliere il livello di prestazioni **Premium**. Selezionare l'opzione di replica **Archiviazione con ridondanza locale**. È anche possibile creare un account di Archiviazione Premium impostando il tipo su **Premium_LRS** in uno dei seguenti percorsi:
    * [API REST di archiviazione](https://docs.microsoft.com/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference) (versione 2014-02-14 o versione successiva)
    * [API REST di gestione servizi](http://msdn.microsoft.com/library/azure/ee460799.aspx) (versione 2014-10-01 o una versione successiva; per le distribuzioni di Azure classiche)
    * [API REST del provider delle risorse di archiviazione di Azure](https://docs.microsoft.com/rest/api/storagerp) (per le distribuzioni di Azure Resource Manager)
    * [Azure PowerShell](../powershell-install-configure.md) (versione 0.8.10 o successiva)

    Nella sezione [Obiettivi di scalabilità e prestazioni di Archiviazione Premium](#premium-storage-scalability-and-performance-targets) sono riportate informazioni sui limiti dell'account di Archiviazione Premium.

* **Archiviazione Premium con ridondanza locale**

    Un account di Archiviazione Premium supporta solo l'archiviazione con ridondanza locale come opzione di replica. L'archiviazione con ridondanza locale mantiene tre copie dei dati in una singola area. Per eseguire il ripristino di emergenza dell'area, è necessario eseguire il backup dei dischi delle VM in un'altra area usando il [Backup di Azure](../backup/backup-introduction-to-azure-backup.md). È necessario anche usare un account di archiviazione con ridondanza geografica come insieme di credenziali di backup. 

    Azure usa l'account di archiviazione come contenitore per i dischi non gestiti. Quando si crea una VM di Azure serie DS, DSv2, GS o Fs con dischi non gestiti e si seleziona un account di archiviazione Premium, il sistema operativo e i dischi dati vengono archiviati in tale account.

## <a name="supported-vms"></a>VM supportate
L'archiviazione Premium supporta le macchine virtuali serie DS, DSv2, GS, Ls e Fs. Con questi tipi di VM è possibile usare dischi di archiviazione sia Standard che Premium. Non è possibile usare i dischi di Archiviazione Premium con MV delle serie non compatibili con Archiviazione Premium.

Per informazioni sui tipi e le dimensioni delle VM in Azure, vedere [Dimensioni delle macchine virtuali Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Per informazioni sui tipi e le dimensioni delle VM in Azure, vedere [Dimensioni delle macchine virtuali Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Di seguito sono indicate alcune funzionalità delle VM serie DS, DSv2, GS, Ls e Fs:

* **Servizio cloud**

    È possibile aggiungere VM serie DS a un servizio cloud contenente solo VM serie DS. Non aggiungere VM serie DS a un servizio cloud esistente che include VM diverse dalla serie DS. È possibile eseguire la migrazione dei dischi rigidi virtuali esistenti in un nuovo servizio cloud che esegue solo VM serie DS. Se si vuole usare lo stesso indirizzo IP virtuale per il nuovo servizio cloud che ospita le VM serie DS, usare gli [indirizzi IP riservati](../virtual-network/virtual-networks-instance-level-public-ip.md). È possibile aggiungere le VM serie GS a un servizio cloud esistente che esegue solo VM serie GS.

* **Disco del sistema operativo**

    È possibile configurare la VM di Archiviazione Premium in modo da usare un disco del sistema operativo Standard o Premium. Per un'esperienza ottimale è consigliabile usare un disco del sistema operativo basato su Archiviazione Premium.

* **Dischi dati**

    È possibile usare dischi di archiviazione sia Premium che Standard nella stessa VM di Archiviazione Premium. Con Archiviazione Premium è possibile effettuare il provisioning di una macchina virtuale e collegare più dischi dati persistenti alla macchina virtuale. Se necessario, è possibile eseguire lo striping dei dischi per aumentare la capacità e le prestazioni del volume.

    > [!NOTE]
    > Se si esegue lo striping dei dischi dati di Archiviazione Premium mediante [Spazi di archiviazione](http://technet.microsoft.com/library/hh831739.aspx), è consigliabile configurare gli spazi di archiviazione con 1 colonna per ogni disco utilizzato. In caso contrario, le prestazioni complessive del volume in cui è stato eseguito lo striping possono essere inferiori al previsto a causa di una distribuzione non uniforme del traffico di dati da un disco a un altro. Per impostazione predefinita, in Server Manager è possibile impostare le colonne per un massimo di 8 dischi. Quando si collegano più di 8 dischi, usare PowerShell per creare il volume. Specificare il numero di colonne manualmente. In caso contrario, l'interfaccia utente di Server Manager continuerà a usare 8 colonne anche se il numero di dischi è maggiore. Ad esempio, se si desidera gestire 32 dischi in un unico striping, è necessario specificare 32 colonne. Per specificare il numero di colonne utilizzate dal disco virtuale è possibile usare il parametro [NumberOfColumns](http://technet.microsoft.com/library/hh848643.aspx) del cmdlet di PowerShell *New-VirtualDisk*. Per altre informazioni, vedere [Panoramica sugli spazi di archiviazione](http://technet.microsoft.com/library/hh831739.aspx) e [Domande frequenti sugli spazi di archiviazione](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
    >
    > 

* **Cache**

    Le VM della serie con dimensioni che supportano Archiviazione Premium hanno una capacità di memorizzazione nella cache unica per livelli elevati di velocità effettiva e latenza. La funzionalità di memorizzazione nella cache supera le prestazioni del disco di Archiviazione Premium sottostante. È possibile impostare i criteri di memorizzazione nella cache del disco per i dischi di Archiviazione Premium su **ReadOnly**, **ReadWrite** o **None**. Il criterio predefinito di memorizzazione nella cache su disco è **ReadOnly** per tutti i dischi dati Premium e **ReadWrite** per i dischi del sistema operativo. Per ottenere prestazioni ottimali per l'applicazione, usare l'impostazione corretta della cache. Ad esempio, per dischi dati con un numero elevato di letture o di sola lettura, come i file di dati di SQL Server, impostare i criteri di memorizzazione nella cache del disco su **ReadOnly**. Per i dischi dati con un numero elevato di scritture o di sola scrittura, come i file di log di SQL Server, impostare i criteri di memorizzazione nella cache del disco su **None**. Per altre informazioni su come ottimizzare la progettazione con Archiviazione Premium, vedere [Progettare ai fini delle prestazioni con Archiviazione Premium](storage-premium-storage-performance.md).

* **Analisi**

    Per analizzare le prestazioni delle VM usando dischi in Archiviazione Premium, attivare la diagnostica di VM nel [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [Azure VM monitoring with Azure Diagnostics Extension](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) (Monitoraggio delle VM di Azure con Estensione di Diagnostica di Azure). 

    Per visualizzare le prestazioni del disco, usare strumenti basati sul sistema operativo, ad esempio [Monitoraggio prestazioni di Windows](https://technet.microsoft.com/library/cc749249.aspx) per le VM Windows e il comando [iostat](http://linux.die.net/man/1/iostat) per le VM Linux.

* **Limiti di scalabilità e prestazioni delle VM**

    Ogni dimensione di VM supportata da Archiviazione Premium presenta limiti di scalabilità e specifiche di prestazioni in termini di IOPS, larghezza di banda e numero di dischi che è possibile collegare a ogni VM. Quando si usano dischi di Archiviazione Premium con VM, assicurarsi che IOPS e larghezza di banda nella VM siano sufficienti per gestire il traffico dei dischi.

    Ad esempio, una VM STANDARD_DS1 ha un larghezza di banda dedicata di 32 MB/s per il traffico nei dischi di archiviazione Premium. Un disco di Archiviazione Premium P10 può offrire una larghezza di banda di 100 MB/s. Se un disco di Archiviazione Premium P10 è collegato a questa VM, può passare solo un massimo di 32 MB/s. Non può usare il massimo valore di 100 MB/s che il disco P10 è in grado di fornire.

    Attualmente la più grande VM della serie DS è la Standard_DS15_v2. La Standard_DS15_v2 può fornire fino a 960 MB/s su tutti i dischi. La più grande VM della serie GS è la Standard_GS5. La Standard_GS5 può fornire fino a 2.000 MB/s su tutti i dischi.

    Si noti che questi limiti riguardano solo il traffico su disco. Questi limiti non includono i riscontri cache e il traffico di rete. Una larghezza di banda separata è disponibile per il traffico di rete della VM. La larghezza di banda per il traffico di rete è diversa dalla larghezza di banda dedicata utilizzata dai dischi di Archiviazione Premium.

    Per le informazioni più aggiornate sul numero massimo di IOPS e sulla velocità effettiva (larghezza di banda) massima per le VM supportate da Archiviazione Premium, vedere [Dimensioni per le macchine virtuali Windows in Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Dimensioni delle macchine virtuali Linux in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    Per altre informazioni sui dischi di Archiviazione Premium e i limiti di IOPS e velocità effettiva, vedere la tabella nella sezione successiva.

## <a name="scalability-and-performance-targets"></a>Obiettivi di scalabilità e prestazioni
Questa sezione descrive tutti gli obiettivi di scalabilità e prestazioni da considerare quando si usa Archiviazione Premium.

Obiettivi di scalabilità degli account di archiviazione Premium:

| Capacità account totale | Larghezza di banda totale per un account di archiviazione con ridondanza locale |
| --- | --- | 
| Capacità disco: 35 TB <br>Capacità snapshot: 10 TB | Fino a 50 gigabit al secondo per dati in ingresso<sup>1</sup> e in uscita<sup>2</sup> |

<sup>1</sup> Tutti i dati (richieste) inviati a un account di archiviazione

<sup>2</sup> Tutti i dati (risposte) ricevuti da un account di archiviazione

Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md).

Se si usano account di archiviazione Premium per dischi non gestiti e l'applicazione supera gli obiettivi di scalabilità di un singolo account di archiviazione, si potrebbe prendere in considerazione la migrazione a Managed Disks. Se non si vuole eseguire la migrazione a Managed Disks, compilare l'applicazione per l'uso di più account di archiviazione. Quindi partizionare i dati tra tali account di archiviazione. Ad esempio, se si desidera collegare dischi da 51 TB tra più VM, distribuirli in due account di archiviazione. Il limite per un account di Archiviazione Premium singolo è 35 TB. Accertarsi che i dischi di cui viene effettuato il provisioning in un singolo account di archiviazione Premium non superino mai i 35 TB.

### <a name="premium-storage-disk-limits"></a>Limiti dei dischi di Archiviazione Premium
Quando si effettua il provisioning di un disco di archiviazione Premium, la dimensione del disco determina il livello massimo di IOPS e velocità effettiva (larghezza di banda). Azure offre sette tipi di dischi di Archiviazione Premium: P4 (solo Managed Disks), P6 (solo Managed Disks), P10, P20, P30, P40 e P50. Ogni tipo di disco di Archiviazione Premium ha limiti specifici di IOPS e velocità effettiva. I limiti per i tipi di dischi sono descritti nella tabella seguente:

| Tipo di disco Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Dimensioni disco           | 32 GB| 64 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS per disco       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Velocità effettiva per disco | 25 MB al secondo  | 50 MB al secondo  | 100 MB al secondo | 150 MB al secondo | 200 MB al secondo | 250 MB al secondo | 250 MB al secondo | 

> [!NOTE]
> Assicurarsi che nella VM sia disponibile una larghezza di banda sufficiente per il traffico dell'unità disco, come descritto in [Macchine virtuali supportate da Archiviazione Premium](#premium-storage-supported-vms). In caso contrario, la velocità effettiva del disco e IOPS sarà limitata a valori più bassi. IOPS e velocità effettiva massima si basano sui limiti della VM, non sui limiti del disco descritti nella tabella precedente.  
> 
> 

Ecco alcuni aspetti importanti da conoscere sulla scalabilità e le prestazioni di Archiviazione Premium:

* **Capacità fornita e prestazioni**

    Quando si effettua il provisioning di un disco di archiviazione Premium, a differenza di Archiviazione Standard, vengono garantiti livelli di capacità, IOPS e velocità effettiva del disco. Se ad esempio si crea un disco P50, Azure effettua il provisioning di 4.095 GB di capacità di archiviazione, 7.500 IOPS e 250 MB/s di velocità effettiva per tale disco. L'applicazione può usare la totalità o una della capacità e delle prestazioni.

* **Dimensioni disco**

    Azure associa la dimensione del disco, arrotondata per eccesso, all'opzione relativa al disco di archiviazione Premium più vicina, come specificato nella tabella della sezione precedente. Ad esempio, una dimensione del disco di 100 GB viene classificata come opzione P10. È possibile eseguire fino a 500 IOPS, con una velocità effettiva fino a 100 MB/s. Analogamente, un disco di 400 GB viene classificato come P20. È possibile eseguire fino a 2.300 IOPS, con una velocità effettiva di 150 MB/s.
    
    > [!NOTE]
    > È possibile aumentare facilmente le dimensioni dei dischi esistenti. Ad esempio, è possibile aumentare le dimensioni di un disco da 30 GB fino a 128 GB o persino a 1 TB. Oppure è possibile convertire un disco P20 in un disco P30 perché si necessita di una capacità maggiore o di IOPS e velocità effettiva più elevati. 
    > 
 
* **Dimensioni di I/O**

    La dimensione di un I/O è 256 KB. Se il volume dei dati è inferiore a 256 KB, viene considerata 1 singola unità di I/O. Le dimensioni di I/O superiori vengono calcolate come più I/O da 256 KB. Ad esempio, 1.100 KB di I/O corrispondono a 5 unità di I/O.

* **Velocità effettiva**

    Il limite della velocità effettiva include le scritture su disco e le operazioni di letture dal disco che non vengono servite dalla cache. Ad esempio, un disco P10 ha una velocità effettiva di 100 MB/s per disco. La tabella seguente illustra alcuni esempi di velocità effettiva valida per un disco P10:

    | Velocità effettiva massima per un disco P10 | Letture non cache dal disco | Scritture non cache nel disco |
    | --- | --- | --- |
    | 100 MB/s | 100 MB/s | 0 |
    | 100 MB/s | 0 | 100 MB/s |
    | 100 MB/s | 60 MB/s | 40 MB/s |

* **Riscontri cache**

    I riscontri nella cache non sono limitati da IOPS o velocità di trasmissione del disco allocati. Ad esempio, quando si usa un disco dati con l'impostazione della cache **ReadOnly** in una VM supportata da Archiviazione Premium, le letture gestite dalla cache non sono soggette ai limiti di IOPS e di velocità effettiva del disco. Se il carico di lavoro di un disco è composto prevalentemente da letture, è possibile ottenere una velocità di trasmissione molto elevata. La cache è soggetta a limiti di velocità effettiva e IOPS separati a livello della VM, in base alle dimensioni della stessa. Le VM della serie DS hanno circa 4.000 IOPS e 33 MB/s per memoria centrale per la cache e gli I/O dell'unità SSD locale. Le VM della serie GS hanno circa 5.000 IOPS e 50 MB/s per memoria centrale per la cache e gli I/O dell’unità SSD locale. 

## <a name="throttling"></a>Limitazione
La limitazione può verificarsi se l'applicazione IOPS o la velocità effettiva supera i limiti allocati per un disco di Archiviazione Premium. La limitazione può anche verificarsi se il traffico su disco totale in tutti i dischi della VM supera il limite della larghezza di banda del disco disponibile per la VM. Per evitare la limitazione, è consigliabile limitare il numero di richieste di I/O in sospeso per il disco. Usare un limite basato sugli obiettivi di scalabilità e prestazioni per il disco di cui è stato eseguito il provisioning e sulla larghezza di banda del disco disponibile per la VM.  

L'applicazione otterrà la latenza più bassa quando viene progettata in modo da evitare la limitazione. Tuttavia, se il numero di richieste di I/O in sospeso per il disco è troppo basso, l'applicazione non potrà sfruttare i livelli massimi di IOPS e velocità effettiva disponibili per il disco.

Gli esempi seguenti illustrano come calcolare i livelli di limitazione. Tutti i calcoli sono basati su una dimensione dell'unità di I/O pari a 256 KB.

### <a name="example-1"></a>Esempio 1
L'applicazione ha elaborato 495 unità di I/O da 16 KB in un disco P10. Le unità di I/O vengono conteggiate come 495 IOPS. Se si tenta un I/O di 2 MB nello stesso secondo, il totale di unità di I/O è uguale a 495 + 8 IOPS. Il motivo è che 2 MB di I/O corrisponde a 2.048 KB / 256 KB = 8 unità di I/O quando le dimensioni dell'unità di I/O sono pari a 256 KB. Poiché la somma di 495 + 8 supera il limite di 500 IOPS per il disco, entra in funzione la limitazione.

### <a name="example-2"></a>Esempio 2
L'applicazione ha elaborato 400 unità di I/O da 256 KB in un disco P10. La larghezza di banda totale utilizzata è (400 &#215; 256) / 1.024 KB = 100 MB/sec. Il limite di velocità effettiva di un disco P10 è 100 MB al secondo. Se l'applicazione tenta di eseguire altre operazioni di I/O nello stesso secondo verrà limitata, in quanto supera il limite allocato.

### <a name="example-3"></a>Esempio 3
Si dispone di una macchina virtuale DS4 con due dischi P30 collegati. Ogni disco P30 è in grado di supportare una velocità effettiva pari a 200 MB/s. Tuttavia, una VM DS4 ha una capacità di larghezza di banda su disco totale pari a 256 MB/s. Non è possibile guidare entrambi i dischi collegati alla velocità effettiva massima della VM DS4 contemporaneamente. Per risolvere il problema, è possibile sostenere un traffico di 200 MB/s su un disco e di 56 MB/s sull'altro disco. Se la somma del traffico sui dischi è superiore a 256 MB/s, il traffico su disco viene limitato.

> [!NOTE]
> Se il traffico su disco è costituito principalmente da I/O di dimensioni ridotte, l'applicazione raggiungerà probabilmente il limite di IOPS prima del limite di velocità effettiva. Al contrario, se il traffico su disco è costituito principalmente da I/O di grandi dimensioni, l'applicazione raggiungerà probabilmente il limite di velocità effettiva prima del limite di IOPS. È possibile ottimizzare il numero di IOPS dell'applicazione e la capacità di velocità effettiva con dimensioni ottimali dell'I/O. Inoltre è possibile limitare il numero di richieste di I/O in sospeso per un disco.
> 

Per informazioni sulla progettazione per prestazioni elevate usando Archiviazione Premium, vedere [Progettare ai fini delle prestazioni con Archiviazione Premium](storage-premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Snapshot e Copia BLOB

Per il servizio Archiviazione, il file VHD è un BLOB di pagine. È possibile creare snapshot dei BLOB di pagine e copiarli in un'altra posizione, ad esempio un diverso account di archiviazione.

### <a name="unmanaged-disks"></a>Dischi non gestiti

Creare [snapshot incrementali](storage-incremental-snapshots.md) dei dischi Premium non gestiti nello stesso modo in cui si usano gli snapshot con Archiviazione Standard. Archiviazione Premium supporta solo l'archiviazione con ridondanza locale come opzione di replica. È consigliabile creare gli snapshot e quindi copiarli in un account di archiviazione Standard con ridondanza geografica. Per altre informazioni, vedere [Opzioni di ridondanza di Archiviazione di Azure](storage-redundancy.md).

Se un disco è collegato a una VM, alcune operazioni API sul disco non sono consentite. Ad esempio, non è possibile eseguire un'operazione [Copy Blob](/rest/api/storageservices/Copy-Blob) sul BLOB finché il disco è collegato a una VM. Creare invece prima di tutto uno snapshot di quel BLOB usando l'API REST [BLOB di snapshot](/rest/api/storageservices/Snapshot-Blob). Eseguire quindi [Copy BLOB](/rest/api/storageservices/Copy-Blob) sullo snapshot per copiare il disco collegato. In alternativa è possibile scollegare il disco e quindi eseguire le operazioni necessarie.

I limiti seguenti si applicano agli snapshot di BLOB di Archiviazione Premium:

| Limite di Archiviazione Premium | Valore |
| --- | --- |
| Massimo numero di snapshot per BLOB | 100 |
| Capacità dell'account di archiviazione per gli snapshot<br>(Include i dati solo degli snapshot; non include i dati del BLOB di base.) | 10 TB |
| Minimo tempo tra snapshot consecutivi | 10 minuti |

Per conservare copie con ridondanza geografica degli snapshot, è possibile copiare gli snapshot da un account di archiviazione Premium a un account di archiviazione Standard con ridondanza geografica mediante AzCopy o Copy Blob. Per altre informazioni, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md) e [Copy Blob](/rest/api/storageservices/Copy-Blob).

Per informazioni dettagliate sull'esecuzione di operazioni REST sui BLOB di pagine negli account di archiviazione Premium, vedere [Using Blob Service Operations with Azure Premium Storage](http://go.microsoft.com/fwlink/?LinkId=521969) (Uso delle operazioni del servizio BLOB con Archiviazione Premium di Azure).

### <a name="managed-disks"></a>Dischi gestiti

Uno snapshot di un disco gestito è una copia di sola lettura del disco gestito. Lo snapshot è archiviato come disco gestito standard. Attualmente gli [snapshot incrementali](storage-incremental-snapshots.md) non sono supportati per i dischi gestiti. Per informazioni su come creare uno snapshot per un disco gestito, vedere gli articoli relativi alla creazione di una copia di un disco rigido virtuale archiviato come disco gestito di Azure usando snapshot gestiti [in Windows](../virtual-machines/virtual-machines-windows-snapshot-copy-managed-disk.md) o [in Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md).

Se un disco gestito è collegato a una VM, alcune operazioni API sul disco non sono consentite. Ad esempio, non è possibile generare una firma di accesso condiviso per eseguire un'operazione di copia mentre il disco è collegato a una macchina virtuale. Creare invece prima di tutto uno snapshot del disco e quindi eseguire la copia dello snapshot. In alternativa, è possibile scollegare il disco e quindi generare una firma di accesso condiviso per eseguire l'operazione di copia.


## <a name="premium-storage-for-linux-vms"></a>Archiviazione Premium per VM Linux
È possibile usare le informazioni seguenti per configurare le VM Linux in Archiviazione Premium:

Per raggiungere gli obiettivi di scalabilità per Archiviazione Premium per tutti i dischi di Archiviazione Premium con cache impostata su **ReadOnly** o **None**, è necessario disabilitare le "barriere" in fase di montaggio del file system. Non sono necessarie barriere per questo scenario perché le scritture relative ai dischi di archiviazione Premium sono durevoli per queste impostazioni della cache. Quando la richiesta di scrittura viene completata in modo corretto, i dati sono stati scritti nell'archivio persistente. Per disabilitare le "barriere", usare uno dei metodi seguenti. Scegliere l'opzione per il file system:
  
* Per **reiserFS**, per disabilitare le barriere, usare l'opzione di montaggio `barrier=none`. (Per abilitare le barriere, usare `barrier=flush`.)
* Per **ext3/ext4**, per disabilitare le barriere, usare l'opzione di montaggio `barrier=0`. (Per abilitare le barriere, usare `barrier=1`.)
* Per **XFS**, per disabilitare le barriere, usare l'opzione di montaggio `nobarrier`. (Per abilitare le barriere, usare `barrier`.)
* Per i dischi di Archiviazione Premium con cache impostata su **ReadWrite**, abilitare le barriere per la durabilità della scrittura.
* Per far sì che le etichette di volume si mantengano dopo il riavvio della VM, è necessario aggiornare /etc/fstab con i riferimenti degli identificatori universalmente univoci (UUID) ai dischi. Per altre informazioni, vedere [Aggiungere un disco gestito a una VM Linux](../virtual-machines/linux/add-disk.md).

Le seguenti distribuzioni Linux sono state convalidate per Archiviazione Premium di Azure. Per prestazioni e stabilità migliori con Archiviazione Premium si consiglia di aggiornare le VM ad almeno una di queste versioni (o versioni successive). Alcune versioni richiedono la versione più recente di Linux Integration Services (LIS) 4.0 per Azure. Per scaricare e installare una distribuzione, fare clic sul collegamento riportato nella tabella seguente. Nuove immagini vengono aggiunte all'elenco non appena viene completata la convalida. Si noti che le nostre convalide mostrano che le prestazioni variano per ogni immagine. Le prestazioni dipendono dalle caratteristiche del carico di lavoro e dalle impostazioni. Immagini diverse sono ottimizzate per tipi di carico di lavoro diversi.

| Distribuzione | Versione | Kernel supportato | Dettagli |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 richiesto](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Vedere la nota nella sezione successiva* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 consigliato](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Vedere la nota nella sezione successiva* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 o RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 or RHCK con [LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 or RHCK con [LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Driver LIS per Openlogic CentOS

Se si eseguono VM OpenLogic CentOS, eseguire il comando seguente per installare i driver più recenti:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Per attivare i nuovi driver, riavviare il computer.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Quando si usa Archiviazione Premium, tenere conto delle considerazioni seguenti relative alla fatturazione:

* **Dimensione del BLOB e del disco di Archiviazione Premium**

    La fatturazione per un BLOB o un disco di Archiviazione Premium dipende dalla dimensione del BLOB o del disco di cui è stato effettuato il provisioning. Azure associa le dimensioni di provisioning (arrotondate per eccesso) all'opzione relativa al disco di Archiviazione Premium più vicina. Per informazioni dettagliate, vedere [Obiettivi di scalabilità e prestazioni di Archiviazione Premium](#premium-storage-scalability-and-performance-targets). Viene eseguito il mapping di ogni disco gestito a una delle dimensioni di provisioning dei dischi supportate e viene eseguita la relativa fatturazione. La fatturazione per qualsiasi disco di cui sia stato effettuato il provisioning viene ripartita in modo proporzionale in base alle ore usando il prezzo mensile dell'offerta di Archiviazione Premium. Ad esempio, se è stato effettuato il provisioning di un disco P10 e lo si è eliminato dopo 20 ore verranno fatturate 20 ore per l'offerta P10, indipendentemente dalla quantità di dati effettivamente scritti sul disco, dalle operazioni IOPS eseguite e dalla velocità effettiva usata.

* **Snapshot dei dischi non gestiti Premium**

    Gli snapshot sui dischi non gestiti Premium sono fatturati in base alla capacità aggiuntiva utilizzata dagli snapshot stessi. Per altre informazioni sugli snapshot, vedere [Creazione di uno snapshot di un BLOB](/rest/api/storageservices/Snapshot-Blob).

* **Snapshot dei dischi gestiti Premium**

    Uno snapshot di un disco gestito è una copia di sola lettura del disco. Il disco è archiviato come disco gestito standard. Il costo di uno snapshot corrisponde a quello di un disco gestito Standard. Ad esempio, se si acquisisce uno snapshot di un disco gestito Premium da 128 GB, il costo dello snapshot sarà equivalente a quello di un disco gestito Standard da 128 GB.  

* **Trasferimenti di dati in uscita**

    I [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/data-transfers/) (dati in uscita dai data center di Azure) vengono fatturati in base all'utilizzo di larghezza di banda.

Per informazioni dettagliate sui prezzi di Archiviazione Premium, sulle VM supportate da Archiviazione Premium e su Managed Disks, vedere questi articoli:

* [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Prezzi delle macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Prezzi di Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Supporto di Backup di Azure 

Per eseguire il ripristino di emergenza dell'area, è necessario eseguire il backup dei dischi delle VM in un'altra area usando [Backup di Azure](../backup/backup-introduction-to-azure-backup.md) e un account di archiviazione con ridondanza geografica come insieme di credenziali di backup.

Per creare un processo di backup con backup basati sul tempo, criteri semplici per il ripristino delle VM e la conservazione dei backup, usare Backup di Azure. È possibile usare Backup con dischi gestiti e non gestiti. Per altre informazioni, vedere [Backup di macchine virtuali di Azure in insiemi di credenziali di Servizi di ripristino](../backup/backup-azure-vms-first-look-arm.md) e [Uso delle macchine virtuali con dischi gestiti con Backup di Azure](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Archiviazione Premium, vedere gli articoli seguenti.

### <a name="design-and-implement-with-premium-storage"></a>Progettare e implementare con Archiviazione Premium
* [Progettare ai fini delle prestazioni con Archiviazione Premium](storage-premium-storage-performance.md)
* [Operazioni di archiviazione BLOB con Archiviazione Premium](http://go.microsoft.com/fwlink/?LinkId=521969)

### <a name="operational-guidance"></a>Informazioni operative
* [Migrazione ad Archiviazione Premium di Azure](storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>Post di BLOG
* [Archiviazione Premium di Azure disponibile a livello generale](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
* [Annuncio della serie GS: aggiunta del supporto di archiviazione Premium per le macchine virtuali di grandi dimensioni nel cloud pubblico](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)

