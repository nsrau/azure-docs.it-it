---
title: Dimensioni delle macchine virtuali Linux in Azure -HPC | Documentazione Microsoft
description: Elenca le diverse dimensioni disponibili per le macchine virtuali High Performance Computing Linux in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: ee99869c2b7a7b3ab38fdd9eae0687862ea53819
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100873"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali di calcolo a prestazioni elevate

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Le dimensioni delle VM abilitate per SR-IOV in Azure consentono di usare praticamente qualsiasi versione di MPI.
Nelle macchine virtuali non SR-IOV abilitate sono supportate solo le versioni Intel MPI 5. x. Le versioni successive (2017, 2018) della libreria di runtime Intel MPI possono o non essere compatibili con i driver RDMA per Linux di Azure.


### <a name="supported-os-images"></a>Immagini del sistema operativo supportate
 
Azure Marketplace include molte distribuzioni Linux che supportano la connettività RDMA:
  
* **HPC basato su CentOS** : per le macchine virtuali non SR-IOV abilitate, la versione 6,5 HPC basata su CentOS o una versione successiva, sono adatti fino a 7,5. Per le macchine virtuali della serie H, sono consigliate le versioni da 7,1 a 7,5. I driver RDMA e Intel MPI 5.1 vengono installati nella VM.
  Per le macchine virtuali SR-IOV, CentOS-HPC 7,6 viene ottimizzato e precaricato con i driver RDMA e diversi pacchetti MPI installati.
  Per altre immagini di macchina virtuale RHEL/CentOS, aggiungere l'estensione InfiniBandLinux per abilitare InfiniBand. Questa estensione VM Linux installa i driver Mellanox OFED (nelle VM SR-IOV) per la connettività RDMA. Il cmdlet di PowerShell seguente installa la versione più recente (versione 1,0) dell'estensione InfiniBandDriverLinux in una macchina virtuale con supporto per RDMA esistente. La VM con supporto per RDMA è denominata *myVM* e viene distribuita nel gruppo di risorse denominato *myResourceGroup* nell'area *Stati Uniti occidentali* come indicato di seguito:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  In alternativa, le estensioni della macchina virtuale possono essere incluse nei modelli di Azure Resource Manager per semplificare la distribuzione con l'elemento JSON seguente:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Il comando seguente installa la versione più recente dell'estensione InfiniBandDriverLinux 1,0 in tutte le VM con supporto per RDMA in un set di scalabilità di macchine virtuali esistente denominato *myVMSS* distribuito nel gruppo di risorse denominato *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Nelle immagini HPC basate su CentOS gli aggiornamenti del kernel sono disabilitati nel file di configurazione **yum** . Ciò è dovuto al fatto che i driver RDMA Linux vengono distribuiti come un pacchetto RPM e gli aggiornamenti dei driver potrebbero non funzionare se il kernel viene aggiornato.
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 per HPC, SLES 12 SP3 per HPC (Premium), SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium), SLES 12 SP4 e SLES 15. I driver RDMA vengono installati e i pacchetti Intel MPI vengono distribuiti nella VM. Installare MPI con questo comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -ubuntu server 16,04 lts, 18,04 LTS. Configurare i driver RDMA nella VM ed eseguire la registrazione con Intel per scaricare Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Per ulteriori informazioni sull'abilitazione di InfiniBand, sulla configurazione di MPI, vedere [Enable InfiniBand](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Opzioni di configurazione del cluster

Azure offre varie opzioni per la creazione di cluster di macchine virtuali HPC Linux in grado di comunicare con la rete RDMA, tra cui: 

* **Macchine virtuali**: distribuire le macchine virtuali HPC con supporto per RDMA nello stesso set di disponibilità (se si usa il modello di distribuzione Azure Resource Manager). Se si usa il modello di distribuzione classico, distribuire le macchine virtuali nello stesso servizio cloud. 

* **Set di scalabilità di macchine virtuali** : in un set di scalabilità di macchine virtuali, assicurarsi di limitare la distribuzione a un singolo gruppo di posizionamento. In un modello di Resource Manager, ad esempio, impostare la proprietà `singlePlacementGroup` su `true`. 

* **MPI tra le macchine virtuali** : se è necessario comunicare MPI tra macchine virtuali (VM), verificare che le macchine virtuali si trovino nello stesso set di disponibilità o nello stesso set di scalabilità.

* **Azure CycleCloud**: creare un cluster HPC in [Azure CycleCloud](/azure/cyclecloud/) per eseguire i processi MPI nei nodi Linux.

* **Azure Batch**: creare un pool di [Azure Batch](/azure/batch/) per eseguire i carichi di lavoro MPI nei nodi di calcolo Linux. Per altre informazioni, vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](../../batch/batch-pool-compute-intensive-sizes.md). Vedere anche il progetto [Batch Shipyard](https://github.com/Azure/batch-shipyard) per l'esecuzione di carichi di lavoro basati su contenitore in Batch.

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) supporta varie distribuzioni Linux per l'esecuzione su nodi di calcolo distribuiti in macchine virtuali di Azure con supporto per RDMA, gestite da un nodo head di Windows Server. Per un esempio di distribuzione, vedere [Create HPC Pack Linux RDMA Cluster in Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam) (Creare un cluster RDMA HPC Pack Linux in Azure).


### <a name="network-considerations"></a>Considerazioni sulla rete
* Nelle VM Linux non SR-IOV abilitate per RDMA in Azure, eth1 è riservato per il traffico di rete RDMA. Non modificare le impostazioni di eth1 o le informazioni nel file di configurazione che fanno riferimento a questa rete.
* Nelle VM abilitate per SR-IOV (HB e HC), Ib0 è riservato per il traffico di rete RDMA.
* La rete RDMA in Azure riserva lo spazio degli indirizzi 172.16.0.0/16. Per eseguire applicazioni MPI in istanze distribuite in una rete virtuale di Azure, assicurarsi che lo spazio degli indirizzi di rete virtuale non si sovrapponga alla rete RDMA.
* In base allo strumento di gestione dei cluster in uso, è possibile che siano necessarie configurazioni di sistema aggiuntive per l'esecuzione dei processi MPI. Ad esempio, in un cluster di macchine virtuali, potrebbe essere necessario stabilire una relazione di trust tra i nodi del cluster generando chiavi SSH o stabilendo accessi SSH senza password.


## <a name="other-sizes"></a>Altre dimensioni
- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come configurare, ottimizzare e ridimensionare i [carichi di lavoro HPC](../workloads/hpc/configure.md) in Azure.
- Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
