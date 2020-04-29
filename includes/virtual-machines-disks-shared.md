---
title: includere il file
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c3e5beaef7fcc9d407103834e2040957ff32984c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008542"
---
Azure Shared Disks (anteprima) è una nuova funzionalità di Azure Managed disks che consente di aggiungere simultaneamente un disco gestito a più macchine virtuali (VM). Il fissaggio di un disco gestito a più macchine virtuali consente di distribuire le applicazioni in cluster nuove o migrate in Azure.

## <a name="how-it-works"></a>Come funziona

Le macchine virtuali nel cluster possono leggere o scrivere sul disco collegato in base alla prenotazione scelta dall'applicazione in cluster usando le [prenotazioni permanenti SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). La richiesta pull SCSI è uno standard di settore sfruttato da applicazioni in esecuzione su SAN (Storage Area Network) in locale. L'abilitazione della richiesta pull SCSI in un disco gestito consente di eseguire la migrazione di queste applicazioni in Azure così come sono.

La condivisione di dischi gestiti offre un'archiviazione a blocchi condivisa a cui è possibile accedere da più macchine virtuali, che vengono esposte come numeri di unità logica (lun). I LUN vengono quindi presentati a un iniziatore (macchina virtuale) da una destinazione (disco). Questi LUN hanno un aspetto simile all'archiviazione diretta (DAS) o a un'unità locale per la macchina virtuale.

I dischi gestiti condivisi non offrono in modo nativo un file system completamente gestito a cui è possibile accedere tramite SMB/NFS. È necessario usare un gestore di cluster, ad esempio Windows Server failover cluster (WSFC) o pacemaker, che gestisce la comunicazione tra i nodi del cluster e il blocco di scrittura.

## <a name="limitations"></a>Limitazioni

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Dimensione disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Carichi di lavoro di esempio

### <a name="windows"></a>Windows

La maggior parte del clustering basato su Windows si basa su WSFC, che gestisce tutta l'infrastruttura di base per la comunicazione del nodo cluster, consentendo alle applicazioni di sfruttare i vantaggi dei modelli di accesso parallelo. WSFC Abilita le opzioni CSV e non basate su CSV a seconda della versione di Windows Server in uso. Per informazioni dettagliate, vedere [creare un cluster di failover](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Alcune applicazioni diffuse in esecuzione su WSFC includono:

- Istanze del cluster di failover di SQL Server (FCI)
- File server di scalabilità orizzontale (SoFS)
- File server per uso generale (carico di lavoro IW)
- Disco del profilo utente di Desktop remoto server (RDS UPD)
- ASC/SCS SAP

### <a name="linux"></a>Linux

