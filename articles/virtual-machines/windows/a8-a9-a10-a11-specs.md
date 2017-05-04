---
title: Informazioni sulle macchine virtuali a elevato uso di calcolo con Windows | Microsoft Docs
description: Informazioni generali e considerazioni sull&quot;uso delle dimensioni di Azure serie H e A8, A9, A10 ed A11 a elevato utilizzo di calcolo per VM e servizi cloud Windows
services: virtual-machines-windows, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 7f86fb57d9082e19b506c42737aaa020d474d599
ms.lasthandoff: 04/27/2017


---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-windows"></a>Informazioni sulle macchine virtuali serie H e serie A a elevato utilizzo di calcolo per Windows
Informazioni generali e considerazioni sull'uso delle nuove istanze di Azure serie H e delle precedenti istanze A8, A9, A10 e A11, note anche come istanze *a elevato utilizzo di calcolo* . Questo articolo illustrato l'uso di queste istanze per le VM Windows. È possibile usarle anche per [le macchine virtuali Linux](../linux/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per conoscere le specifiche di base, le capacità di archiviazione e i dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Accesso alla rete RDMA
Per accedere alla rete RDMA per il traffico MPI di Windows MPI, le istanze con supporto RDMA devono soddisfare i requisiti seguenti: 

* **Sistema operativo**
  
  * **Macchine virtuali** : Windows Server 2012 R2, Windows Server 2012
  * **Servizi cloud** : famiglia di sistemi operativi guest Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2

    > [!NOTE]
    > Windows Server 2016 non supporta attualmente la connettività RDMA in Azure.
    >
    
* **MPI** : Microsoft MPI (MS-MPI) 2012 R2 o versione successiva, Intel MPI Library 5.x

  Le implementazioni MPI supportate usano l'interfaccia Microsoft Network Direct per la comunicazione tra le istanze. 
* **Estensione di VM HpcVmDrivers**: nelle VM con supporto per RDMA, è necessario aggiungere l'estensione HpcVmDrivers per installare i driver dei dispositivi di rete Windows che consentono la connettività RDMA. In alcune distribuzioni di istanze A8 e A9 l'estensione HpcVmDrivers viene aggiunta automaticamente. Se è necessario aggiungere l'estensione della macchina virtuale a una macchina virtuale, è possibile usare i cmdlet di [Azure PowerShell](/powershell/azure/overview). 

  
  Per installare l'ultima versione 1.1 dell'estensione HpcVMDrivers in una macchina virtuale con supporto per RDMA esistente e denominata myVM distribuita nel modello di distribuzione di Resource Manager:

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Per altre informazioni, vedere [Estensioni e funzionalità della macchina virtuale](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). È anche possibile usare estensioni delle macchine virtuali nel [modello di distribuzione classico](classic/manage-extensions.md).


## <a name="considerations-for-hpc-pack-and-windows"></a>Considerazioni per HPC Pack e Windows
[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), la soluzione Microsoft gratuita per la gestione di cluster e processi, non è necessaria per l'uso di istanze a elevato utilizzo di calcolo con Windows Server. Rappresenta tuttavia un'opzione per creare un cluster di elaborazione in Azure per l'esecuzione di applicazioni MPI basate su Windows e altri carichi di lavoro HPC. HPC Pack 2012 R2 e versioni successive include un ambiente di runtime per MS-MPI che può usare la rete RDMA di Azure quando viene distribuito in macchine virtuali con supporto per RDMA.

Per altre informazioni ed elenchi di controllo per usare le istanze a elevato utilizzo di calcolo con HPC Pack in Windows Server, vedere [Configurare un cluster di Windows RDMA con HPC Pack per eseguire applicazioni MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni dettagliate sulla disponibilità e i prezzi delle dimensioni a elevato uso di calcolo, vedere [Prezzi di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) e [Prezzi di servizi cloud](https://azure.microsoft.com/pricing/details/cloud-services/).
* Per informazioni sulle funzionalità di archiviazione e dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali in Azure](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Per iniziare a distribuire e usare istanze a elevato utilizzo di calcolo con HPC Pack in Windows, vedere [Configurare un cluster RDMA di Windows con HPC Pack per eseguire applicazioni MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Per informazioni sull'utilizzo di istanze a elevato uso di calcolo per eseguire applicazioni MPI con Azure Batch, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](../../batch/batch-mpi.md).


