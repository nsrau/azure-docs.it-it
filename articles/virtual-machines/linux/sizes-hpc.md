---
title: Dimensioni delle macchine virtuali Linux in Azure -HPC | Microsoft Docs
description: "Elenca le diverse dimensioni disponibili per le macchine virtuali High Performance Computing Linux in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie."
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
ms.date: 11/08/2017
ms.author: jonbeck
ms.openlocfilehash: cdfd09d90be9696dacc151e138920944c8bbd2c9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali di calcolo a prestazioni elevate

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>Istanze con supporto per RDMA
Un subset delle istanze a elevato utilizzo di calcolo (H16r, H16mr, NC24r, A8 e A9) è dotato di un'interfaccia di rete per la connettività ad accesso diretto a memoria remota (RDMA). Questa interfaccia è un'aggiunta all'interfaccia di rete di Azure standard disponibile per gli altri formati di VM. 
  
Questa interfaccia consente alle istanze con supporto per RDMA di comunicare attraverso una rete InfiniBand che funziona a velocità FDR per macchine virtuali H16r, H16mr e NC24r e a velocità QDR per macchine virtuali A8 e A9. Le funzionalità RDMA possono migliorare la scalabilità e le prestazioni delle applicazioni MPI (Message Passing Interface) in esecuzione solo in Intel MPI 5.x. Le versioni successive (2017, 2018) della libreria di runtime MPI Intel non sono compatibili con i driver RDMA di Azure.

Distribuire le VM con supporto per RDMA nello stesso set di disponibilità, se si usa il modello di distribuzione Azure Resource Manager, o nello stesso servizio cloud, se si usa il modello di distribuzione classica. Seguono altri requisiti per le macchine virtuali Linux con supporto per RDMA per accedere alla rete RDMA di Azure.

### <a name="distributions"></a>Distribuzioni
 
Distribuire una VM a elevato utilizzo di calcolo da una delle immagini in Azure Marketplace che supporta la connettività RDMA:
  
* **Ubuntu**: Ubuntu Server 16.04 LTS. Configurare i driver RDMA nella VM ed eseguire la registrazione con Intel per scaricare Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server**: SLES 12 SP3 per HPC, SLES 12 SP3 per HPC (Premium), SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium). I driver RDMA vengono installati e i pacchetti Intel MPI vengono distribuiti nella VM. Installare MPI con questo comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **HPC basato su CentOS**: HPC basato su CentOS 7.3, HPC basato su CentOS 7.1, HPC basato su CentOS 6.8 o HPC basato su CentOS 6.5. Per la serie H, si consiglia la versione 7.1 o successiva. I driver RDMA e Intel MPI 5.1 vengono installati nella VM.  
 
  > [!NOTE]
  > Nelle immagini HPC basate su CentOS gli aggiornamenti del kernel sono disabilitati nel file di configurazione **yum** . In questo modo i driver Linux RDMA vengono distribuiti come pacchetto RPM e gli aggiornamenti dei driver potrebbero non funzionare se il kernel viene aggiornato.
  > 
 
### <a name="cluster-configuration"></a>Configurazione del cluster 
    
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




