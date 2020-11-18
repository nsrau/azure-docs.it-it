---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7831eaaa478a3f28ff158d5c9599abaf8a107c15
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684432"
---
Azure Shared disks è una nuova funzionalità di Azure Managed disks che consente di aggiungere contemporaneamente un disco gestito a più macchine virtuali (VM). Collegando un disco gestito a più macchine virtuali è possibile distribuire nuove applicazioni in cluster o eseguire la migrazione di quelle esistenti in Azure.

## <a name="how-it-works"></a>Funzionamento

Le macchine virtuali nel cluster possono leggere o scrivere sul disco collegato in base alla prenotazione scelta dall'applicazione in cluster usando le [prenotazioni permanenti SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). Una prenotazione permanente SCSI è uno standard del settore usato dalle applicazioni in esecuzione in una rete di archiviazione (SAN) locale. L'abilitazione delle prenotazioni permanenti SCSI in un disco gestito consente di eseguire la migrazione di queste applicazioni in Azure così come sono.

I dischi gestiti condivisi offrono l'archiviazione a blocchi condivisa a cui è possibile accedere da più macchine virtuali, che vengono esposte come numeri di unità logica (lun). I LUN vengono quindi presentati a un iniziatore (macchina virtuale) da una destinazione (disco). Questi LUN hanno un aspetto simile all'archiviazione collegata direttamente (DAS) o a un'unità locale per la macchina virtuale.

I dischi gestiti condivisi non offrono in modo nativo un file system completamente gestito accessibile tramite SMB/NFS. È necessario usare un gestore di cluster, ad esempio Windows Server failover cluster (WSFC) o pacemaker, che gestisce la comunicazione del nodo del cluster e il blocco di scrittura.

## <a name="limitations"></a>Limitazioni

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

### <a name="operating-system-requirements"></a>Requisiti del sistema operativo

