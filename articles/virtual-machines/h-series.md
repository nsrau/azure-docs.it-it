---
title: Serie H-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie H.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: jushiman
ms.openlocfilehash: 34f89656fc56b27edafeff0e60874a00139266dd
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87268952"
---
# <a name="h-series"></a>Serie H

Le macchine virtuali serie H sono ottimizzate per le applicazioni basate su frequenze di CPU elevate o per i requisiti di memoria di grandi dimensioni. Le macchine virtuali serie H dispongono di core del processore Intel Xeon E5 2667 V3, fino a 14 GB di RAM per core CPU e senza hyperthreading. Le funzionalità della serie H 56 GB/sec Mellanox FDR InfiniBand in una configurazione di albero FAT non bloccante per prestazioni RDMA coerenti. Le macchine virtuali serie H supportano Intel MPI 5. x e MS-MPI.

ACU: 290-300

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium: non supportata

Live Migration: Non supportato

Manutenzione con mantenimento della memoria: Non supportato

| Dimensione | vCPU | Processore | Memoria (GB) | Larghezza di banda di memoria (GB/sec) | Frequenza CPU di base (GHz) | Frequenza di tutti i core (GHz, picco) | Frequenza a core singolo (GHz, picco) | Prestazioni RDMA (GB/sec) | Supporto MPI | Archiviazione temporanea (GB) | Numero massimo di dischi dati | Velocità effettiva del disco max: IOPS | NIC Ethernet max |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 V3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 V3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 V3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 V3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 V3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 V3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> per le applicazioni MPI, la rete back-end RDMA dedicata viene abilitata dalla rete InfiniBand FDR.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Immagini del sistema operativo supportate (Linux)
 
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

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Per ulteriori informazioni sull'abilitazione di InfiniBand, sulla configurazione di MPI, vedere [Enable InfiniBand](./workloads/hpc/enable-infiniband.md).

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
