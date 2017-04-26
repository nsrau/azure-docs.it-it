---
title: Installazione del driver serie N di Azure per Windows | Microsoft Docs
description: Informazioni su come configurare i driver GPU NVIDIA per le VM serie N che eseguono Windows in Azure
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: 1767a2db05abd3abadfedbef86c38e55c5a57980
ms.lasthandoff: 04/04/2017


---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Configurare i driver GPU NVIDIA per le VM serie N che eseguono Windows Server
Per usufruire delle funzionalità GPU delle VM serie N di Azure che eseguono Windows Server 2016 o Windows Server 2012 R2, è necessario installare i driver della scheda grafica NVIDIA in ciascuna VM dopo la distribuzione. Le informazioni di configurazione dei driver sono disponibili anche per le [VM Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per conoscere le specifiche di base, le capacità di archiviazione e i dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Vedere anche [General considerations for N-series VMs](#general-considerations-for-n-series-vms) (Considerazioni generali per le VM serie N).



## <a name="supported-gpu-drivers"></a>Driver GPU supportati

Connettersi tramite Desktop remoto a ciascuna VM serie N. Scaricare, estrarre e installare il driver supportato per il sistema operativo Windows. 

### <a name="nvidia-tesla-drivers-for-nc-vms-tesla-k80"></a>Driver NVIDIA Tesla per VM NC (Tesla K80)



| OS | Versione del driver |
| -------- |------------- |
| Windows Server 2016 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2016-international-whql.exe) (.exe) |
| Windows Server 2012 R2 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2008-2012r2-64bit-international-whql.exe) (.exe) |

> [!NOTE]
> I collegamenti ai download dei driver Tesla forniti qui sono quelli attivi al momento della pubblicazione. Per i driver più aggiornati, visitare il sito Web di [NVIDIA](http://www.nvidia.com/).
>

### <a name="nvidia-grid-drivers-for-nv-vms-tesla-m60"></a>Driver NVIDIA GRID per VM NV (Tesla M60)

| OS | Versione del driver |
| -------- |------------- |
| Windows Server 2016 | [369.95](https://go.microsoft.com/fwlink/?linkid=836843) (ZIP) |
| Windows Server 2012 R2 | [369.95](https://go.microsoft.com/fwlink/?linkid=836844) (ZIP)  |



## <a name="verify-gpu-driver-installation"></a>Verificare l'installazione del driver GPU

Nelle VM NV Azure, è necessario eseguire il riavvio dopo l'installazione del driver. Nelle VM NC, non è necessario riavviare il sistema.

È possibile verificare l'installazione del driver in Gestione dispositivi. L'esempio seguente illustra la corretta configurazione della scheda Tesla K80 in una VM NC Azure.

![Proprietà del driver GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Per eseguire una query sullo stato del dispositivo GPU, eseguire l'utilità della riga di comando [smi nvidia](https://developer.nvidia.com/nvidia-system-management-interface) installata con il driver. 

![Stato del dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-for-nc24r-vms"></a>Rete RDMA per VM NC24r

La connettività di rete RDMA può essere abilitata nelle VM NC24r distribuite nello stesso set di disponibilità. È necessario aggiungere l'estensione HpcVmDrivers per installare i driver dei dispositivi di rete Windows che consentono la connettività RDMA. Per aggiungere l'estensione VM a una VM NC24r, usare i cmdlet di [Azure PowerShell](/powershell/azureps-cmdlets-docs) per Azure Resource Manager.

> [!NOTE]
> Attualmente solo Windows Server 2012 R2 supporta la rete RDMA nelle VM NC24r.
> 

Per installare l'ultima versione 1.1 dell'estensione HpcVMDrivers in una VM esistente con supporto per RDMA denominata myVM negli Stati Uniti occidentali:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Per altre informazioni, vedere [Estensioni e funzionalità della macchina virtuale per Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

La rete RDMA supporta il traffico Message Passing Interface (MPI) per le applicazioni in esecuzione con [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) o Intel MPI 5. x. 

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle GPU NVIDIA nelle macchine virtuali serie N, vedere:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (per VM NC Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (per macchine virtuali NC di Azure)

* Gli sviluppatori che creano applicazioni con accelerazione GPU per GPU NVIDIA Tesla possono inoltre scaricare e installare il CUDA Toolkit 8 per [Windows Server 2016](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_win10-exe) o [Windows Server 2012 R2](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_windows-exe). Per altre informazioni, vedere la [guida di installazione di CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).



