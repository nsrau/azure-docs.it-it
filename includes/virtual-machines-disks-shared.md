---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c3e5beaef7fcc9d407103834e2040957ff32984c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008542"
---
Dischi condivisi di Azure (anteprima) è una nuova funzionalità per i dischi gestiti di Azure che consentono di collegare un disco gestito a più macchine virtuali (VM) contemporaneamente. La connessione di un disco gestito a più macchine virtuali consente di distribuire nuove applicazioni cluster o di eseguire la migrazione di applicazioni cluster esistenti in Azure.Attaching a managed disk to multiple VMs allows you to either deploy new or migrate existing clustered applications to Azure.

## <a name="how-it-works"></a>Funzionamento

Le macchine virtuali del cluster possono leggere o scrivere sul disco collegato in base alla prenotazione scelta dall'applicazione cluster tramite [sCSI Persistent Reservations](https://www.t10.org/members/w_spc3.htm) (SCSI Persistent Reservations). SCSI PR è uno standard di settore utilizzato dalle applicazioni in esecuzione in locale nella rete SAN (Storage Area Network). L'abilitazione del PR SCSI in un disco gestito consente di eseguire la migrazione di queste applicazioni in Azure così com'è.

La condivisione dei dischi gestiti offre l'archiviazione a blocchi condivisi accessibile da più macchine virtuali, queste vengono esposte come numeri di unità logica (LUN). I LUN vengono quindi presentati a un intatore (VM) da una destinazione (disco). Questi LUN hanno l'aspetto di DAS (Direct-attached-storage) o di un'unità locale per la macchina virtuale.

I dischi gestiti condivisi non offrono in modo nativo un file system completamente gestito a cui è possibile accedere tramite SMB/NFS. È necessario utilizzare un gestore cluster, ad esempio WSFC (Windows Server Failover Cluster) o Pacemaker, che gestisce la comunicazione dei nodi del cluster e il blocco in scrittura.

## <a name="limitations"></a>Limitazioni

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Dimensione disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Carichi di lavoro di esempioSample workloads

### <a name="windows"></a>Windows

La maggior parte dei cluster basati su Windows si basa su WSFC, che gestisce tutta l'infrastruttura di base per la comunicazione dei nodi del cluster, consentendo alle applicazioni di sfruttare i modelli di accesso parallelo. WSFC abilita le opzioni basate su CSV e non basate su CSV a seconda della versione di Windows Server in uso. Per informazioni dettagliate, vedere [Creare un cluster](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)di failover .

Alcune applicazioni popolari in esecuzione su WSFC includono:

