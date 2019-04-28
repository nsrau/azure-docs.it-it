---
title: Dimensioni delle macchine virtuali Linux in Azure -HPC | Documentazione Microsoft
description: Elenca le diverse dimensioni disponibili per le macchine virtuali High Performance Computing Linux in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 44b965bd60d976d4d28dc5e31d78a1c838d4ee02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542365"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali di calcolo a prestazioni elevate

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Sono supportati solo i runtime Intel MPI 5.x.

> [!NOTE]
> Nelle versioni successive (2017, 2018) del runtime di Intel MPI library può o potrebbe non essere compatibile con i driver Linux RDMA in Azure.

### <a name="distributions"></a>Distribuzioni
 
Distribuire una VM a elevato utilizzo di calcolo da una delle immagini in Azure Marketplace che supporta la connettività RDMA:
  
* **Ubuntu**: Ubuntu Server 16.04 LTS. Configurare i driver RDMA nella VM ed eseguire la registrazione con Intel per scaricare Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server**: SLES 12 SP3 per HPC, SLES 12 SP3 per HPC (Premium), SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium). I driver RDMA vengono installati e i pacchetti Intel MPI vengono distribuiti nella VM. Installare MPI con questo comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **HPC basato su CentOS**: HPC basato su CentOS 6.5 o versione successiva. Per la serie H, si consiglia la versione 7.1 o successiva. I driver RDMA e Intel MPI 5.1 vengono installati nella VM.  
 
  > [!NOTE]
  > Nelle immagini HPC basate su CentOS gli aggiornamenti del kernel sono disabilitati nel file di configurazione **yum** . In questo modo i driver Linux RDMA vengono distribuiti come pacchetto RPM e gli aggiornamenti dei driver potrebbero non funzionare se il kernel viene aggiornato.
  > 
 
### <a name="cluster-configuration-options"></a>Opzioni di configurazione del cluster

Azure offre varie opzioni per la creazione di cluster di macchine virtuali HPC Linux in grado di comunicare con la rete RDMA, tra cui: 

* **Macchine virtuali**: distribuire le macchine virtuali HPC con supporto per RDMA nello stesso set di disponibilità (se si usa il modello di distribuzione Azure Resource Manager). Se si usa il modello di distribuzione classico, distribuire le macchine virtuali nello stesso servizio cloud. 

* **Set di scalabilità di macchine virtuali**: in un set di scalabilità di macchine virtuali, assicurarsi di limitare la distribuzione a un singolo gruppo di posizionamento. In un modello di Resource Manager, ad esempio, impostare la proprietà `singlePlacementGroup` su `true`. 

* **Azure CycleCloud**: creare un cluster HPC in [Azure CycleCloud](/azure/cyclecloud/) per eseguire i processi MPI nei nodi Linux.

* **Azure Batch**: creare un pool di [Azure Batch](/azure/batch/) per eseguire i carichi di lavoro MPI nei nodi di calcolo Linux. Per altre informazioni, vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](../../batch/batch-pool-compute-intensive-sizes.md). Vedere anche il progetto [Batch Shipyard](https://github.com/Azure/batch-shipyard) per l'esecuzione di carichi di lavoro basati su contenitore in Batch.

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) supporta varie distribuzioni Linux per l'esecuzione su nodi di calcolo distribuiti in macchine virtuali di Azure con supporto per RDMA, gestite da un nodo head di Windows Server. Per un esempio di distribuzione, vedere [Create HPC Pack Linux RDMA Cluster in Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam) (Creare un cluster RDMA HPC Pack Linux in Azure).

In base allo strumento di gestione dei cluster in uso, è possibile che siano necessarie configurazioni di sistema aggiuntive per l'esecuzione dei processi MPI. In un cluster di macchine virtuali, ad esempio, è possibile che sia necessario stabilire un trust tra i nodi del cluster generando chiavi SSH o stabilendo un trust SSH senza password.

### <a name="network-topology-considerations"></a>Considerazioni sulla topologia di rete
* Nelle macchine virtuali Linux con supporto per RDMA in Azure, Eth1 è riservato al traffico di rete RDMA. Non modificare le impostazioni di Eth1 o le informazioni nel file di configurazione riferite a questa rete. Eth0 è riservato per il normale traffico di rete di Azure.

* La rete RDMA in Azure riserva lo spazio degli indirizzi 172.16.0.0/16. 




## <a name="other-sizes"></a>Altre dimensioni
- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.




