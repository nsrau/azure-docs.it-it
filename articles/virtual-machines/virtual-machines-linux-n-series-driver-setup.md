---
title: Installazione di driver per serie N di Azure per Linux | Documentazione Microsoft
description: Informazioni su come installare driver GPU NVIDIA per macchine virtuali serie N che eseguono Linux in Azure
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 0d7eba02757fb1b2263cf11c561b374eab837f21
ms.lasthandoff: 03/03/2017


---
# <a name="set-up-gpu-drivers-for-n-series-linux-vms"></a>Installare driver GPU per macchine virtuali Linux serie N
Per usufruire delle funzionalità per GPU delle macchine virtuali serie N di Azure che eseguono una distribuzione Linux supportata, dopo la distribuzione è necessario installare driver di grafica NVIDIA in ogni macchina virtuale. Questo articolo è disponibile anche per le [VM Windows](virtual-machines-windows-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per conoscere le specifiche, le capacità di archiviazione e i dettagli dei dischi delle macchine virtuali serie N, vedere [Dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).



## <a name="supported-gpu-drivers"></a>Driver GPU supportati


> [!NOTE]
> Attualmente, il supporto per GPU Linux è disponibile solo sulle macchine virtuali NC di Azure che eseguono Ubuntu Server 16.04 LTS.

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>Driver NVIDIA Tesla per macchine virtuali NC

* [Ubuntu 16.04 LTS](https://go.microsoft.com/fwlink/?linkid=836899) (file di installazione autoestraente con estensione RUN)

## <a name="tesla-driver-installation-on-ubuntu-1604-lts"></a>Installazione del driver Tesla in Ubuntu 16.04 LTS

1. Stabilire una connessione SSH con la macchina virtuale serie N di Azure.

2. Per verificare che nel sistema sia presente una GPU con supporto per core CUDA, eseguire il comando seguente:

    ```bash
    lspci | grep -i NVIDIA
    ```
    Verrà visualizzato un output simile all'esempio seguente (che rappresenta una scheda NVIDIA Tesla K80):

    ![Output del comando Ispci](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

3. Scaricare il file con estensione RUN relativo al driver per la propria distribuzione. Il comando di esempio seguente consente di scaricare il driver Ubuntu 16.04 LTS Tesla nella directory /tmp:

    ```bash
    wget -O /tmp/NVIDIA-Linux-x86_64-367.48.run https://go.microsoft.com/fwlink/?linkid=836899
    ```

4. Se nel sistema è necessario installare `gcc` e `make` (obbligatori per i driver Tesla), digitare quanto segue:

    ```bash
    sudo apt-get update
    
    sudo apt install gcc

    sudo apt install make
    ```

4. Passare alla directory contenente il file di installazione del driver ed eseguire comandi simili al seguente:

    ```bash
    chmod +x NVIDIA-Linux-x86_64-367.48.run
    
    sudo sh ./NVIDIA-Linux-x86_64-367.48.run
    ```

## <a name="verify-driver-installation"></a>Verificare l'installazione del driver


Per controllare lo stato del dispositivo GPU, eseguire l'utilità della riga di comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installata con il driver. 

![Stato del dispositivo NVIDIA](./media/virtual-machines-linux-n-series-driver-setup/smi.png)

## <a name="optional-installation-of-nvidia-cuda-toolkit-on-ubuntu-1604-lts"></a>Installazione facoltativa di NVIDIA CUDA Toolkit in Ubuntu 16.04 LTS

Facoltativamente, è possibile installare NVIDIA CUDA Toolkit 8.0 in macchine virtuali NC che eseguono Ubuntu 16.04 LTS. Oltre a driver GPU, il toolkit offre anche un ambiente di sviluppo completo per gli sviluppatori di C e C++ che creano applicazioni con accelerazione GPU.

Per installare il toolkit CUDA, eseguire comandi simili ai seguenti:

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.44-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers
```

L'installazione può richiedere alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle GPU NVIDIA nelle macchine virtuali serie N, vedere:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (per VM NC Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (per macchine virtuali NC di Azure)