I cluster Linux possono sfruttare i gestori di cluster come [pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker si basa su [Corosync](http://corosync.github.io/corosync/), abilitando le comunicazioni del cluster per le applicazioni distribuite in ambienti a disponibilità elevata. Alcuni filesystem in cluster comuni includono [OCFS2](https://oss.oracle.com/projects/ocfs2/) e [GFS2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). È possibile modificare prenotazioni e registrazioni utilizzando utilità quali [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) e [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Flusso di prenotazione permanente

Il diagramma seguente illustra un'applicazione di database in cluster a 2 nodi di esempio che sfrutta la richiesta pull SCSI per abilitare il failover da un nodo all'altro.

![Cluster a due nodi. Un'applicazione in esecuzione nel cluster gestisce l'accesso al disco](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Il flusso è il seguente:

1. L'applicazione in cluster in esecuzione sia in Azure VM1 che in VM2 registra la finalità di lettura o scrittura sul disco.
1. L'istanza dell'applicazione in VM1 accetta quindi la prenotazione esclusiva per scrivere sul disco.
1. Questa prenotazione viene applicata sul disco di Azure e il database ora può scrivere esclusivamente sul disco. Eventuali scritture dall'istanza dell'applicazione in VM2 non riusciranno.
1. Se l'istanza dell'applicazione in VM1 diventa inattiva, l'istanza in VM2 può ora avviare un failover del database ed eseguire il commit del disco.
1. Questa prenotazione viene ora applicata sul disco di Azure e il disco non accetterà più le Scritture da VM1. Accetterà solo le Scritture da VM2.
1. L'applicazione in cluster può completare il failover del database e gestire le richieste da VM2.

Il diagramma seguente illustra un altro carico di lavoro cluster comune costituito da più nodi che leggono dati dal disco per l'esecuzione di processi paralleli, ad esempio il training di modelli di machine learning.

![Cluster di macchine virtuali a quattro nodi, ogni nodo registra la finalità di scrittura, l'applicazione richiede una prenotazione esclusiva per gestire correttamente i risultati della scrittura](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Il flusso è il seguente:

1. L'applicazione in cluster in esecuzione su tutte le macchine virtuali registra la finalità di lettura o scrittura sul disco.
1. L'istanza dell'applicazione in VM1 accetta una prenotazione esclusiva per scrivere sul disco durante l'apertura delle letture sul disco da altre macchine virtuali.
1. Questa prenotazione viene applicata nel disco di Azure.
1. Tutti i nodi del cluster possono ora leggere dal disco. Solo un nodo scrive i risultati sul disco, per conto di tutti i nodi del cluster.

### <a name="ultra-disks-reservation-flow"></a>Flusso di prenotazione di dischi ultra

I dischi Ultra offrono una limitazione aggiuntiva, per un totale di due limitazioni. Per questo motivo, il flusso di prenotazione del disco Ultra può funzionare come descritto nella sezione precedente oppure può limitare e distribuire le prestazioni in modo più granulare.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>Limitazioni delle prestazioni del disco Ultra

I dischi Ultra offrono la funzionalità univoca che consente di impostare le prestazioni esponendo attributi modificabili e consentendo di modificarli. Per impostazione predefinita, sono disponibili solo due attributi modificabili, ma i dischi Ultra condivisi hanno due attributi aggiuntivi.


|Attributo  |Descrizione  |
|---------|---------|
|DiskIOPSReadWrite     |Numero totale di operazioni di i/o al secondo consentite in tutte le VM montaggio del disco condiviso con accesso in scrittura.         |
|DiskMBpsReadWrite     |Velocità effettiva totale (MB/s) consentita per tutte le macchine virtuali che montano il disco condiviso con accesso in scrittura.         |
|DiskIOPSReadOnly*     |Numero totale di operazioni di i/o al secondo consentite in tutte le VM montaggio del disco condiviso come ReadOnly.         |
|DiskMBpsReadOnly*     |Velocità effettiva totale (MB/s) consentita per tutte le macchine virtuali che montano il disco condiviso come ReadOnly.         |

\*Si applica solo a dischi Ultra condivisi

Le formule seguenti illustrano come è possibile impostare gli attributi delle prestazioni, dal momento che sono modificabili dall'utente:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - Limiti di IOPS di 300 IOPS/GiB, fino a un massimo di 160K IOPS per disco
    - Almeno 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly è almeno 2 IOPS/GiB
- DiskMBpsRead Write/DiskMBpsReadOnly:
    - Il limite di velocità effettiva di un singolo disco è 256 KiB/s per ogni IOPS con provisioning, fino a un massimo di 2000 MBps per disco
    - La velocità effettiva minima garantita per ogni disco è 4KiB/s per ogni IOPS di cui è stato effettuato il provisioning, con una linea di base complessiva minima di 1 MBps

### <a name="examples"></a>Esempi

Gli esempi seguenti illustrano alcuni scenari che mostrano come la limitazione può funzionare con i dischi Ultra condivisi, in particolare.

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Cluster a due nodi con volumi condivisi cluster

Di seguito è riportato un esempio di WSFC a 2 nodi tramite volumi condivisi cluster. Con questa configurazione, entrambe le macchine virtuali hanno accesso in scrittura simultaneo al disco, il che comporta la suddivisione della limitazione ReadWrite tra le due macchine virtuali e la limitazione di sola lettura non in uso.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="Esempio di ultra nodo a due nodi CSV":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>Cluster a due nodi senza volumi condivisi cluster

Di seguito è riportato un esempio di WSFC a 2 nodi che non usa volumi condivisi cluster. Con questa configurazione, solo una macchina virtuale ha accesso in scrittura al disco. In questo modo la limitazione ReadWrite viene utilizzata esclusivamente per la macchina virtuale primaria e la limitazione ReadOnly viene utilizzata solo dal database secondario.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="Due nodi CSV senza esempio di disco rigido CSV":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>Cluster Linux a quattro nodi

Di seguito è riportato un esempio di un cluster Linux a 4 nodi con un solo Writer e tre lettori con scalabilità orizzontale. Con questa configurazione, solo una macchina virtuale ha accesso in scrittura al disco. In questo modo, la limitazione ReadWrite viene utilizzata esclusivamente per la macchina virtuale primaria e la limitazione ReadOnly è divisa dalle VM secondarie.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Esempio di limitazione ultra a quattro nodi":::

:::image-end:::