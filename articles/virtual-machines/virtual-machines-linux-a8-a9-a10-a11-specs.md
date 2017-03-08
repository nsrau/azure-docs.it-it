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
ms.date: 11/18/2016
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f3b1593a80e6eedc08005988d485a317951efd33
ms.openlocfilehash: ad2beff5bbc6aaa18247939ccfe310460f9ccdee
ms.lasthandoff: 03/01/2017

---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-linux"></a>Informazioni sulle macchine virtuali serie H e serie A a elevato utilizzo di calcolo per Linux
Informazioni generali e considerazioni sull'uso delle nuove dimensioni di Azure serie H e delle precedenti dimensioni A8, A9, A10 e A11, note anche come istanze *a elevato utilizzo di calcolo* . Questo articolo illustra l'uso di queste dimensioni per macchine virtuali Linux. Questo articolo è disponibile anche per le [VM Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Per conoscere le specifiche di base, le capacità di archiviazione e i dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Accesso alla rete RDMA
È possibile creare cluster di macchine virtuali Linux con supporto per RDMA che eseguono una delle distribuzioni HPC Linux riportate di seguito e un'implementazione MPI supportata per sfruttare i vantaggi di una rete RDMA di Azure. Per le opzioni di distribuzione e la procedura di configurazione di esempio, vedere [Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) .

* **Distribuzioni** : è necessario distribuire macchine virtuali da immagini SLES (SUSE Linux Enterprise Server) con supporto per RDMA o da immagini HPC OpenLogic basate su CentOS in Azure Marketplace. I driver Linux RDMA necessari sono supportati solo dalle immagini Marketplace seguenti:
  
  * SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium)
  * SLES 12 per HPC, SLES 12 per HPC (Premium)
  * HPC basata su CentOS 7.1
  * HPC basata su CentOS 6.5
    
    > [!NOTE]
    > Per le macchine virtuali serie H è consigliabile un'immagine SLES 12 SP1 per HPC o un'immagine HPC basata su CentOS 7.1.
    > 
    > Nelle immagini HPC basate su CentOS gli aggiornamenti del kernel sono disabilitati nel file di configurazione **yum** . In questo modo i driver Linux RDMA vengono distribuiti come pacchetto RPM e gli aggiornamenti dei driver potrebbero non funzionare se il kernel viene aggiornato.
    > 
    > 
* **MPI** : Intel MPI Library 5.x.
  
    A seconda dell'immagine Marketplace scelta, può essere necessario separare licenze, installazione o configurazione di Intel MPI, come illustrato di seguito: 
  
  * **SLES 12 SP1 per immagine HPC** : installare i pacchetti Intel MPI distribuiti nella macchina virtuale eseguendo il comando seguente:
    
          sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  * **SLES 12 per immagine HPC** : è necessario eseguire una registrazione separata per scaricare e installare Intel MPI. Per istruzioni, vedere la [documentazione relativa a Intel MPI Library](https://software.intel.com/en-us/intel-mpi-library/documentation).
  * **Immagini HPC basate su CentOS** : Intel MPI 5.1 è già installato.  
    
    Per eseguire processi MPI su macchine virtuali n cluster, è necessaria una configurazione di sistema aggiuntiva. In un cluster di macchine virtuali, ad esempio, è necessario stabilire una relazione di trust tra i nodi di calcolo. Per le impostazioni tipiche, vedere [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="considerations-for-hpc-pack-and-linux"></a>Considerazioni per HPC Pack e Linux
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), la soluzione Microsoft gratuita per la gestione di cluster e processi, fornisce un'opzione per l'uso di istanze a elevato utilizzo di calcolo con Linux. La versione più recente di HPC Pack 2012 R2 supporta varie distribuzioni Linux per l'esecuzione su nodi di calcolo distribuiti in VM di Azure, gestite da un nodo head di Windows Server. Grazie ai nodi di calcolo Linux con supporto per RDMA che eseguono Intel MPI, HPC Pack può pianificare ed eseguire applicazioni Linux MPI che hanno accesso alla rete RDMA. Per iniziare, vedere [Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="network-topology-considerations"></a>Considerazioni sulla topologia di rete
* Nelle macchine virtuali Linux con supporto per RDMA in Azure, Eth1 è riservato al traffico di rete RDMA. Non modificare le impostazioni di Eth1 o le informazioni nel file di configurazione riferite a questa rete. Eth0 è riservato per il normale traffico di rete di Azure.
* In Azure, IP over InfiniBand (IB) non è supportato. Solo RDMA over IB è supportato.

## <a name="rdma-driver-updates-for-sles-12"></a>Aggiornamenti dei driver RDMA per SLES 12
Dopo la creazione di una macchina virtuale basata su un'immagine SLES 12 HPC, può essere necessario aggiornare i driver RDMA nelle macchine virtuali per la connettività di rete RDMA. 

> [!IMPORTANT]
> Questo passaggio è **obbligatorio** per le distribuzioni di macchine virtuali SLES 12 per HPC in tutte le aree di Azure. Questo passaggio non è necessario se si distribuisce una macchina virtuale SLES 12 SP1 per HPC, una macchina virtuale HPC basata su CentOS 7.1 o una macchina virtuale HPC basata su CentOS 6.5. 
> 
> 

Prima di aggiornare i driver, arrestare tutti i processi **zypper** o tutti i processi che bloccano i database repository SUSE nella VM. In caso contrario, i driver rischiano di non essere aggiornati correttamente.  

Per aggiornare i driver Linux RDMA in ogni VM, eseguire uno dei seguenti set di comandi dell'interfaccia della riga di comando di Azure seguenti nel computer client.

**Macchina virtuale SLES 12 per HPC di cui è stato effettuato il provisioning nel modello di distribuzione classica**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**Macchina virtuale SLES 12 per HPC di cui è stato effettuato il provisioning nel modello di distribuzione Resource Manager**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

> [!NOTE]
> L'installazione dei driver può richiedere un po' di tempo e il comando non restituisce un output. Dopo l'aggiornamento, la VM viene riavviata e nel giro di qualche minuto è pronta per l'uso.
> 
> 

### <a name="sample-script-for-driver-updates"></a>Script di esempio per gli aggiornamenti dei driver
Se è disponibile un cluster di macchine virtuali SLES 12 per HPC, è possibile creare script di aggiornamento del driver in tutti i nodi del cluster. Ad esempio, lo script seguente aggiorna il driver in un cluster a 8 nodi.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## <a name="next-steps"></a>Passaggi successivi
* Per informazioni dettagliate sulla disponibilità e i prezzi delle dimensioni a elevato utilizzo di calcolo, vedere [Macchine virtuali - Prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).
* Per informazioni sulle funzionalità di archiviazione e dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Per iniziare a distribuire e usare dimensioni a elevato uso di calcolo con RDMA in Linux, vedere [Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


