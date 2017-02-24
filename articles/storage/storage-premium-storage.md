---
title: Archiviazione Premium a prestazioni elevate e dischi delle macchine virtuali di Azure | Microsoft Docs
description: Illustra Archiviazione Premium a prestazioni elevate e i dischi delle macchine virtuali di Azure gestiti e non gestiti. Le macchine virtuali di Azure serie DS, DSv2 e GS supportano Archiviazione Premium.
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
ms.date: 02/06/2017
ms.author: ramankum
translationtype: Human Translation
ms.sourcegitcommit: 3a353bc874c1827f8a0fc85352894ad96cff16b5
ms.openlocfilehash: c9e43df37784999036c6cf250f27a808f79ebe2f


---
# <a name="high-performance-premium-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Archiviazione Premium a prestazioni elevate e dischi delle macchine virtuali di Azure
Archiviazione Premium di Microsoft Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali (VM) che eseguono carichi di lavoro con elevato numero di operazioni di I/O. I dischi delle macchine virtuali che usano Archiviazione Premium memorizzano i dati in unità a stato solido (SSD). È possibile migrare i dischi delle macchine virtuali dell'applicazione ad Archiviazione Premium di Azure per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi.

Una macchina virtuale di Azure supporta il collegamento di diversi dischi di archiviazione Premium, offrendo alle fino a 64 TB di spazio di archiviazione per ogni macchina virtuale. Archiviazione Premium permette alle applicazioni di raggiungere 80.000 IOPS (operazioni di input/output al secondo) per ogni macchina virtuale e fino a 2000 MB/s di velocità effettiva dei dischi per ogni macchina virtuale, con una latenza estremamente bassa per le operazioni di lettura.

Con Archiviazione Premium, Azure offre la possibilità di trasferire in modalità lift-and-shift nel cloud le applicazioni aziendali più complesse, come Dynamics AX, Dynamics CRM, Exchange Server, farm di SharePoint e SAP Business Suite. È possibile eseguire un'ampia gamma di carichi di lavoro del database a prestazioni intensive, come SQL Server, Oracle, MongoDB, MySQL e Redis, che richiedono prestazioni elevate e coerenti e una bassa latenza su Archiviazione Premium.

> [!NOTE]
> Per ottenere prestazioni ottimali per l'applicazione, è consigliabile eseguire la migrazione in Archiviazione Premium di qualsiasi disco di macchina virtuale che richieda un numero elevato di IOPS. Se il disco non richiede un numero elevato di IOPS, è possibile limitare i costi mantenendolo in Archiviazione Standard, che archivia i dati dei dischi delle macchine virtuali in unità disco rigido (HDD) invece che in unità SSD.
> 

Per creare dischi Premium per le macchine virtuali di Azure è possibile procedere in due modi:

**Dischi non gestiti**: si tratta del metodo originale con cui vengono gestiti gli account di archiviazione usati per archiviare i file VHD che corrispondono ai dischi delle macchine virtuali. I file VHD vengono archiviati come BLOB di pagine in account di archiviazione. 

**[Azure Managed Disks](storage-managed-disks-overview.md)**: questa funzionalità gestisce gli account di archiviazione usati per i dischi delle macchine virtuali. Specificando il tipo, Premium o Standard, e le dimensioni del disco necessarie, Azure crea e gestisce automaticamente il disco. Azure gestisce anche l'inserimento dei dischi in più account di archiviazione per rimanere entro i limiti di scalabilità degli account di archiviazione.

Anche se entrambi i tipi di disco sono disponibili, è consigliabile usare Managed Disks per sfruttarne appieno le varie funzionalità.