I dischi condivisi supportano diversi sistemi operativi. Vedere le sezioni [Windows](#windows) o [Linux](#linux) per i sistemi operativi supportati.

## <a name="disk-sizes"></a>Dimensione disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Carichi di lavoro di esempio

### <a name="windows"></a>Windows

I dischi condivisi di Azure sono supportati in Windows Server 2008 e versioni successive. La maggior parte del clustering basato su Windows si basa su WSFC, che gestisce tutta l'infrastruttura di base per la comunicazione del nodo cluster, consentendo alle applicazioni di sfruttare i vantaggi dei modelli di accesso parallelo. WSFC abilita le opzioni basate e non su CSV a seconda della versione di Windows Server in uso. Per i dettagli, fare riferimento a [Creare un cluster di failover](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Tra le applicazioni più comuni in esecuzione su WSFC ci sono:

- [Creare un'istanza FCI con i dischi condivisi di Azure (SQL Server in macchine virtuali di Azure)](../articles/azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md)
- File server di scalabilità orizzontale (SoFS) [modello] (https://aka.ms/azure-shared-disk-sofs-template)
- SAP ASC/SCS [modello] (https://aka.ms/azure-shared-disk-sapacs-template)
- File server per uso generale (carico di lavoro IW)
- Disco del profilo utente di server desktop remoto (RDS UPD)

### <a name="linux"></a>Linux

I dischi condivisi di Azure sono supportati in:
- [SUSE SLE per SAP e SUSE SLE HA 15 SP1 e versioni successive](https://www.suse.com/c/azure-shared-disks-excercise-w-sles-for-sap-or-sle-ha/)
- [Ubuntu 18,04 e versioni successive](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874)
- [RHEL Developer Preview in qualsiasi versione RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/index?lb_target=production#azure-configuring-shared-block-storage_configuring-rhel-high-availability-on-azure)
- [Oracle Enterprise Linux](https://docs.oracle.com/en/operating-systems/oracle-linux/8/availability/hacluster-1.html)

I cluster Linux possono sfruttare gli strumenti di gestione cluster, ad esempio [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker si basa su [Corosync](http://corosync.github.io/corosync/), abilitando le comunicazioni del cluster per le applicazioni distribuite in ambienti a disponibilità elevata. Alcuni file system in cluster comuni includono [ocfs2](https://oss.oracle.com/projects/ocfs2/) e [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Per l'accesso arbitrali al disco è possibile usare modelli di clustering basati su SCSI Persistent Reservation (SCSI PR) e/o STONITH Block Device (SBD). Quando si usa la richiesta pull SCSI, è possibile modificare prenotazioni e registrazioni usando utilità quali [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) e [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Flusso di prenotazione permanente

Il diagramma seguente illustra un esempio di applicazione di database in cluster a 2 nodi che usa la prenotazione permanente SCSI per abilitare il failover da un nodo all'altro.

![Cluster a due nodi. Un'applicazione in esecuzione nel cluster gestisce l'accesso al disco](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Il flusso è il seguente:

1. L'applicazione in cluster in esecuzione sia nella macchina virtuale Azure VM1 che in VM2 registra il proprio intento di lettura o scrittura sul disco.
1. L'istanza dell'applicazione in VM1 effettua quindi la prenotazione esclusiva per scrivere sul disco.
1. Questa prenotazione viene applicata sul disco di Azure e il database può ora scrivere in modo esclusivo sul disco. Eventuali scritture dall'istanza dell'applicazione in VM2 non avranno esito positivo.
1. Se l'istanza dell'applicazione in VM1 diventa inattiva, l'istanza in VM2 può avviare un failover del database e subentrare sul disco.
1. Questa prenotazione viene ora applicata sul disco di Azure e il disco non accetterà più scritture da VM1, ma solo da VM2.
1. L'applicazione in cluster può completare il failover del database e gestire le richieste da VM2.

Il diagramma seguente illustra un altro carico di lavoro in cluster comune costituito da più nodi che leggono dati dal disco per l'esecuzione di processi paralleli, ad esempio il training di modelli di Machine Learning.

![Cluster di macchine virtuali a quattro nodi, in cui ogni nodo registra l'intento di scrittura e l'applicazione effettua una prenotazione esclusiva per gestire correttamente i risultati della scrittura](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Il flusso è il seguente:

1. L'applicazione in cluster in esecuzione in tutte le macchine virtuali registra l'intento di lettura o scrittura sul disco.
1. L'istanza dell'applicazione in VM1 effettua una prenotazione esclusiva per scrivere sul disco aprendo le letture sul disco da altre macchine virtuali.
1. Questa prenotazione viene applicata sul disco di Azure.
1. Tutti i nodi del cluster possono ora leggere dal disco. Solo un nodo riscrive i risultati sul disco, per conto di tutti i nodi del cluster.

### <a name="ultra-disks-reservation-flow"></a>Flusso di prenotazione di dischi Ultra

I dischi Ultra offrono un'ulteriore limitazione, per un totale di due limitazioni. Per questo motivo, il flusso di prenotazione di dischi Ultra può operare come descritto nella sezione precedente oppure può imporre una limitazione e distribuire le prestazioni in modo più granulare.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="Immagine di una tabella che rappresenta l'accesso &quot;ReadOnly&quot; o &quot;lettura/scrittura&quot; per il titolare della prenotazione, registrato e altro.":::

## <a name="performance-throttles"></a>Limitazioni delle prestazioni

### <a name="premium-ssd-performance-throttles"></a>Limitazione delle prestazioni SSD Premium

Con l'unità SSD Premium, i IOPS del disco e la velocità effettiva sono corretti, ad esempio, IOPS di un P30 è 5000. Questo valore rimane se il disco è condiviso tra due macchine virtuali o 5 macchine virtuali. I limiti del disco possono essere raggiunti da una singola macchina virtuale o divisi in due o più macchine virtuali. 

### <a name="ultra-disk-performance-throttles"></a>Limitazioni delle prestazioni dei dischi Ultra

I dischi Ultra offrono la funzionalità esclusiva di consentire l'impostazione delle prestazioni esponendo attributi modificabili e consentendo di modificarli. Per impostazione predefinita, ci sono solo due attributi modificabili, ma i dischi Ultra condivisi hanno due attributi aggiuntivi.


|Attributo  |Descrizione  |
|---------|---------|
|DiskIOPSReadWrite     |Numero totale di operazioni di I/O al secondo consentite in tutte le macchine virtuali che hanno montato il disco condiviso con accesso in scrittura.         |
|DiskMBpsReadWrite     |Velocità effettiva totale (MB/s) consentita in tutte le macchine virtuali che hanno montato il disco condiviso con accesso in scrittura.         |
|DiskIOPSReadOnly*     |Numero totale di operazioni di i/o `ReadOnly` al secondo consentite in tutte le VM che montano il disco condiviso         |
|DiskMBpsReadOnly*     |Velocità effettiva totale (MB/s) consentita in tutte le VM che montano il disco condiviso come `ReadOnly` .         |

\* Si applica solo a dischi Ultra condivisi

Le formule seguenti illustrano come è possibile impostare gli attributi delle prestazioni, dal momento che sono modificabili dall'utente:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - Limiti di operazioni di I/O al secondo pari a 300 IOPS/GiB, fino a un massimo di 160.000 IOPS per disco
    - Minimo 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly è almeno 2 IOPS/GiB
- DiskMBpsRead    Write/DiskMBpsReadOnly:
    - Il limite di velocità effettiva di un singolo disco è di 256 KiB/s per ogni operazione di I/O al secondo di cui è stato effettuato il provisioning, fino a un massimo di 2000 MBps per disco
    - La velocità effettiva minima garantita per disco è 4KiB/s per ogni operazione di I/O al secondo di cui è stato effettuato il provisioning, con una baseline complessiva minima di 1 MBps

#### <a name="examples"></a>Esempi

Gli esempi seguenti illustrano alcuni scenari che mostrano come funziona la limitazione in particolare con i dischi Ultra condivisi.

##### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Cluster a due nodi che usa volumi condivisi del cluster

Di seguito è riportato un esempio di un cluster WSFC a 2 nodi che usa volumi condivisi del cluster. Con questa configurazione, entrambe le macchine virtuali hanno accesso in scrittura simultaneo al disco, il che comporta la `ReadWrite` suddivisione della limitazione tra le due macchine virtuali e la `ReadOnly` limitazione non in uso.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="Esempio di disco Ultra a due nodi che usa volumi condivisi del cluster":::

##### <a name="two-node-cluster-without-cluster-share-volumes"></a>Cluster a due nodi senza volumi condivisi del cluster

Di seguito è riportato un esempio di un cluster WSFC a 2 nodi che non usa volumi condivisi del cluster. Con questa configurazione, solo una macchina virtuale ha accesso in scrittura al disco. Ciò comporta la `ReadWrite` limitazione dell'utilizzo esclusivo della macchina virtuale primaria e della `ReadOnly` limitazione utilizzata solo dal database secondario.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="Esempio di disco Ultra a due nodi che non usa volumi condivisi del cluster":::

##### <a name="four-node-linux-cluster"></a>Cluster Linux a quattro nodi

Di seguito è riportato un esempio di cluster Linux a 4 nodi con una sola macchina virtuale con accesso in scrittura e tre macchine virtuali con accesso in lettura con scalabilità orizzontale. Con questa configurazione, solo una macchina virtuale ha accesso in scrittura al disco. Ciò comporta la `ReadWrite` limitazione dell'uso esclusivo della macchina virtuale primaria e della limitazione delle `ReadOnly` richieste da parte delle VM secondarie.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Esempio di limitazione di disco Ultra a quattro nodi":::

#### <a name="ultra-pricing"></a>Prezzi ultra

I prezzi per i dischi condivisi sono basati sulla capacità con provisioning, sulle IOPS con provisioning totale (diskIOPSReadWrite + diskIOPSReadOnly) e sulla velocità effettiva totale con provisioning (diskMBpsReadWrite + diskMBpsReadOnly). Non sono previsti costi aggiuntivi per ogni montaggio di VM aggiuntivo. Ad esempio, un disco con una condivisione ultra condivisa con la seguente configurazione (diskSizeGB: 1024, DiskIOPSReadWrite: 10000, DiskMBpsReadWrite: 600, DiskIOPSReadOnly: 100, DiskMBpsReadOnly: 1) viene addebitato con 1024 GiB, 10100 IOPS e 601 MBps, indipendentemente dal fatto che sia montato su due macchine virtuali o cinque macchine virtuali.