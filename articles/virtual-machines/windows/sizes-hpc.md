---
title: Dimensioni delle macchine virtuali Windows di Azure - HPC | Documentazione Microsoft
description: Elenca le diverse dimensioni disponibili per le macchine virtuali High Performance Computing Windows in Azure.
services: virtual-machines-windows
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.openlocfilehash: a0596d134e9c26877848f93d72f35bfd2c957570
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="high-performance-compute-vm-sizes"></a>Dimensioni delle VM High Performance Computing (HPC)

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>Istanze con supporto per RDMA
Un subset delle istanze a elevato uso di calcolo (H16r, H16mr, A8 e A9) è dotato di un'interfaccia di rete per la connettività ad accesso diretto a memoria remota (RDMA). Questa interfaccia è un'aggiunta all'interfaccia di rete di Azure standard disponibile per gli altri formati di VM. 
  
Questa interfaccia consente alle istanze con supporto per RDMA di comunicare attraverso una rete InfiniBand che funziona a velocità FDR per macchine virtuali H16r e H16mr e a velocità QDR per macchine virtuali A8 e A9. Queste funzionalità RDMA possono migliorare la scalabilità e le prestazioni delle applicazioni di interfaccia MPI (Message Passing).

Per accedere alla rete RDMA di Azure, i requisiti per le macchine virtuali Windows con supporto per RDMA sono i seguenti: 

* **Sistema operativo**
  
  Windows Server 2012 R2, Windows Server 2012
  
  > [!NOTE]
  > Windows Server 2016 non supporta attualmente la connettività RDMA in Azure.
  >

* **Set di disponibilità o servizio cloud**: distribuire le VM con supporto per RDMA nello stesso set di disponibilità, se si usa il modello di distribuzione Azure Resource Manager, o nello stesso servizio cloud, se si usa il modello di distribuzione classica. Se si usa Azure Batch, le VM con supporto di RDMA devono trovarsi nello stesso pool.

* **MPI** : Microsoft MPI (MS-MPI) 2012 R2 o versione successiva, Intel MPI Library 5.x

  Le implementazioni MPI supportate usano l'interfaccia Microsoft Network Direct per la comunicazione tra le istanze. 

* **Spazio degli indirizzi della rete RDMA** : la rete RDMA in Azure riserva lo spazio degli indirizzi 172.16.0.0/16. Per eseguire applicazioni MPI in istanze distribuite in una rete virtuale di Azure, assicurarsi che lo spazio degli indirizzi di rete virtuale non si sovrapponga alla rete RDMA.

* **Estensione macchina virtuale HpcVmDrivers**: nelle VM con supporto per RDMA, è necessario aggiungere l'estensione HpcVmDrivers per installare i driver dei dispositivi di rete Windows per la connettività RDMA. In alcune distribuzioni di istanze A8 e A9 l'estensione HpcVmDrivers viene aggiunta automaticamente. Per aggiungere l'estensione macchina virtuale a una macchina virtuale, è possibile usare i cmdlet di [Azure PowerShell](/powershell/azure/overview). 

  
  Il comando seguente installa l'ultima versione 1.1 dell'estensione HpcVMDrivers in una macchina virtuale esistente con supporto per RDMA, denominata *myVM* distribuita nel gruppo di risorse denominato *myResourceGroup* nell'area *Stati Uniti occidentali*:

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  Per altre informazioni, vedere [Estensioni e funzionalità della macchina virtuale](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). È anche possibile usare estensioni delle macchine virtuali nel [modello di distribuzione classico](classic/manage-extensions.md).


## <a name="using-hpc-pack"></a>Utilizzo di HPC Pack

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), la soluzione di gestione di processi e cloud HPC gratuita di Microsoft, è un'opzione per creare un cluster di elaborazione in Azure per l'esecuzione di applicazioni MPI basate su Windows e altri carichi di lavoro HPC. HPC Pack 2012 R2 e versioni successive include un ambiente di runtime per MS-MPI che può usare la rete RDMA di Azure quando viene distribuito in macchine virtuali con supporto per RDMA.




## <a name="other-sizes"></a>Altre dimensioni
- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](../virtual-machines-windows-sizes-memory.md)
- [Ottimizzate per l'archiviazione](../virtual-machines-windows-sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)

## <a name="next-steps"></a>Passaggi successivi

- Per gli elenchi di controllo per usare le istanze a elevato utilizzo di calcolo con HPC Pack in Windows Server, vedere [Configurare un cluster di Windows RDMA con HPC Pack per eseguire applicazioni MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Per usare istanze a elevato uso di calcolo quando si eseguono applicazioni MPI con Azure Batch, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](../../batch/batch-mpi.md).

- Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.




