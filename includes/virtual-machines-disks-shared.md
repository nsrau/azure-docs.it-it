---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1bdb4e40d7e173dcb2368f2f0cf645581647f6ee
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202203"
---
Azure Shared Disks (anteprima) è una nuova funzionalità di Azure Managed disks che consente di aggiungere simultaneamente un disco gestito di Azure a più macchine virtuali (VM). Il fissaggio di un disco gestito a più macchine virtuali consente di distribuire le applicazioni in cluster nuove o migrate in Azure.

## <a name="how-it-works"></a>Funzionamento

Le macchine virtuali nel cluster possono leggere o scrivere sul disco collegato in base alla prenotazione scelta dall'applicazione in cluster usando le [prenotazioni permanenti SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). La richiesta pull di SCSI è uno standard di settore noto, sfruttato da applicazioni in esecuzione su SAN (Storage Area Network) in locale. L'abilitazione della richiesta pull SCSI in un disco gestito consente di eseguire la migrazione di queste applicazioni in Azure così come sono.

I dischi gestiti con dischi condivisi abilitati offrono un'archiviazione a blocchi condivisa a cui possono accedere più macchine virtuali, che vengono esposti come numeri di unità logica (lun). I LUN vengono quindi presentati a un iniziatore (macchina virtuale) da una destinazione (disco). Questi LUN hanno un aspetto simile all'archiviazione diretta (DAS) o a un'unità locale per la macchina virtuale.

I dischi gestiti con dischi condivisi abilitati non offrono in modo nativo un file system completamente gestito a cui è possibile accedere tramite SMB/NFS. È necessario usare un gestore di cluster, ad esempio Windows Server failover cluster (WSFC) o pacemaker, che gestisce la comunicazione tra i nodi del cluster e il blocco di scrittura.

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

![Shared-Disk-updated-two-node-cluster-Diagram. png](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Il flusso è il seguente:

1. L'applicazione in cluster in esecuzione sia in Azure VM1 che in VM2 registra la finalità di lettura o scrittura sul disco.
1. L'istanza dell'applicazione in VM1 accetta quindi la prenotazione esclusiva per scrivere sul disco.
1. Questa prenotazione viene applicata sul disco di Azure e il database ora può scrivere esclusivamente sul disco. Eventuali scritture dall'istanza dell'applicazione in VM2 non riusciranno.
1. Se l'istanza dell'applicazione in VM1 diventa inattiva, l'istanza in VM2 può ora avviare un failover del database ed eseguire il commit del disco.
1. Questa prenotazione viene ora applicata sul disco di Azure e il disco non accetterà più le Scritture da VM1. Accetterà solo le Scritture da VM2.
1. L'applicazione in cluster può completare il failover del database e gestire le richieste da VM2.

Il diagramma seguente illustra un altro carico di lavoro cluster comune costituito da più nodi che leggono dati dal disco per l'esecuzione di processi paralleli, ad esempio il training di modelli di machine learning.

![Shared-Disk-updated-Machine-Learning-Trainer-Model. png](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Il flusso è il seguente:

1. L'applicazione in cluster in esecuzione su tutte le macchine virtuali registra la finalità di lettura o scrittura sul disco.
1. L'istanza dell'applicazione in VM1 accetta una prenotazione esclusiva per scrivere sul disco durante l'apertura delle letture sul disco da altre macchine virtuali.
1. Questa prenotazione viene applicata nel disco di Azure.
1. Tutti i nodi del cluster possono ora leggere dal disco. Solo un nodo scrive i risultati sul disco, per conto di tutti i nodi del cluster.