- Istanze del cluster di failover di SQL ServerSQL Server Failover Cluster Instances (FCI)
- File server di scalabilità orizzontale (SoFS)Scale-out File Server (SoFS)
- File server per uso generale (carico di lavoro IW)
- Disco profilo utente di Remote Desktop Server (UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

I cluster Linux possono sfruttare i gestori di cluster, ad esempio [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker si basa su [Corosync](http://corosync.github.io/corosync/), consentendo le comunicazioni cluster per le applicazioni distribuite in ambienti a disponibilità elevata. Alcuni file system cluster comuni includono [ocfs2](https://oss.oracle.com/projects/ocfs2/) e [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). È possibile manipolare prenotazioni e registrazioni utilizzando servizi di [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) e [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Flusso di prenotazione persistente

Nel diagramma seguente viene illustrata un'applicazione di database cluster a due nodi di esempio che sfrutta il PR SCSI per abilitare il failover da un nodo all'altro.

![Cluster a due nodi. Un'applicazione in esecuzione nel cluster sta gestendo l'accesso al disco](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Il flusso è il seguente:

1. L'applicazione cluster in esecuzione sia in Azure VM1 che in VM2 registra l'intenzione di leggere o scrivere sul disco.
1. L'istanza dell'applicazione in VM1 accetta quindi la prenotazione esclusiva per scrivere sul disco.
1. Questa prenotazione viene applicata sul disco di Azure e il database può ora scrivere esclusivamente sul disco. Tutte le scritture dall'istanza dell'applicazione in VM2 non avranno esito positivo.
1. Se l'istanza dell'applicazione in VM1 si arresta, l'istanza in VM2 può ora avviare un failover del database e l'acquisizione del disco.
1. Questa prenotazione viene ora applicata sul disco di Azure e il disco non accetterà più scritture da VM1.This reservation is now enforced on the Azure disk and the disk will no longer accept writes from VM1. Accetterà solo scritture da VM2.
1. The clustered application can complete the database failover and serve requests from VM2.

Il diagramma seguente illustra un altro carico di lavoro comune del cluster costituito da più nodi che leggono i dati dal disco per l'esecuzione di processi paralleli, ad esempio il training dei modelli di apprendimento automatico.

![Cluster VM a quattro nodi, ogni nodo registra l'intenzione di scrivere, l'applicazione accetta una prenotazione esclusiva per gestire correttamente i risultati di scrittura](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Il flusso è il seguente:

1. L'applicazione cluster in esecuzione in tutte le macchine virtuali registra l'intenzione di leggere o scrivere sul disco.
1. L'istanza dell'applicazione in VM1 accetta una prenotazione esclusiva per scrivere sul disco durante l'apertura delle letture sul disco da altre macchine virtuali.
1. Questa prenotazione viene applicata sul disco di Azure.This reservation is enforced on your Azure disk.
1. Tutti i nodi del cluster possono ora leggere dal disco. Solo un nodo esegue il backup dei risultati sul disco, per conto di tutti i nodi del cluster.

### <a name="ultra-disks-reservation-flow"></a>Flusso di prenotazione di dischi ultra

I dischi Ultra offrono un'acceleratore aggiuntiva, per un totale di due acceleratori. A causa di ciò, il flusso di prenotazione di dischi ultra può funzionare come descritto nella sezione precedente oppure può limitare e distribuire le prestazioni in modo più granulare.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>Acceleratori di prestazioni del disco ultra

I dischi ultra hanno la capacità unica di consentire di impostare le prestazioni esponendo attributi modificabili e consentendo di modificarli. Per impostazione predefinita, esistono solo due attributi modificabili, ma i dischi ultra condivisi hanno due attributi aggiuntivi.


|Attributo  |Descrizione  |
|---------|---------|
|DiskIOPSReadWrite     |Numero totale di operazioni di I/O al secondo consentite in tutte le macchine virtuali che montano il disco di condivisione con accesso in scrittura.         |
|DiskMBpsReadWrite     |Velocità effettiva totale consentita in tutte le macchine virtuali che montano il disco condiviso con accesso in scrittura.         |
|DiskIOPSReadOnly     |Numero totale di operazioni di I/O al secondo consentite in tutte le macchine virtuali che montano il disco condiviso come ReadOnly.         |
|Proprietà DiskMBpsReadOnly     |Velocità effettiva totale consentita in tutte le macchine virtuali che montano il disco condiviso come ReadOnly.         |

\*Si applica solo ai dischi ultra condivisi

Le formule seguenti spiegano come impostare gli attributi delle prestazioni, poiché sono modificabili dall'utente:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - Limiti di IOPS di 300 IOPS/GiB, fino a un massimo di 160K IOPS per disco
    - Minimo 100 operazioni di I/O al secondo
    - DiskIOPSReadWrite : DiskIOPSReadOnly è almeno 2 IOPS/GiB
- DiskMBpsRead Write/DiskMBpsReadOnly:
    - Il limite di velocità effettiva di un singolo disco è di 256 KiB/s per ogni IOPS di cui è stato eseguito il provisioning, fino a un massimo di 2000 MBps per disco
    - La velocità effettiva minima garantita per disco è 4 KiB/s per ogni IOPS di cui è stato eseguito il provisioning, con un minimo di base complessivo di 1 MBps

### <a name="examples"></a>Esempi

Negli esempi seguenti vengono illustrati alcuni scenari che illustrano il funzionamento della limitazione delle richieste con dischi ultra condivisi, in particolare.

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Cluster a due nodi che utilizzano volumi condivisi del cluster

Di seguito è riportato un esempio di WSFC a 2 nodi che utilizza volumi condivisi in cluster. Con questa configurazione, entrambe le macchine virtuali dispongono di accesso simultaneo in scrittura al disco, con conseguente suddivisione dell'acceleratore ReadWrite tra le due macchine virtuali e la limitazione ReadOnly non in uso.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV a due nodi ultra esempio":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>Cluster a due nodi senza volumi di condivisione cluster

Di seguito è riportato un esempio di un cluster WSFC a 2 nodi che non utilizza volumi condivisi in cluster. Con questa configurazione, solo una macchina virtuale ha accesso in scrittura al disco. In questo modo l'acceleratore ReadWrite viene utilizzato esclusivamente per la macchina virtuale primaria e la limitazione ReadOnly utilizzata solo dal database secondario.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV due nodi nessun esempio di disco ultra csv":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>Cluster Linux a quattro nodi

Di seguito è riportato un esempio di cluster Linux a 4 nodi con un singolo writer e tre lettori con scalabilità orizzontale. Con questa configurazione, solo una macchina virtuale ha accesso in scrittura al disco. In questo modo l'acceleratore ReadWrite viene usato esclusivamente per la macchina virtuale primaria e la limitazione ReadOnly viene divisa dalle macchine virtuali secondarie.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Esempio di limitazione di più di nodo a quattro nodi":::

:::image-end:::