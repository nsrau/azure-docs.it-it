---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a14ae76e15c1adb59917e61fbcbdaa34a7efa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77472018"
---
Dischi condivisi di Azure (anteprima) è una nuova funzionalità per i dischi gestiti di Azure che consente di collegare un disco gestito di Azure a più macchine virtuali (VM) contemporaneamente. La connessione di un disco gestito a più macchine virtuali consente di distribuire nuove applicazioni cluster o di eseguire la migrazione di applicazioni cluster esistenti in Azure.Attaching a managed disk to multiple VMs allows you to either deploy new or migrate existing clustered applications to Azure.

## <a name="how-it-works"></a>Funzionamento

Le macchine virtuali del cluster possono leggere o scrivere sul disco collegato in base alla prenotazione scelta dall'applicazione cluster tramite [sCSI Persistent Reservations](https://www.t10.org/members/w_spc3.htm) (SCSI Persistent Reservations). SCSI PR è uno standard di settore noto utilizzato dalle applicazioni in esecuzione in locale nella rete SAN (Storage Area Network). L'abilitazione del PR SCSI in un disco gestito consente di eseguire la migrazione di queste applicazioni in Azure così com'è.

I dischi gestiti con dischi condivisi abilitati offrono uno spazio di archiviazione a blocchi condivisi accessibile da più macchine virtuali, che viene esposto come numeri di unità logica (LUN). I LUN vengono quindi presentati a un intatore (VM) da una destinazione (disco). Questi LUN hanno l'aspetto di DAS (Direct-attached-storage) o di un'unità locale per la macchina virtuale.

I dischi gestiti con dischi condivisi abilitati non offrono in modo nativo un file system completamente gestito a cui è possibile accedere tramite SMB/NFS. È necessario utilizzare un gestore cluster, ad esempio WSFC (Windows Server Failover Cluster) o Pacemaker, che gestisce la comunicazione dei nodi del cluster e il blocco in scrittura.

## <a name="limitations"></a>Limitazioni

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Dimensione disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Carichi di lavoro di esempioSample workloads

### <a name="windows"></a>WINDOWS

La maggior parte dei cluster basati su Windows si basa su WSFC, che gestisce tutta l'infrastruttura di base per la comunicazione dei nodi del cluster, consentendo alle applicazioni di sfruttare i modelli di accesso parallelo. WSFC abilita le opzioni basate su CSV e non basate su CSV a seconda della versione di Windows Server in uso. Per informazioni dettagliate, vedere [Creare un cluster](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)di failover .

Alcune applicazioni popolari in esecuzione su WSFC includono:

- Istanze del cluster di failover di SQL ServerSQL Server Failover Cluster Instances (FCI)
- File server di scalabilità orizzontale (SoFS)Scale-out File Server (SoFS)
- File server per uso generale (carico di lavoro IW)
- Disco profilo utente di Remote Desktop Server (UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

I cluster Linux possono sfruttare i gestori di cluster, ad esempio [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker si basa su [Corosync](http://corosync.github.io/corosync/), consentendo le comunicazioni cluster per le applicazioni distribuite in ambienti a disponibilità elevata. Alcuni file system cluster comuni includono [ocfs2](https://oss.oracle.com/projects/ocfs2/) e [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). È possibile manipolare prenotazioni e registrazioni utilizzando servizi di [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) e [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Flusso di prenotazione permanente

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