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
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e7f6c840be3a284f635114287a69c151f671531d
ms.lasthandoff: 04/03/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Configurare i driver GPU per le VM serie N che eseguono Linux

Per usufruire delle funzionalità per GPU delle macchine virtuali serie N di Azure che eseguono una distribuzione Linux supportata, dopo la distribuzione è necessario installare driver di grafica NVIDIA in ogni macchina virtuale. Le informazioni di configurazione dei driver sono disponibili anche per le [VM Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


> [!IMPORTANT]
> Attualmente, il supporto per GPU Linux è disponibile solo sulle macchine virtuali NC di Azure che eseguono Ubuntu Server 16.04 LTS.
> 

Per conoscere le specifiche, le capacità di archiviazione e i dettagli dei dischi delle macchine virtuali serie N, vedere [Dimensioni delle macchine virtuali](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vedere anche [General considerations for N-series VMs](#general-considerations-for-n-series-vms) (Considerazioni generali per le VM serie N).



## <a name="install-nvidia-cuda-drivers"></a>Installare i driver NVIDIA CUDA

Ecco i passaggi per installare i driver NVIDIA nelle VM NC Linux dal Toolkit 8.0 di NVIDIA CUDA. Gli sviluppatori C++ e C possono facoltativamente installare il toolkit completo per creare applicazioni con accelerazione GPU. Per altre informazioni, vedere la [guida di installazione di CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> I collegamenti ai download dei driver forniti qui sono quelli attivi al momento della pubblicazione. Per i driver più aggiornati, visitare il sito Web di [NVIDIA](http://www.nvidia.com/).

Per installare il toolkit di CUDA, eseguire una connessione SSH a ciascuna VM. Per verificare che nel sistema sia presente una GPU con supporto per core CUDA, eseguire il comando seguente:

```bash
lspci | grep -i NVIDIA
```
Verrà visualizzato un output simile all'esempio seguente (che rappresenta una scheda NVIDIA Tesla K80):

![Output del comando Ispci](./media/n-series-driver-setup/lspci.png)

Quindi eseguire i comandi specifici per la distribuzione.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
L'installazione può richiedere alcuni minuti.

Per installare facoltativamente il toolkit di CUDA completo, digitare:

```bash
sudo apt-get install cuda
```

Riavviare la VM e procedere a verificare l'installazione.

## <a name="verify-driver-installation"></a>Verificare l'installazione del driver


Per controllare lo stato del dispositivo GPU, eseguire una connessione SSH alla VM ed eseguire l'utilità della riga di comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installata con il driver. 

![Stato del dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="cuda-driver-updates"></a>Aggiornamenti dei driver CUDA

È consigliabile aggiornare periodicamente i driver CUDA dopo la distribuzione.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

Al termine dell'aggiornamento, riavviare la VM.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Non è consigliabile installare X Server o altri sistemi che usano il driver nouveau in VM NC Ubuntu. Prima di installare i driver NVIDIA GPU, è necessario disabilitare il driver nouveau.  

* Se si desidera acquisire un'immagine di una VM Linux in cui sono installati driver NVIDIA, vedere [Come generalizzare e acquisire una macchina virtuale Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle GPU NVIDIA nelle macchine virtuali serie N, vedere:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (per VM NC Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (per macchine virtuali NC di Azure)


