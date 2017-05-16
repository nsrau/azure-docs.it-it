---
title: Installazione di driver per serie N di Azure per Linux | Microsoft Docs
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
ms.date: 05/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 181428e5302c5c8f5b72f06d6c54b0f87802690a
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Configurare i driver GPU per le VM serie N che eseguono Linux

Per usufruire delle funzionalità GPU delle VM serie N di Azure che eseguono Linux, è necessario installare i driver della scheda grafica NVIDIA in ogni VM. Questo articolo descrive la procedura di installazione dei driver dopo la distribuzione di una macchina virtuale serie N. Le informazioni di configurazione dei driver sono disponibili anche per le [VM Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


Per conoscere le specifiche, le capacità di archiviazione e i dettagli dei dischi delle macchine virtuali serie N, vedere [Dimensioni delle macchine virtuali Linux GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



## <a name="supported-distributions-and-drivers"></a>Driver e distribuzioni supportati

> [!IMPORTANT]
> Il supporto per GPU Linux al momento è disponibile solo sulle macchine virtuali NC di Azure. 

Per eseguire i driver di grafica NVIDIA in macchine virtuali Linux serie N, sono supportate le distribuzioni seguenti da Azure Marketplace.

### <a name="nc-vms-tesla-k80-card"></a>Macchine virtuali NC (scheda Tesla K80)
* Ubuntu 16.04 LTS 
* Red Hat Enterprise Linux 7.3 
* CentOS-based 7.3 

**Driver supportati**: NVIDIA CUDA 8.0, driver branch R375. [Procedura di installazione](#install-CUDA-drivers-for-NC-VMs)




> [!WARNING] 
> L'installazione di software di terze parti in prodotti Red Hat può influenzare i termini di supporto di Red Hat. Vedere l'[articolo della Knowledge base di Red Hat](https://access.redhat.com/articles/1067).
>




## <a name="install-cuda-drivers-for-nc-vms"></a>Installare i driver CUDA per macchine virtuali NC

Ecco i passaggi per installare i driver NVIDIA nelle VM NC Linux dal Toolkit 8.0 di NVIDIA CUDA. 

Gli sviluppatori C++ e C possono facoltativamente installare il toolkit completo per creare applicazioni con accelerazione GPU. Per altre informazioni, vedere la [guida di installazione di CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> I collegamenti ai download dei driver CUDA forniti qui sono quelli attivi al momento della pubblicazione. Per i driver più aggiornati, visitare il sito Web di [NVIDIA](http://www.nvidia.com/).
>

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

### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 o Red Hat Enterprise Linux 7.3

> [!IMPORTANT] 
> A causa di un problema noto, non si riesce a installare il driver NVIDIA CUDA nelle macchine virtuali NC24r che eseguono CentOS 7.3 o Red Hat Enterprise Linux 7.3.
>

Ottenere prima gli aggiornamenti. 

```bash
sudo yum update

sudo reboot
```

Riconnettersi alla macchina virtuale e continuare l'installazione con i comandi seguenti:

```bash
sudo yum install kernel-devel

sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

sudo yum install dkms

CUDA_REPO_PKG=cuda-repo-rhel7-8.0.61-1.x86_64.rpm

wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo yum install cuda-drivers
```

L'installazione può richiedere alcuni minuti. Per installare facoltativamente il toolkit di CUDA completo, digitare:

```bash
sudo yum install cuda
```

Riavviare la VM e procedere a verificare l'installazione.


### <a name="verify-driver-installation"></a>Verificare l'installazione del driver


Per controllare lo stato del dispositivo GPU, eseguire una connessione SSH alla VM ed eseguire l'utilità della riga di comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installata con il driver. 

Verrà visualizzato un output simile al seguente:

![Stato del dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)


### <a name="cuda-driver-updates"></a>Aggiornamenti dei driver CUDA

È consigliabile aggiornare periodicamente i driver CUDA dopo la distribuzione.

#### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

Al termine dell'aggiornamento, riavviare la VM.

#### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 o Red Hat Enterprise Linux 7.3

```bash
sudo yum update
```

Al termine dell'aggiornamento, riavviare la VM.



## <a name="troubleshooting"></a>Risoluzione dei problemi

* Esiste un problema noto con i driver CUDA sulle macchine virtuali serie N di Azure che eseguono il kernel Linux 4.4.0-75 su Ubuntu 16.04 LTS. Per mantenere il funzionamento del driver quando si aggiorna il kernel, eseguire l'aggiornamento del kernel almeno alla versione 4.4.0-77. 



## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle GPU NVIDIA nelle macchine virtuali serie N, vedere:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (per VM NC Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (per macchine virtuali NC di Azure)

* Se si intende acquisire un'immagine di una VM Linux in cui sono installati driver NVIDIA, vedere [Come generalizzare e acquisire una macchina virtuale Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
