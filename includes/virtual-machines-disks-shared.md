---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2589c2abf13edc19b930d597a4d75a2be823f45d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277916"
---
I dischi condivisi di Azure (anteprima) sono una nuova funzionalità di Azure Managed Disks che consente di collegare un disco gestito a più macchine virtuali contemporaneamente. Collegando un disco gestito a più macchine virtuali è possibile distribuire nuove applicazioni in cluster o eseguire la migrazione di quelle esistenti in Azure.

## <a name="how-it-works"></a>Funzionamento

Le macchine virtuali nel cluster possono leggere o scrivere sul disco collegato in base alla prenotazione scelta dall'applicazione in cluster usando le [prenotazioni permanenti SCSI](https://www.t10.org/members/w_spc3.htm). Una prenotazione permanente SCSI è uno standard del settore usato dalle applicazioni in esecuzione in una rete di archiviazione (SAN) locale. L'abilitazione delle prenotazioni permanenti SCSI in un disco gestito consente di eseguire la migrazione di queste applicazioni in Azure così come sono.

La condivisione di dischi gestiti offre un'archiviazione a blocchi condivisa a cui è possibile accedere da più macchine virtuali, che vengono esposte come numeri di unità logica (LUN). I LUN vengono quindi presentati a un iniziatore (macchina virtuale) da una destinazione (disco). Questi LUN hanno un aspetto simile all'archiviazione collegata direttamente (DAS) o a un'unità locale per la macchina virtuale.

I dischi gestiti condivisi non offrono in modo nativo un file system completamente gestito accessibile tramite SMB/NFS. È necessario usare uno strumento di gestione cluster, come Windows Server Failover Cluster (WSFC) o Pacemaker, che gestisce la comunicazione del nodo del cluster oltre al blocco della scrittura.

## <a name="limitations"></a>Limitazioni

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Dimensione disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Carichi di lavoro di esempio

### <a name="windows"></a>Windows

La maggior parte del clustering basato su Windows è compilato su WSFC, che gestisce tutta l'infrastruttura di base per la comunicazione del nodo cluster, consentendo alle applicazioni di sfruttare i vantaggi dei modelli di accesso parallelo. WSFC abilita le opzioni basate e non su CSV a seconda della versione di Windows Server in uso. Per i dettagli, fare riferimento a [Creare un cluster di failover](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Tra le applicazioni più comuni in esecuzione su WSFC ci sono:

- [Creare un'istanza FCI con i dischi condivisi di Azure (SQL Server in macchine virtuali di Azure)](../articles/azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md)
- File server di scalabilità orizzontale (SoFS)
- File server per uso generale (carico di lavoro IW)
- Disco del profilo utente di server desktop remoto (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

I cluster Linux possono sfruttare gli strumenti di gestione cluster, ad esempio [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker si basa su [Corosync](http://corosync.github.io/corosync/), abilitando le comunicazioni del cluster per le applicazioni distribuite in ambienti a disponibilità elevata. Alcuni file system in cluster comuni includono [ocfs2](https://oss.oracle.com/projects/ocfs2/) e [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). È possibile modificare prenotazioni e registrazioni tramite utilità quali [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) e [sg_persist](https://linux.die.net/man/8/sg_persist).

#### <a name="ubuntu"></a>Ubuntu

Per informazioni su come configurare la disponibilità elevata di Ubuntu con Corosync e Pacemaker nei dischi condivisi di Azure, vedere [Ubuntu Community Discourse](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874).

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

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="Immagine di una tabella che illustra l'accesso di sola lettura o di lettura/scrittura per il titolare della prenotazione, l'utente registrato e altri.":::

## <a name="performance-throttles"></a>Limitazioni delle prestazioni

### <a name="premium-ssd-performance-throttles"></a>Limitazioni delle prestazioni SSD Premium
Con l'unità SSD Premium, le operazioni di i/o al secondo e la velocità effettiva sono fisse, ad esempio IOPS di un P30 è 5000. Questo valore rimane se il disco è condiviso tra due macchine virtuali o 5 macchine virtuali. I limiti del disco possono essere raggiunti da una singola macchina virtuale o divisi in due o più macchine virtuali. 

### <a name="ultra-disk-performance-throttles"></a>Limitazioni delle prestazioni dei dischi Ultra

I dischi Ultra offrono la funzionalità esclusiva di consentire l'impostazione delle prestazioni esponendo attributi modificabili e consentendo di modificarli. Per impostazione predefinita, ci sono solo due attributi modificabili, ma i dischi Ultra condivisi hanno due attributi aggiuntivi.


|Attributo  |Descrizione  |
|---------|---------|
|DiskIOPSReadWrite     |Numero totale di operazioni di I/O al secondo consentite in tutte le macchine virtuali che hanno montato il disco condiviso con accesso in scrittura.         |
|DiskMBpsReadWrite     |Velocità effettiva totale (MB/s) consentita in tutte le macchine virtuali che hanno montato il disco condiviso con accesso in scrittura.         |
|DiskIOPSReadOnly*     |Numero totale di operazioni di I/O al secondo consentite in tutte le macchine virtuali che hanno montato il disco condiviso come di sola lettura.         |
|DiskMBpsReadOnly*     |Velocità effettiva totale (MB/s) consentita in tutte le macchine virtuali che hanno montato il disco condiviso come di sola lettura.         |

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

Di seguito è riportato un esempio di un cluster WSFC a 2 nodi che usa volumi condivisi del cluster. Con questa configurazione, entrambe le macchine virtuali hanno accesso in scrittura simultaneo al disco, il che comporta la divisione della limitazione ReadWrite tra le due macchine virtuali e il mancato utilizzo della limitazione ReadOnly.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="Esempio di disco Ultra a due nodi che usa volumi condivisi del cluster":::

##### <a name="two-node-cluster-without-cluster-share-volumes"></a>Cluster a due nodi senza volumi condivisi del cluster

Di seguito è riportato un esempio di un cluster WSFC a 2 nodi che non usa volumi condivisi del cluster. Con questa configurazione, solo una macchina virtuale ha accesso in scrittura al disco. Di conseguenza, la limitazione ReadWrite viene usata esclusivamente per la macchina virtuale primaria e la limitazione ReadOnly viene usata solo dalla macchina virtuale secondaria.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="Esempio di disco Ultra a due nodi che non usa volumi condivisi del cluster":::

##### <a name="four-node-linux-cluster"></a>Cluster Linux a quattro nodi

Di seguito è riportato un esempio di cluster Linux a 4 nodi con una sola macchina virtuale con accesso in scrittura e tre macchine virtuali con accesso in lettura con scalabilità orizzontale. Con questa configurazione, solo una macchina virtuale ha accesso in scrittura al disco. Di conseguenza, la limitazione ReadWrite viene usata esclusivamente per la macchina virtuale primaria e la limitazione ReadOnly è divisa tra le macchine virtuali secondarie.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Esempio di limitazione di disco Ultra a quattro nodi":::
