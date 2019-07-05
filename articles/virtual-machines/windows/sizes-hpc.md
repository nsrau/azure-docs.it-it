---
title: Dimensioni delle macchine virtuali Windows di Azure - HPC | Documentazione Microsoft
description: Elenca le diverse dimensioni disponibili per le macchine virtuali High Performance Computing Windows in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck;amverma
ms.openlocfilehash: 5fc5b5a287a421f93d3184ded3e429c5cff8fa3c
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566284"
---
# <a name="high-performance-compute-vm-sizes"></a>Dimensioni delle VM High Performance Computing (HPC)

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Sistema operativo** -Windows Server 2016 in tutte le precedenti HPC serie di macchine virtuali. Windows Server 2012 R2, Windows Server 2012 sono supportate anche nelle macchine virtuali abilitate SR-IOV (ad eccezione di conseguenza HB e connessione ibrida).

* **MPI** -The SR-IOV abilitate dimensioni delle macchine Virtuali in Azure (HB, HC) consentire qualsiasi versione di MPI da usare con OFED Mellanox.
Nelle macchine virtuali abilitate SR-IOV, implementazioni MPI supportate usano l'interfaccia Microsoft Network Direct (ND) per la comunicazione tra istanze. Di conseguenza, solo Microsoft MPI (MS-MPI) 2012 R2 o versioni successive e sono supportate le versioni di Intel MPI 5.x. Nelle versioni successive (2017, 2018) del runtime di Intel MPI library può o potrebbe non essere compatibile con i driver RDMA di Azure.

* **Estensione di VM InfiniBandDriverWindows** : nelle VM con supporto per RDMA, aggiungere l'estensione InfiniBandDriverWindows per abilitare InfiniBand. Questa estensione della macchina virtuale Windows installa driver Windows Network Direct (nelle VM di SR-IOV) o i driver Mellanox OFED (nelle VM di SR-IOV) per la connettività RDMA.
In alcune distribuzioni di istanze A8 e A9, l'estensione HpcVmDrivers viene aggiunta automaticamente. Si noti che l'estensione di VM HpcVmDrivers è deprecato; quindi non essere aggiornata. Per aggiungere l'estensione macchina virtuale a una macchina virtuale, è possibile usare i cmdlet di [Azure PowerShell](/powershell/azure/overview). 

  Il comando seguente installa l'estensione di InfiniBandDriverWindows versione 1.0 più recente in una VM esistente con supporto per RDMA denominata *myVM* distribuiti nel gruppo di risorse denominato *myResourceGroup* nel  *Stati Uniti occidentali* area:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  In alternativa, le estensioni di VM possono essere incluso nei modelli di Azure Resource Manager per semplificare la distribuzione, con l'elemento JSON seguente:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Il comando seguente installa l'estensione InfiniBandDriverWindows versione 1.0 più recente in tutte le VM con supporto per RDMA in una scalabilità di macchine Virtuali esistenti set denominato *myVMSS* distribuiti nel gruppo di risorse denominato *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Per altre informazioni, vedere [Estensioni e funzionalità della macchina virtuale](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). È anche possibile usare estensioni delle macchine virtuali nel [modello di distribuzione classico](classic/manage-extensions.md).

* **Spazio degli indirizzi della rete RDMA** : la rete RDMA in Azure riserva lo spazio degli indirizzi 172.16.0.0/16. Per eseguire applicazioni MPI in istanze distribuite in una rete virtuale di Azure, assicurarsi che lo spazio degli indirizzi di rete virtuale non si sovrapponga alla rete RDMA.


### <a name="cluster-configuration-options"></a>Opzioni di configurazione del cluster

Azure offre varie opzioni per la creazione di cluster di macchine virtuali HPC Windows in grado di comunicare con la rete RDMA, tra cui: 

* **Macchine virtuali**: distribuire le macchine virtuali HPC con supporto per RDMA nello stesso set di disponibilità (se si usa il modello di distribuzione Azure Resource Manager). Se si usa il modello di distribuzione classico, distribuire le macchine virtuali nello stesso servizio cloud. 

* **Set di scalabilità di macchine virtuali** : nella scalabilità di macchine virtuali impostato, assicurarsi comunque di limitare la distribuzione in un singolo gruppo di posizionamento. In un modello di Resource Manager, ad esempio, impostare la proprietà `singlePlacementGroup` su `true`. 

* **MPI tra le macchine virtuali** : se la comunicazione MPI se necessarie tra le macchine virtuali (VM), assicurarsi che le macchine virtuali sono nel stesso set di disponibilità o la macchina virtuale stessa set di scalabilità.

* **Azure CycleCloud**: creare un cluster HPC in [Azure CycleCloud](/azure/cyclecloud/) per eseguire i processi MPI nei nodi Windows.

* **Azure Batch**: creare un pool di [Azure Batch](/azure/batch/) per eseguire i carichi di lavoro MPI nei nodi di calcolo di Windows Server. Per altre informazioni, vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](../../batch/batch-pool-compute-intensive-sizes.md). Vedere anche il progetto [Batch Shipyard](https://github.com/Azure/batch-shipyard) per l'esecuzione di carichi di lavoro basati su contenitore in Batch.

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) include un ambiente di runtime per MS-MPI che può usare la rete RDMA di Azure quando viene distribuito in macchine virtuali Windows con supporto per RDMA. Per esempi di distribuzione, vedere [Configurare un cluster RDMA di Windows con HPC Pack per eseguire applicazioni MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Altre dimensioni
- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](../virtual-machines-windows-sizes-memory.md)
- [Ottimizzate per l'archiviazione](../virtual-machines-windows-sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

- Per gli elenchi di controllo per usare le istanze a elevato utilizzo di calcolo con HPC Pack in Windows Server, vedere [Configurare un cluster di Windows RDMA con HPC Pack per eseguire applicazioni MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Per usare istanze a elevato uso di calcolo quando si eseguono applicazioni MPI con Azure Batch, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](../../batch/batch-mpi.md).

- Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
