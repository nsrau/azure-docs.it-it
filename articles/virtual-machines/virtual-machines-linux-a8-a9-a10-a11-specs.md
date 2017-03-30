---
title: Informazioni sulle macchine virtuali a elevato uso di calcolo con Linux | Microsoft Docs
description: Informazioni generali e considerazioni sull&quot;uso delle dimensioni serie H e A8, A9, A10 e A11 a elevato utilizzo di calcolo per macchine virtuali Linux
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 16cf6e2d-f401-4b22-af8c-9a337179f5f6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: e6818ea7db680f34bb4ee900396c74fc6e42c4b6
ms.lasthandoff: 03/16/2017

---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-linux"></a>Informazioni sulle macchine virtuali serie H e serie A a elevato utilizzo di calcolo per Linux
Informazioni generali e considerazioni sull'uso delle nuove dimensioni di Azure serie H e delle precedenti dimensioni A8, A9, A10 e A11, note anche come istanze *a elevato utilizzo di calcolo* . Questo articolo illustra l'uso di queste dimensioni per macchine virtuali Linux. È possibile usarle anche per [macchine virtuali di Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Per conoscere le specifiche di base, le capacità di archiviazione e i dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Accesso alla rete RDMA
È possibile creare cluster di macchine virtuali Linux con supporto per RDMA che eseguono una delle distribuzioni HPC Linux riportate di seguito e un'implementazione MPI supportata per sfruttare i vantaggi di una rete RDMA di Azure. Per le opzioni di distribuzione e la procedura di configurazione di esempio, vedere [Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) .

* **Distribuzioni** : è necessario distribuire macchine virtuali da immagini SLES (SUSE Linux Enterprise Server) con supporto per RDMA o da immagini HPC OpenLogic basate su CentOS in Azure Marketplace. Le immagini Marketplace seguenti supportano la connettività RDMA:
  
    * SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium)
    
    * HPC 7.1 basata su CentOS, HPC 6.5 basata su CentOS  
 
        > [!NOTE]
        > Per le macchine virtuali serie H è consigliabile un'immagine SLES 12 SP1 per HPC o un'immagine HPC basata su CentOS 7.1.
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
    
    Per eseguire processi MPI su macchine virtuali n cluster, è necessaria una configurazione di sistema aggiuntiva. In un cluster di macchine virtuali, ad esempio, è necessario stabilire una relazione di trust tra i nodi di calcolo. Per le impostazioni tipiche, vedere [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="considerations-for-hpc-pack-and-linux"></a>Considerazioni per HPC Pack e Linux
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), la soluzione Microsoft gratuita per la gestione di cluster e processi, fornisce un'opzione per l'uso di istanze a elevato utilizzo di calcolo con Linux. La versione più recente di HPC Pack supporta varie distribuzioni Linux per l'esecuzione su nodi di calcolo distribuiti in VM di Azure, gestite da un nodo head di Windows Server. Grazie ai nodi di calcolo Linux con supporto per RDMA che eseguono Intel MPI, HPC Pack può pianificare ed eseguire applicazioni Linux MPI che hanno accesso alla rete RDMA. Per iniziare, vedere [Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="network-topology-considerations"></a>Considerazioni sulla topologia di rete
* Nelle macchine virtuali Linux con supporto per RDMA in Azure, Eth1 è riservato al traffico di rete RDMA. Non modificare le impostazioni di Eth1 o le informazioni nel file di configurazione riferite a questa rete. Eth0 è riservato per il normale traffico di rete di Azure.
* In Azure, IP over InfiniBand (IB) non è supportato. Solo RDMA over IB è supportato.



## <a name="next-steps"></a>Passaggi successivi
* Per informazioni dettagliate sulla disponibilità e i prezzi delle dimensioni a elevato utilizzo di calcolo, vedere [Macchine virtuali - Prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).
* Per informazioni sulle funzionalità di archiviazione e dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Per iniziare a distribuire e usare dimensioni a elevato uso di calcolo con RDMA in Linux, vedere [Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


