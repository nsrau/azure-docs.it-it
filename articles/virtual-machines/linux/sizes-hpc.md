---
title: Dimensioni delle macchine virtuali Linux in Azure -HPC | Documentazione Microsoft
description: Elenca le diverse dimensioni disponibili per le macchine virtuali High Performance Computing Linux in Azure.
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.openlocfilehash: b7a3844ce86b4efac8a4fc3c2540e7b6460873a2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="high-performance-compute-linux-vm-sizes"></a>Dimensioni delle macchine virtuali High Performance Computing (HPC) Linux

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>Istanze con supporto per RDMA
Un subset delle istanze a elevato uso di calcolo (H16r, H16mr, A8 e A9) è dotato di un'interfaccia di rete per la connettività ad accesso diretto a memoria remota (RDMA). Questa interfaccia è un'aggiunta all'interfaccia di rete di Azure standard disponibile per gli altri formati di VM. 
  
Questa interfaccia consente alle istanze con supporto per RDMA di comunicare attraverso una rete InfiniBand che funziona a velocità FDR per macchine virtuali H16r e H16mr e a velocità QDR per macchine virtuali A8 e A9. Queste funzionalità RDMA possono migliorare la scalabilità e le prestazioni delle applicazioni di interfaccia MPI (Message Passing).

Di seguito sono riportati i requisiti per le macchine virtuali Linux con supporto per RDMA per accedere alla rete RDMA di Azure:
 
* **Distribuzioni**: è necessario distribuire le macchine virtuali da immagini SLES (SUSE Linux Enterprise Server) con supporto per RDMA o da immagini Rogue Wave Software (in precedenza OpenLogic) basate su CentOS in Azure Marketplace. Le immagini Marketplace seguenti supportano la connettività RDMA:
  
    * SLES 12 SP1 per HPC o SLES 12 SP1 per HPC (Premium)
    
    * HPC basata su CentOS 7.3, HPC basata su CentOS 7.1, HPC basata su CentOS 6.8 o HPC basata su CentOS 6.5  
 
        > [!NOTE]
        > Per le macchine virtuali serie H è consigliabile un'immagine SLES 12 SP1 per HPC o un'immagine HPC basata su CentOS 7.1 o versione successiva.
        >
        > Nelle immagini HPC basate su CentOS gli aggiornamenti del kernel sono disabilitati nel file di configurazione **yum** . In questo modo i driver Linux RDMA vengono distribuiti come pacchetto RPM e gli aggiornamenti dei driver potrebbero non funzionare se il kernel viene aggiornato.
        > 
        > 
* **MPI** : Intel MPI Library 5.x.
  
    A seconda dell'immagine Marketplace scelta, può essere necessario separare licenze, installazione o configurazione di Intel MPI, come illustrato di seguito: 
  
  * **Immagine SLES 12 SP1 per HPC** - I pacchetti Intel MPI vengono distribuiti nella macchina virtuale. Eseguire l'installazione con questo comando:

      ```bash
      sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
      ```

  * **Immagini HPC basate su CentOS** : Intel MPI 5.1 è già installato.  
    
    Per eseguire processi MPI su macchine virtuali n cluster, è necessaria una configurazione di sistema aggiuntiva. In un cluster di macchine virtuali, ad esempio, è necessario stabilire una relazione di trust tra i nodi di calcolo. Per le impostazioni tipiche, vedere [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Considerazioni sulla topologia di rete
* Nelle macchine virtuali Linux con supporto per RDMA in Azure, Eth1 è riservato al traffico di rete RDMA. Non modificare le impostazioni di Eth1 o le informazioni nel file di configurazione riferite a questa rete. Eth0 è riservato per il normale traffico di rete di Azure.

* In Azure, IP over InfiniBand (IB) non è supportato. Solo RDMA over IB è supportato.

## <a name="using-hpc-pack"></a>Utilizzo di HPC Pack
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), la soluzione di gestione di processi e cluster HPC gratuita di Microsoft, è un'opzione per l'uso di istanze a elevato utilizzo di calcolo con Linux. La versione più recente di HPC Pack supporta varie distribuzioni Linux per l'esecuzione su nodi di calcolo distribuiti in VM di Azure, gestite da un nodo head di Windows Server. Grazie ai nodi di calcolo Linux con supporto per RDMA che eseguono Intel MPI, HPC Pack può pianificare ed eseguire applicazioni Linux MPI che hanno accesso alla rete RDMA. Vedere l'articolo di [introduzione ai nodi di calcolo Linux in un cluster HPC Pack in Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Altre dimensioni
- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a distribuire e usare dimensioni a elevato uso di calcolo con RDMA in Linux, vedere [Configurare un cluster Linux RDMA per eseguire applicazioni MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.