Per iniziare a usare gratuitamente Archiviazione Premium di Azure, visitare [questa pagina](https://azure.microsoft.com/pricing/free-trial/). 

Per informazioni sulla migrazione delle macchine virtuali esistenti ad Archiviazione Premium, vedere gli articoli relativi alla [migrazione di una macchina virtuale Windows di Azure esistente in Managed Disks](../virtual-machines/virtual-machines-windows-convert-unmanaged-to-managed-disks.md) o alla [migrazione di una macchina virtuale Linux di Azure esistente in Managed Disks](../virtual-machines/virtual-machines-linux-convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Archiviazione Premium è attualmente supportata nella maggior parte delle aree. Per un elenco delle aree disponibili, vedere la pagina [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/#services) e cercare le aree in cui sono supportate le macchine virtuali serie DS, DSV2, Fs e GS.
> 

## <a name="premium-storage-features"></a>Funzionalità di Archiviazione Premium

Di seguito sono illustrate alcune delle funzionalità di Archiviazione Premium.

**Dischi di archiviazione Premium**: Archiviazione Premium di Azure supporta dischi di macchine virtuali che possono essere collegati a macchine virtuali di serie e dimensioni specifiche, incluse le serie DS, DSv2, GS e Fs. È possibile scegliere tra tre dimensioni disco, ovvero P10 (128 GiB), P20 (512 GiB) e P30 (1024 GiB), ciascuna delle quali presenta specifiche proprie in termini di prestazioni. A seconda dei requisiti dell'applicazione è possibile collegare uno o più dischi alla macchina virtuale. Nella sezione seguente [Obiettivi di scalabilità e prestazioni di Archiviazione Premium ](#premium-storage-scalability-and-performance-targets) verranno descritte in dettaglio le specifiche.

**BLOB di pagine Premium**: Archiviazione Premium supporta i BLOB di pagine, che vengono usati per archiviare dischi persistenti non gestiti per le macchine virtuali. A differenza di Archiviazione Standard, Archiviazione Premium non supporta BLOB in blocchi, BLOB di aggiunta, file, tabelle o code.
Qualsiasi oggetto inserito in un account di archiviazione Premium sarà un BLOB di pagine e verrà bloccato a una delle dimensioni di provisioning supportate. Questo è il motivo per cui un account di archiviazione Premium non è destinato all'archiviazione di BLOB di piccole dimensioni.

**Account di archiviazione Premium**: per iniziare a usare Archiviazione Premium, creare un account di archiviazione Premium per dischi non gestiti. Se si preferisce usare il [portale di Azure](https://portal.azure.com), è possibile creare un account di archiviazione Premium specificando il livello di prestazioni "Premium" e "Archiviazione con ridondanza locale" come opzione di replica. È anche possibile creare un account di archiviazione Premium specificando il tipo "Premium_LRS" tramite l'[API REST di archiviazione](/rest/api/storageservices/fileservices/Azure-Storage-Services-REST-API-Reference) 2014-02-14 o versione successiva, l'[API REST di Gestione dei servizi](http://msdn.microsoft.com/library/azure/ee460799.aspx) 2014-10-01 o versione successiva (distribuzioni classiche), il [Riferimento all'API REST del provider di risorse di Archiviazione di Azure](/rest/api/storagerp) (distribuzioni Azure Resource Manager) e [Azure PowerShell](../powershell-install-configure.md) 0.8.10 o versione successiva. Nella sezione [Obiettivi di scalabilità e prestazioni di Archiviazione Premium](#premium-storage-scalability-and-performance-targets.md)sono presenti informazioni sui limiti dell'account di archiviazione Premium.

**Archiviazione con ridondanza locale Premium**: un account di archiviazione Premium supporta solo l'opzione di replica di archiviazione con ridondanza locale, ciò significa che mantiene tre copie dei dati in una sola area. Per considerazioni sulla replica geografica durante l'uso di Archiviazione Premium, vedere la sezione [Snapshot e Copia BLOB](#snapshots-and-copy-blob) di questo articolo.

Azure usa l'account di archiviazione come contenitore per i dischi non gestiti. Quando si crea una macchina virtuale di Azure serie DS, DSv2, GS o Fs con dischi non gestiti e si seleziona un account di archiviazione Premium, il sistema operativo e i dischi dati vengono archiviati in tale account.

## <a name="premium-storage-supported-vms"></a>Macchine virtuali supportate da Archiviazione Premium
Archiviazione Premium supporta le macchine virtuali serie DS, DSv2, GS e Fs. Con queste macchine virtuali è possibile usare dischi di archiviazione sia Standard che Premium. Non è possibile usare i dischi di archiviazione Premium con le macchine virtuali di serie non compatibili con Archiviazione Premium.

Per informazioni sulle dimensioni e i tipi di VM di Azure disponibili, vedere [Dimensioni della macchina virtuale di Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Per informazioni sulle dimensioni e i tipi di VM per Linux, vedere [Dimensioni della macchina virtuale di Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Di seguito sono indicate alcune funzionalità delle macchine virtuali serie DS, DSv2, GS e Fs:

**Servizio cloud**: le macchine virtuali della serie DS possono essere aggiunte a un servizio cloud che include solo macchine virtuali della serie DS. Non aggiungere macchine virtuali della serie DS a un servizio cloud esistente che include macchine virtuali di una serie diversa. È possibile eseguire la migrazione dei dischi rigidi virtuali esistenti in un nuovo servizio cloud che esegue solo macchine virtuali della serie DS. Se si vuole mantenere lo stesso indirizzo IP virtuale (VIP) per il nuovo servizio cloud che ospita le VM della serie DS, usare gli [indirizzi IP riservati](../virtual-network/virtual-networks-instance-level-public-ip.md). È possibile aggiungere le macchine virtuali serie GS a un servizio cloud esistente che esegue solo macchine virtuali serie GS.

**Disco del sistema operativo**: è possibile configurare le macchine virtuali eseguibili in Archiviazione Premium per l'uso di un disco del sistema operativo Standard o Premium. Per un'esperienza ottimale è consigliabile usare un disco del sistema operativo basato su Archiviazione Premium.

**Dischi dati**: è possibile usare dischi di archiviazione sia Premium che Standard nella stessa macchina virtuale in esecuzione in Archiviazione Premium. Con Archiviazione Premium è possibile effettuare il provisioning di una macchina virtuale e collegare più dischi dati persistenti alla macchina virtuale. Se necessario, è possibile eseguire lo striping dei dischi per aumentare la capacità e le prestazioni del volume.

> [!NOTE]
> Se si esegue lo striping dei dischi dati di Archiviazione Premium tramite [Spazi di archiviazione](http://technet.microsoft.com/library/hh831739.aspx), è necessario configurarlo con una colonna per ogni disco usato. In caso contrario, le prestazioni complessive del volume in cui è stato eseguito lo striping possono essere inferiori al previsto a causa di una distribuzione non uniforme del traffico di dati da un disco a un altro. Per impostazione predefinita, l'interfaccia utente di Server Manager consente di configurare colonne per un massimo di 8 dischi. Se i dischi da gestire sono più di 8, è necessario usare PowerShell per creare il volume e specificare manualmente il numero di colonne. In caso contrario, l'interfaccia utente di Server Manager continuerà a usare 8 colonne anche se il numero di dischi è maggiore. Ad esempio, se si desidera gestire 32 dischi in un unico striping, è necessario specificare 32 colonne. È possibile utilizzare il parametro *NumberOfColumns* del cmdlet di PowerShell [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) per specificare il numero di colonne usate dal disco virtuale. Per altre informazioni, vedere [Panoramica sugli spazi di archiviazione](http://technet.microsoft.com/library/hh831739.aspx) e [Domande frequenti sugli spazi di archiviazione](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
> 

**Cache**: le macchine virtuali delle serie che supportano Archiviazione Premium hanno una specifica capacità di memorizzazione nella cache che permette di ottenere livelli elevati di velocità effettiva e latenza, superiori rispetto alle prestazioni del disco di archiviazione Premium sottostante. È possibile configurare i criteri di memorizzazione nella cache del disco per i dischi di archiviazione Premium come ReadOnly, ReadWrite o None. Il criterio predefinito di memorizzazione nella cache su disco è ReadOnly per tutti i dischi di dati Premium e ReadWrite per i dischi del sistema operativo. Usare l'impostazione di configurazione corretta per ottenere prestazioni ottimali per l'applicazione. Ad esempio, per dischi dati con numero elevato di letture o di sola lettura, come i file di dati di SQL Server, impostare i criteri di memorizzazione nella cache del disco su "ReadOnly". Per i dischi dati con numero elevato di scritture o di sola scrittura, come i file di log di SQL Server, impostare i criteri di memorizzazione nella cache del disco su "None". Per altre informazioni su come ottimizzare la progettazione con Archiviazione Premium, vedere [Progettare ai fini delle prestazioni con Archiviazione Premium](storage-premium-storage-performance.md).

**Analisi**: per analizzare le prestazioni delle macchine virtuali usando i dischi in Archiviazione Premium, è possibile abilitare la diagnostica della macchina virtuale nel [portale di Azure](https://portal.azure.com). Per informazioni dettagliate, vedere [Microsoft Azure Virtual Machine Monitoring with Azure Diagnostics Extension](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) (Monitoraggio di macchine virtuali di Microsoft Azure con l'estensione Diagnostica di Azure). Per visualizzare le prestazioni del disco, usare strumenti basati sul sistema operativo, ad esempio [Monitoraggio prestazioni di Windows](https://technet.microsoft.com/library/cc749249.aspx) per le macchine virtuali Windows e [IOSTAT](http://linux.die.net/man/1/iostat) per le macchine virtuali Linux.

**Prestazioni e limiti di scalabilità delle macchine virtuali**: ogni dimensione di macchina virtuale supportata da Archiviazione Premium presenta limiti di scalabilità e specifiche di prestazioni in termini di IOPS, larghezza di banda e numero di dischi che è possibile collegare a ogni macchina virtuale. Quando si usano dischi di archiviazione Premium con macchine virtuali, assicurarsi che IOPS e larghezza di banda disponibili nella macchina virtuale siano sufficienti per gestire il traffico dei dischi.
Ad esempio, una macchina virtuale STANDARD_DS1 ha un larghezza di banda dedicata di 32 MB/s disponibile per il traffico nei dischi di archiviazione Premium. Un disco di archiviazione Premium P10 può offrire una larghezza di banda di 100 MB/s. Se si collega un disco di archiviazione Premium P10 a tale una macchina virtuale, questa può raggiungere al massimo 32 MB/s, ma non i 100 MB/s offerti dal disco P10.

Attualmente, la macchina virtuale di dimensioni maggiori della serie DS è Standard_DS15_v2, che può offrire fino a 960 MB/s in tutti i dischi. La macchina virtuale di dimensioni maggiori della serie GS è Standard_GS5, che può offrire fino a 2000 MB/s in tutti i dischi.
Si noti che questi limiti riguardano solo il traffico su disco e non includono riscontri nella cache e traffico di rete. Esiste una larghezza di banda separata per il traffico di rete delle macchine virtuali che è diversa dalla larghezza di banda dedicata per i dischi di archiviazione Premium.

Per le informazioni più aggiornate sul numero massimo di IOPS e sulla velocità effettiva (larghezza di banda) massima per le macchine virtuali supportate da Archiviazione Premium, vedere [Dimensioni per le macchine virtuali Windows in Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Dimensioni delle macchine virtuali Linux in Azure](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per informazioni sui dischi di archiviazione Premium e sui relativi limiti per IOPS e velocità effettiva, vedere la tabella disponibile nella sezione [Obiettivi di scalabilità e prestazioni di Archiviazione Premium](#premium-storage-scalability-and-performance-targets) di questo articolo.

## <a name="premium-storage-scalability-and-performance-targets"></a>Obiettivi di scalabilità e prestazioni di Archiviazione Premium
Questa sezione descrive tutti gli obiettivi di scalabilità e prestazioni da considerare quando si usa Archiviazione Premium.

Obiettivi di scalabilità degli account di archiviazione Premium:

| Capacità account totale | Larghezza di banda totale per un account di archiviazione con ridondanza locale |
| --- | --- | 
| Capacità disco: 35 TB <br>Capacità snapshot: 10 TB | Fino a 50 gigabit al secondo per dati in ingresso e in uscita |

* Si intendono in ingresso tutti i dati (richieste) inviati a un account di archiviazione.
* Si intendono in uscita tutti i dati (risposte) ricevuti da un account di archiviazione.

Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md).

Se si usano account di archiviazione Premium per dischi non gestiti e l'applicazione supera gli obiettivi di scalabilità di un singolo account di archiviazione, è consigliabile prendere in considerazione la migrazione a Managed Disks. Se non si vuole eseguire la migrazione a Managed Disks, compilare l'applicazione per l'uso di più account di archiviazione e partizionare i dati tra tali account di archiviazione. Ad esempio, per collegare dischi per 51 TB tra varie macchine virtuali, è necessario distribuirli in due account di archiviazione. Questo perché il limite per un singolo account di archiviazione Premium è di 35 TB. Accertarsi che i dischi di cui viene effettuato il provisioning in un singolo account di archiviazione Premium non superino mai i 35 TB.

### <a name="premium-storage-disk-limits"></a>Limiti dei dischi di archiviazione Premium
Quando si effettua il provisioning di un disco di archiviazione Premium, la dimensione del disco determina il livello massimo di IOPS e velocità effettiva (larghezza di banda). I dischi di archiviazione Premium sono di tre tipi: P10, P20 e P30. Ognuno è caratterizzato da limiti specifici relativi alle operazioni di IOPS e alla velocità effettiva, come specificato nella tabella seguente:

|Tipo di disco di Archiviazione Premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Dimensione disco | 128 GiB | 512 GiB | 1024 GiB (1 TB) |
| IOPS per disco | 500 | 2300 | 5000 |
Velocità effettiva per disco | 100 MB/s | 150 MB/s | 200 MB/s |

> [!NOTE]
> Assicurarsi che la larghezza di banda della macchina virtuale sia sufficiente a gestire il traffico su disco, come illustrato nella sezione [Macchine virtuali supportate da Archiviazione Premium](#premium-storage-supported-vms) riportata in precedenza in questo articolo. In caso contrario, gli IOPS e la velocità effettiva del disco saranno limitati a valori inferiori in base ai limiti della macchina virtuale piuttosto che in base ai limiti del disco citati nella tabella precedente.  
> 
> 

Ecco alcuni aspetti importanti per quanto riguarda gli obiettivi di scalabilità e prestazioni di Archiviazione Premium:

* **Capacità di cui è stato effettuato il provisioning e prestazioni**: quando si effettua il provisioning di un disco di archiviazione Premium, a differenza di Archiviazione Standard, vengono garantiti livelli di capacità, IOPS e velocità effettiva per il disco. Se ad esempio si crea un disco P30, Azure effettua il provisioning di 1024 GB di capacità di archiviazione, 5000 IOPS e 200 MB/s di velocità effettiva per tale disco. L'applicazione può usare la totalità o una della capacità e delle prestazioni.

* **Dimensione disco**: Azure associa la dimensione del disco, arrotondata per eccesso, all'opzione relativa al disco di archiviazione Premium più vicina, come specificato nella tabella. Ad esempio, un disco da 100 GiB viene classificato come opzione P10 e può eseguire fino a 500 IOPS a una velocità effettiva massima di 100 MB/s. Analogamente, un disco da 400 GiB viene classificato come opzione P20 e può eseguire fino a 2300 IOPS a una velocità effettiva di 150 MB/s.
  
> [!NOTE]
> È possibile aumentare facilmente le dimensioni dei dischi esistenti. Ad esempio, è possibile aumentare le dimensioni di un disco da 30 GB fino a 128 GB o persino a 1 TB. Oppure è possibile convertire un disco P20 in un disco P30 perché si necessita di una capacità maggiore o di IOPS e velocità effettiva più elevati. 
> 
 
* **Dimensioni I/O**: le dimensioni dell'unità di input/output (I/O) sono di 256 KB. Se il volume dei dati è inferiore a 256 KB, viene considerata una singola unità di I/O. Le dimensioni di I/O superiori vengono calcolate come più I/O da 256 KB. Ad esempio, 1.100 KB di I/O corrispondono a cinque unità di I/O.

* **Velocità effettiva**: il limite della velocità effettiva include scritture su disco e letture dal disco che non vengono servite dalla cache. Ad esempio, un disco P10 ha una velocità effettiva di 100 MB/s per disco. Di seguito sono indicati alcuni esempi di velocità effettiva per il disco P10.

| Velocità effettiva massima per disco P10 | Letture non cache dal disco | Scritture non cache nel disco |
| --- | --- | --- |
| 100 MB al secondo | 100 MB al secondo | 0 |
| 100 MB al secondo | 0 | 100 MB al secondo |
| 100 MB al secondo | 60 MB al secondo | 40 MB al secondo |

* **Riscontri nella cache**: i riscontri nella cache non sono limitati dalla velocità effettiva o dalle operazioni di I/O al secondo allocate del disco. Ad esempio, quando si usa un disco dati con l'impostazione della cache ReadOnly in una macchina virtuale supportata da Archiviazione Premium, le letture gestite dalla cache non sono soggette ai limiti di IOPS e di velocità effettiva del disco. Di conseguenza, è possibile ottenere una velocità effettiva molto elevata da un disco se il carico di lavoro è prevalentemente composto da letture. Si noti che la cache è soggetta a limiti di velocità effettiva e IOPS separati a livello della macchina virtuale, in base alle dimensioni della stessa. Le macchine virtuali della serie DS hanno circa 4000 IOPS e 33 MB/sec per core per la cache e gli IO dell’unità SSD locale. Le macchine virtuali della serie GS hanno un limite di 5000 IOPS e 50 MB/sec per core per la cache e gli I/O dell’unità SSD locale. 

## <a name="throttling"></a>Limitazione
È possibile che si verifichi una limitazione se la velocità effettiva o le operazioni di I/O al secondo dell'applicazione superano i limiti allocati per un disco di archiviazione Premium oppure se il traffico su disco totale in tutti i dischi della macchina virtuale supera il limite della larghezza di banda del disco disponibile per la macchina virtuale. Per evitare la limitazione, è consigliabile limitare il numero di richieste di I/O in attesa per il disco in base agli obiettivi di scalabilità e prestazioni per il disco di cui è stato effettuato il provisioning e in base alla larghezza di banda del disco disponibile per la macchina virtuale.  

L'applicazione otterrà la latenza più bassa quando viene progettata in modo da evitare la limitazione. D'altra parte, se il numero di richieste di I/O in sospeso per il disco è troppo basso, l'applicazione non potrà sfruttare i livelli massimi di IOPS e velocità effettiva disponibili per il disco.

Gli esempi seguenti illustrano come calcolare i livelli di limitazione. Tutti i calcoli sono basati sulle dimensioni dell'unità di I/O pari a 256 KB:

### <a name="example-1"></a>Esempio 1:
L'applicazione ha eseguito 495 unità di I/O da 16 KB in un disco P10. Queste saranno conteggiate come 495 unità di I/O al secondo (IOPS). Se si tenta un I/O di 2 MB nello stesso secondo, il totale di unità di I/O è uguale a 495 + 8. Il motivo è che 2 MB di I/O corrispondono a 2048 KB / 256 KB = 8 unità di I/O quando le dimensioni dell'unità di I/O sono pari a 256 KB. Poiché la somma di 495 + 8 supera il limite di 500 IOPS per il disco, entra in funzione la limitazione.

### <a name="example-2"></a>Esempio 2
L'applicazione ha eseguito 400 unità di I/O da 256 KB in un disco P10. La larghezza di banda totale utilizzata è (400 * 256) / 1024 = 100 MB/sec. Il limite di velocità effettiva di un disco P10 è 100 MB al secondo. Se l'applicazione tenta di eseguire altre operazioni di I/O nello stesso secondo verrà limitata, in quanto supera il limite allocato.

### <a name="example-3"></a>Esempio 3:
Si dispone di una macchina virtuale DS4 con due dischi P30 collegati. Ogni disco P30 è in grado di supportare una velocità effettiva pari a 200 MB al secondo. Tuttavia, una macchina virtuale DS4 ha una capacità di larghezza di banda su disco totale pari a 256 MB al secondo. Pertanto, non è possibile guidare i dischi collegati alla velocità effettiva massima della macchina virtuale DS4 allo stesso tempo. Per risolvere questo problema, è possibile sostenere un traffico di 200 MB al secondo su un disco e di 56 MB al secondo sull'altro disco. Se la somma del traffico sui dischi è superiore a 256 MB al secondo, il traffico su disco viene limitato.

> [!NOTE]
> Se il traffico su disco è costituito principalmente da I/O di dimensioni ridotte, è estremamente probabile che l'applicazione raggiunga il limite di IOPS prima del limite di velocità effettiva. Al contrario, se il traffico su disco è costituito principalmente da I/O di grandi dimensioni, è molto probabile che l'applicazione raggiunga il limite di velocità effettiva anziché il limite di IOPS. Per massimizzare la capacità di IOPS e velocità effettiva dell'applicazione, usare dimensioni di I/O ottimali e limitare il numero delle richieste di I/O in sospeso per il disco.
> 

Per informazioni sulla progettazione per prestazioni elevate usando Archiviazione Premium, leggere l'articolo [Progettare ai fini delle prestazioni con Archiviazione Premium](storage-premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Snapshot e copia di BLOB

Per il servizio Archiviazione, il file VHD è un BLOB di pagine. È possibile creare snapshot dei BLOB di pagine e copiarli in un'altra posizione, ad esempio un diverso account di archiviazione.

### <a name="unmanaged-disks"></a>Dischi non gestiti

È possibile creare [snapshot incrementali](storage-incremental-snapshots.md) dei dischi Premium non gestiti nello stesso modo in cui si usano gli snapshot con Archiviazione Standard. Poiché Archiviazione Premium supporta solo l'archiviazione con ridondanza locale come opzione di replica, è consigliabile creare gli snapshot e quindi copiarli in un account di Archiviazione Standard con ridondanza geografica. Per altre informazioni, vedere [Opzioni di ridondanza di Archiviazione di Azure](storage-redundancy.md).

Se un disco è collegato a una macchina virtuale, alcune operazioni API non sono consentite sui dischi. Ad esempio, non è possibile eseguire un'operazione [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob) sul BLOB finché il disco è collegato a una macchina virtuale. Al contrario, è necessario creare innanzitutto uno snapshot del BLOB usando il metodo dell'API REST [Snapshot Blob](/rest/api/storageservices/fileservices/Snapshot-Blob), quindi eseguire l'operazione [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob) dello snapshot per copiare il disco collegato. In alternativa, è possibile scollegare il disco e quindi eseguire le operazioni necessarie.

I limiti seguenti si applicano agli snapshot di BLOB di Archiviazione Premium:

| Limite di Archiviazione Premium | Valore |
| --- | --- |
| Max. numero di snapshot per BLOB | 100 |
| Capacità dell'account di archiviazione per gli snapshot (include solo i dati negli snapshot e non i dati nel BLOB di base) | 10 TB |
| Min tempo tra snapshot consecutivi | 10 minuti |

Per conservare copie con ridondanza geografica degli snapshot, è possibile copiare gli snapshot da un account di archiviazione Premium a un account di archiviazione Standard con ridondanza geografica mediante AzCopy o Copy Blob.. Per altre informazioni, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md) e [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob).

Per informazioni dettagliate sull'esecuzione di operazioni REST sui BLOB di pagine negli account di archiviazione Premium, vedere [Using Blob Service Operations with Azure Premium Storage](http://go.microsoft.com/fwlink/?LinkId=521969) (Uso delle operazioni del servizio BLOB con Archiviazione Premium di Azure).

### <a name="managed-disks"></a>Dischi gestiti

Uno snapshot di un disco gestito è una copia di sola lettura del disco gestito archiviata come disco gestito Standard. Gli [snapshot incrementali](storage-incremental-snapshots.md) non sono attualmente supportati per Managed Disks, ma lo saranno in futuro. Per informazioni su come creare uno snapshot per un disco gestito, vedere gli articoli relativi alla creazione di una copia di un disco rigido virtuale archiviato come disco gestito di Azure usando snapshot gestiti [in Windows](../virtual-machines/virtual-machines-windows-snapshot-copy-managed-disk.md) o [in Linux](../virtual-machines/linux/virtual-machines-linux-snapshot-copy-managed-disk.md).

Se un disco gestito è collegato a una macchina virtuale, alcune operazioni API non sono consentite sui dischi. Ad esempio, non è possibile generare una firma di accesso condiviso per eseguire un'operazione di copia mentre il disco è collegato a una macchina virtuale. Creare invece prima di tutto uno snapshot del disco e quindi eseguire la copia dello snapshot. In alternativa, è possibile scollegare il disco e quindi generare una firma di accesso condiviso per eseguire l'operazione di copia.


## <a name="using-linux-vms-with-premium-storage"></a>Uso di macchine virtuali Linux con Archiviazione Premium
Per configurare macchine virtuali Linux in Archiviazione Premium, vedere le istruzioni importanti riportate di seguito:

* Per tutti i dischi di archiviazione Premium con cache impostata su ReadOnly o None, è necessario disabilitare le "barriere" in fase di montaggio del file system per raggiungere gli obiettivi di scalabilità per Archiviazione Premium. Non sono necessarie barriere per questo scenario perché le scritture relative ai dischi di archiviazione Premium sono durevoli per queste impostazioni della cache. Quando la richiesta di scrittura viene completata in modo corretto, i dati sono stati scritti nell'archivio persistente. Per disabilitare le barriere, scegliere tra i metodi seguenti il più adatto per il file system usato:
  
* Se si usa **reiserFS**, disabilitare le barriere tramite l'opzione di montaggio "barrier=none" Per abilitarle, utilizzare "barrier=flush".
* Se si usa **ext3/ext4**, disabilitare le barriere tramite l'opzione di montaggio "barrier=0" Per abilitarle, utilizzare "barrier=1".
* Se si usa **XFS**, disabilitare le barriere tramite l'opzione di montaggio "nobarrier" Per abilitarle, utilizzare "barrier".
* Per i dischi di archiviazione Premium con cache impostata su ReadWrite, è necessario abilitare le barriere per garantire la durabilità delle scritture.
* Per far sì che le etichette di volume restino dopo il riavvio della macchina virtuale, è necessario aggiornare /etc/fstab con i riferimenti UUID ai dischi. Vedere anche [Aggiungere un disco a una VM Linux](../virtual-machines/virtual-machines-linux-add-disk.md).

Di seguito sono riportate le distribuzioni di Linux convalidate con Archiviazione Premium. Si consiglia di aggiornare le macchine virtuali ad almeno una di queste versioni (o successive) per prestazioni e stabilità migliori con Archiviazione Premium. Per alcune versioni è necessaria la versione più recente di Linux Integration Services (LIS)&4;.0 per Microsoft Azure. Usare il collegamento indicato di seguito per il download e l'installazione. Continueremo ad aggiungere altre immagini all'elenco non appena effettueremo ulteriori convalide. Si noti che le nostre convalide hanno mostrato che le prestazioni variano per queste immagini e che ciò dipende, inoltre, dalle impostazioni e dalle caratteristiche dei carichi di lavoro sulle immagini. Immagini diverse sono ottimizzate per tipi di carico di lavoro diversi.

| Distribuzione | Versione | Kernel supportato | Dettagli |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 richiesto](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Vedere la nota seguente* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 consigliato](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Vedere la nota seguente* |
| RHEL | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 o RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 or RHCK con [LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 or RHCK con [LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Driver LIS per Openlogic CentOS

I clienti che eseguono le macchine virtuali OpenLogic CentOS devono eseguire il comando seguente per installare i driver più recenti:

```
sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
sudo yum install microsoft-hyper-v
```

Sarà quindi necessario riavviare il computer per attivare i nuovi driver.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Quando si usa il servizio di archiviazione Premium, tenere conto delle considerazioni seguenti relative alla fatturazione:

* Dimensione del BLOB o del disco di archiviazione Premium
* Snapshot di Archiviazione Premium
* Trasferimenti di dati in uscita

**Dimensione del BLOB o del disco di archiviazione Premium**: la fatturazione per un BLOB o un disco di archiviazione Premium dipende dalla dimensione del BLOB o del disco di cui è stato effettuato il provisioning. Azure associa la dimensione del disco di provisioning, arrotondata per eccesso, all'opzione relativa al disco di archiviazione Premium più vicina, come specificato nella sezione [Obiettivi di scalabilità e prestazioni di Archiviazione Premium](#premium-storage-scalability-and-performance-targets). Viene eseguito il mapping di ogni disco a una delle dimensioni di provisioning supportate e viene eseguita la relativa fatturazione. La fatturazione per qualsiasi disco di cui sia stato effettuato il provisioning viene ripartita in modo proporzionale in base alle ore usando il prezzo mensile dell'offerta di Archiviazione Premium. Ad esempio, se è stato effettuato il provisioning di un disco P10 e lo si è eliminato dopo 20 ore verranno fatturate 20 ore per l'offerta P10, indipendentemente dalla quantità di dati effettivamente scritti sul disco, dalle operazioni IOPS eseguite o dalla velocità effettiva usata.

**Snapshot di dischi di archiviazione Premium**: gli snapshot nei dischi di archiviazione Premium vengono fatturati in base alla capacità aggiuntiva usata allo scopo. Per informazioni sugli snapshot, vedere [Creazione di uno snapshot di un BLOB](/rest/api/storageservices/fileservices/Snapshot-Blob).

**Snapshot di dischi gestiti Premium**: uno snapshot di un disco gestito è una copia di sola lettura del disco archiviata come disco gestito Standard. Il costo di uno snapshot corrisponde a quello di un disco gestito Standard. Ad esempio, se si acquisisce uno snapshot di un disco gestito Premium da 128 GB, il costo dello snapshot sarà equivalente a quello di un disco gestito Standard da 128 GB.  

**Trasferimenti di dati in uscita**: i [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/data-transfers/) (dati in uscita dai data center di Azure) vengono fatturati in base all'uso della larghezza di banda.

Per informazioni dettagliate sui prezzi di Archiviazione Premium, sulle macchine virtuali supportate da Archiviazione Premium e su Managed Disks, vedere quanto segue:

* [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Prezzi di Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-service-support"></a>Supporto del servizio Backup di Azure 

È possibile eseguire il backup delle macchine virtuali con dischi non gestiti con Backup di Azure. [Altre informazioni](../backup/backup-azure-vms-first-look-arm.md).

È anche possibile usare il servizio Backup di Azure con Managed Disks per creare un processo di backup con backup pianificati, facile ripristino delle macchine virtuali e criteri di conservazione dei backup. Per altre informazioni in merito, vedere la sezione relativa all'[uso del servizio Backup di Azure per macchine virtuali con dischi gestiti](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Archiviazione Premium di Azure, vedere gli articoli seguenti.

### <a name="design-and-implement-with-azure-premium-storage"></a>Progettare e implementare con Archiviazione Premium di Azure
* [Progettare ai fini delle prestazioni con Archiviazione Premium](storage-premium-storage-performance.md)
* [Uso delle operazioni del servizio BLOB con Archiviazione Premium di Azure](http://go.microsoft.com/fwlink/?LinkId=521969)

### <a name="operational-guidance"></a>Informazioni operative
* [Migrazione ad Archiviazione Premium di Azure](storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>Post di blog
* [Archiviazione Premium di Azure disponibile a livello generale](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
* [Annuncio della serie GS: aggiunta del supporto di archiviazione Premium per le macchine virtuali di grandi dimensioni nel cloud pubblico](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)


<!--HONumber=Feb17_HO2-->


