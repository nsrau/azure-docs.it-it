---
title: Installazione del driver serie N di Azure per Windows | Microsoft Docs
description: Informazioni su come configurare i driver GPU NVIDIA per le VM serie N che eseguono Windows in Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e21115915808899e8455dc6d4f1f4cf8a100f483
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Configurare i driver GPU NVIDIA per le VM serie N che eseguono Windows Server
Per usufruire delle funzionalità GPU delle VM serie N di Azure che eseguono Windows Server 2016 o Windows Server 2012 R2, installare i driver della scheda grafica NVIDIA supportati. Questo articolo descrive la procedura di installazione dei driver dopo la distribuzione di una macchina virtuale serie N. Le informazioni di configurazione dei driver sono disponibili anche per le [VM Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per conoscere le specifiche base, le capacità di archiviazione e i dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Installazione del driver

1. Connettersi tramite Desktop remoto a ciascuna VM serie N.

2. Scaricare, estrarre e installare il driver supportato per il sistema operativo Windows.

Nelle VM NV Azure, è necessario eseguire il riavvio dopo l'installazione del driver. Nelle VM NC, non è necessario riavviare il sistema.

## <a name="verify-driver-installation"></a>Verificare l'installazione del driver

È possibile verificare l'installazione del driver in Gestione dispositivi. L'esempio seguente illustra la corretta configurazione della scheda Tesla K80 in una VM NC Azure.

![Proprietà del driver GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Per eseguire una query sullo stato del dispositivo GPU, eseguire l'utilità della riga di comando [smi nvidia](https://developer.nvidia.com/nvidia-system-management-interface) installata con il driver.

1. Aprire un prompt dei comandi e passare alla directory **C:\Programmi\NVIDIA Corporation\NVSMI**.

2. Eseguire `nvidia-smi`. Se il driver è installato, l'output sarà simile al seguente. Si noti che **GPU-Util** mostra **0%** a meno che nella macchina virtuale non sia attualmente in esecuzione un carico di lavoro GPU. La versione del driver e i dettagli GPU possono essere diversi da quelli riportati.

![Stato del dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Connettività di rete RDMA

La connettività di rete RDMA può essere abilitata in macchine virtuali serie N abilitate per RDMA, come le macchine virtuali NC24r distribuite nello stesso set di disponibilità o set di scalabilità di macchine virtuali. È necessario aggiungere l'estensione HpcVmDrivers per installare i driver dei dispositivi di rete Windows che consentono la connettività RDMA. Per aggiungere l'estensione di VM a una macchina virtuale serie N abilitata per RDMA, usare i cmdlet di [Azure PowerShell](/powershell/azure/overview) per Azure Resource Manager.

Per installare l'ultima versione 1.1 dell'estensione HpcVMDrivers in una VM esistente con supporto per RDMA denominata myVM negli Stati Uniti occidentali:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Per altre informazioni, vedere [Estensioni e funzionalità della macchina virtuale per Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

La rete RDMA supporta il traffico Message Passing Interface (MPI) per le applicazioni in esecuzione con [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) o Intel MPI 5. x. 


## <a name="next-steps"></a>Passaggi successivi

* Gli sviluppatori che creano applicazioni con accelerazione GPU per GPU NVIDIA Tesla possono inoltre scaricare e installare il [CUDA Toolkit 9.1](https://developer.nvidia.com/cuda-downloads). Per altre informazioni, vedere la [guida di installazione di CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


