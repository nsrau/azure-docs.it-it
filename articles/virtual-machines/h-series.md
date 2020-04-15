---
title: Serie H - Macchine virtuali di AzureH-series - Azure Virtual Machines
description: Specifiche per le macchine virtuali della serie H.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: 6e0257f8a32d05f49dd67195f22d387319358ef2
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314139"
---
# <a name="h-series"></a>Serie H

Le macchine virtuali della serie H sono ottimizzate per le applicazioni guidate da alte frequenze della CPU o da grandi quantità di memoria per core. Le macchine virtuali di serie H sono dotate di 8 o 16 core del processore Intel Xeon E5 2667 v3, fino a 14 GB di RAM per core CPU e nessun hyperthreading. Serie H dispone di 56 Gb/sec Mellanox FDR InfiniBand in una configurazione ad albero grasso non bloccante per prestazioni RDMA coerenti. Le macchine virtuali della serie H supportano Intel MPI 5.x e MS-MPI.

ACU: 290-300

Archiviazione Premium: non supportata

Memorizzazione nella cache Archiviazione Premium: non supportata

Live Migration: non supportato

Aggiornamenti di conservazione della memoria: non supportatiMemory Preserving Updates: Not Supported

| Dimensione | vCPU | Processore | Memoria (GB) | Larghezza di banda della memoria GB/s | Frequenza CPU di base (GHz) | Frequenza di tutti i core (GHz, picco) | Frequenza monocore (GHz, picco) | Prestazioni RDMA (Gb/s) | Supporto MPI | Archiviazione temporanea (GB) | Numero massimo di dischi dati | Velocità effettiva del disco max: IOPS | Schede di interfaccia di rete Max Ethernet |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> Per le applicazioni MPI, la rete back-end RDMA dedicata è abilitata dalla rete FDR InfiniBand.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Immagini del sistema operativo supportate (Linux)
 
Azure Marketplace include molte distribuzioni Linux che supportano la connettività RDMA:The Azure Marketplace has many Linux distributions that support RDMA connectivity:
  
* **HPC basato su CentOS** - Per le macchine virtuali non-SR-IOV abilitate, la versione 6.5 basata su CentOS o una versione successiva, fino a 7.5 sono adatti. Per le macchine virtuali della serie H, sono consigliate le versioni da 7.1 a 7.5.For H-series VMs, versions 7.1 to 7.5 are recommended. I driver RDMA e Intel MPI 5.1 vengono installati nella VM.
  Per le macchine virtuali SR-IOV, CentOS-HPC 7.6 è ottimizzato e precaricato con i driver RDMA e vari pacchetti MPI installati.
  Per altre immagini di macchine virtuali RHEL/CentOS, aggiungere l'estensione InfiniBandLinux per abilitare InfiniBand.For other RHEL/CentOS VM images, add the InfiniBandLinux extension to enable InfiniBand. Questa estensione VM Linux installa i driver OFED Mellanox (nelle macchine virtuali SR-IOV) per la connettività RDMA. Il cmdlet PowerShell seguente installa la versione più recente (versione 1.0) dell'estensione InfiniBandDriverLinux in una macchina virtuale con supporto RDMA esistente. La macchina virtuale che supporta RDMA è denominata myVM e viene distribuita nel gruppo di risorse denominato myResourceGroup nell'area *Stati Uniti occidentali* come segue:The RDMA-capable VM is named *myVM* and is deployed in the resource group named *myResourceGroup* in the West US region as follows:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  In alternativa, le estensioni della macchina virtuale possono essere incluse nei modelli di Azure Resource Manager per semplificare la distribuzione con il seguente elemento JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Il comando seguente installa l'estensione InfiniBandDriverLinux della versione 1.0 più recente in tutte le macchine virtuali che supportano RDMA in un set di scalabilità di macchine virtuali esistente denominato *myVMSS* distribuito nel gruppo di risorse denominato *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Nelle immagini HPC basate su CentOS gli aggiornamenti del kernel sono disabilitati nel file di configurazione **yum** . Ciò è dovuto al fatto che i driver RDMA Linux vengono distribuiti come pacchetto RPM e gli aggiornamenti dei driver potrebbero non funzionare se il kernel viene aggiornato.
  >
  

* **SUSE Linux Enterprise Server** - SLES 12 SP3 per HPC, SLES 12 SP3 per HPC (Premium), SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium), SLES 12 SP4 e SLES 15. I driver RDMA vengono installati e i pacchetti Intel MPI vengono distribuiti nella VM. Installare MPI con questo comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu Server 16.04 LTS, 18.04 LTS. Configurare i driver RDMA nella VM ed eseguire la registrazione con Intel per scaricare Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Per ulteriori informazioni sull'abilitazione di InfiniBand, impostazione di MPI, vedere [Enable InfiniBand](./workloads/hpc/enable-infiniband.md).

## <a name="other-sizes"></a>Altre dimensioni

- [Scopo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
