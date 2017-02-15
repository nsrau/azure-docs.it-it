---
title: Installazione del driver serie N di Azure per Windows | Documentazione Microsoft
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
ms.date: 11/30/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4bf470737f961219250c37d890ed81eb62409626
ms.openlocfilehash: feb9370f0241fd860749d32f5db5842cb18463f1


---
# <a name="set-up-gpu-drivers-for-n-series-vms"></a>Configurare i driver GPU per le VM serie N
Per usufruire delle funzionalità GPU delle VM serie N di Azure che eseguono Windows Server, è necessario installare i driver della scheda grafica NVIDIA in ciascuna VM dopo la distribuzione. Questo articolo è disponibile anche per le [VM Linux](virtual-machines-linux-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per conoscere le specifiche di base, le capacità di archiviazione e i dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




## <a name="supported-gpu-drivers"></a>Driver GPU supportati

Connettersi tramite Desktop remoto a ciascuna VM serie N. Scaricare, estrarre e installare il driver supportato per il sistema operativo Windows. 

### <a name="nvidia-grid-drivers-for-nv-vms"></a>Driver NVIDIA GRID per VM NV

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836843) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836844) (.zip)

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>Driver NVIDIA Tesla per VM NC

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836841) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836842) (.zip)



## <a name="verify-driver-installation"></a>Verificare l'installazione del driver

Nelle VM NV Azure, è necessario eseguire il riavvio dopo l'installazione del driver. Nelle VM NC, non è necessario riavviare il sistema.

È possibile verificare l'installazione del driver in Gestione dispositivi. L'esempio seguente illustra la corretta configurazione della scheda K80 in una VM NC Azure.

![Proprietà del driver GPU](./media/virtual-machines-windows-n-series-driver-setup/GPU_driver_properties.png)

Per eseguire una query sullo stato del dispositivo GPU, eseguire l'utilità della riga di comando [smi nvidia](https://developer.nvidia.com/nvidia-system-management-interface) installata con il driver. 

![Stato dei dispositivi NVIDIA](./media/virtual-machines-windows-n-series-driver-setup/smi.png)  

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulle GPU NVIDIA nelle VM serie N, vedere:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (per VM NC Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (per VM NV Azure)

* Gli sviluppatori che creano applicazioni con accelerazione GPU per GPU NVIDIA Tesla possono inoltre scaricare e installare il [CUDA Toolkit 8](https://developer.nvidia.com/cuda-downloads).





<!--HONumber=Dec16_HO2-->


