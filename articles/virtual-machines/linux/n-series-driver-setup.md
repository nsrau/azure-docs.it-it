---
title: Installazione di driver per serie N di Azure per Linux | Microsoft Docs
description: Informazioni su come installare driver GPU NVIDIA per macchine virtuali serie N che eseguono Linux in Azure
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/12/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d353adcafed02832243277118da8480e54544ce
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Installare i driver GPU NVIDIA in VM serie N che eseguono Linux

Per usufruire delle funzionalità GPU delle VM serie N di Azure che eseguono Linux, installare i driver della scheda grafica NVIDIA supportati. Questo articolo descrive la procedura di installazione dei driver dopo la distribuzione di una macchina virtuale serie N. Le informazioni di configurazione dei driver sono disponibili anche per le [VM Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per conoscere le specifiche, le capacità di archiviazione e i dettagli dei dischi delle macchine virtuali serie N, vedere [Dimensioni delle macchine virtuali Linux GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-for-nc-ncv2-ncv3-and-nd-series-vms"></a>Installare i driver CUDA per macchine virtuali Serie NC, NCv2, NCv3 e ND

Di seguito sono indicati i passaggi per installare i driver NVIDIA nelle macchine virtuali Serie N dal Toolkit di NVIDIA CUDA. 

Gli sviluppatori C++ e C possono facoltativamente installare il toolkit completo per creare applicazioni con accelerazione GPU. Per altre informazioni, vedere la [guida di installazione di CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

> [!NOTE]
> I collegamenti ai download dei driver CUDA forniti qui sono quelli attivi al momento della pubblicazione. Per i driver CUDA più aggiornati, visitare il sito Web [NVIDIA](https://developer.nvidia.com/cuda-zone).
>

Per installare il toolkit di CUDA, eseguire una connessione SSH a ciascuna VM. Per verificare che nel sistema sia presente una GPU con supporto per core CUDA, eseguire il comando seguente:

```bash
lspci | grep -i NVIDIA
```
Verrà visualizzato un output simile all'esempio seguente (che rappresenta una scheda NVIDIA Tesla K80):

![Output del comando Ispci](./media/n-series-driver-setup/lspci.png)

Quindi eseguire i comandi di installazione specifici per la distribuzione.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Scaricare e installare i driver CUDA.
  ```bash
  CUDA_REPO_PKG=cuda-repo-ubuntu1604_9.1.85-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  L'installazione può richiedere alcuni minuti.

2. Per installare facoltativamente il toolkit di CUDA completo, digitare:

  ```bash
  sudo apt-get install cuda
  ```

3. Riavviare la VM e procedere a verificare l'installazione.

#### <a name="cuda-driver-updates"></a>Aggiornamenti dei driver CUDA

È consigliabile aggiornare periodicamente i driver CUDA dopo la distribuzione.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux-73-or-74"></a>CentOS o Red Hat Enterprise Linux 7.3 o 7.4

1. Aggiornare il kernel.

  ```
  sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
  sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

  ```bash
  wget https://aka.ms/lis
 
  tar xvzf lis
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. Riconnettersi alla macchina virtuale e continuare l'installazione con i comandi seguenti:

  ```bash
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9.1.85-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  L'installazione può richiedere alcuni minuti. 

4. Per installare facoltativamente il toolkit di CUDA completo, digitare:

  ```bash
  sudo yum install cuda
  ```

5. Riavviare la VM e procedere a verificare l'installazione.

### <a name="verify-driver-installation"></a>Verificare l'installazione del driver

Per controllare lo stato del dispositivo GPU, eseguire una connessione SSH alla VM ed eseguire l'utilità della riga di comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installata con il driver. 

Se il driver è installato, l'output sarà simile al seguente. Si noti che **GPU-Util** mostra 0% a meno che nella macchina virtuale non sia attualmente in esecuzione un carico di lavoro GPU. La versione del driver e i dettagli GPU possono essere diversi da quelli riportati.

![Stato del dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Connettività di rete RDMA

La connettività di rete RDMA può essere abilitata in macchine virtuali serie N abilitate per RDMA, come le macchine virtuali NC24r distribuite nello stesso set di disponibilità o set di scalabilità di macchine virtuali. La rete RDMA supporta il traffico Message Passing Interface (MPI) per le applicazioni in esecuzione con Intel MPI 5.x o una versione più recente. Seguono i requisiti aggiuntivi:

### <a name="distributions"></a>Distribuzioni

Distribuire le macchine virtuali serie N abilitate per RDMA da un'immagine in Azure Marketplace che supporta la connettività RDMA nelle macchine virtuali serie N:
  
* **Ubuntu 16.04 LTS** - Configurare i driver RDMA nella VM ed eseguire la registrazione con Intel per scaricare Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

> [!NOTE]
> Attualmente, le immagini HPC basate su CentOS non sono consigliate per la connettività RDMA nelle macchine virtuali serie N. RDMA non è supportato sul kernel 7.4 CentOS più recente che supporta le GPU NVIDIA.
> 


## <a name="install-grid-drivers-for-nv-series-vms"></a>Installare i driver GRID per le macchine virtuali serie NV

Per installare i driver NVIDIA GRID nelle macchine virtuali serie NV, stabilire una connessione SSH a ogni macchina virtuale e seguire la procedura per la distribuzione di Linux. 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Eseguire il comando `lspci`. Verificare che la scheda o le schede NVIDIA M60 siano visualizzate come dispositivi PCI.

2. Installare gli aggiornamenti.

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Disabilitare il driver del kernel Nouveau, che è incompatibile con il driver NVIDIA. Nelle macchine virtuali NV usare solo il driver NVIDIA. A tale scopo, creare un file in `/etc/modprobe.d ` denominato `nouveau.conf` con il contenuto seguente:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. Riavviare la macchina virtuale ed eseguire nuovamente la connessione. Uscire dal server X:

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. Scaricare e installare il driver GRID:

  ```bash
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
  ``` 

6. Quando viene chiesto se si vuole eseguire l'utilità nvidia-xconfig per aggiornare il file di configurazione di X, selezionare **Yes** (Sì).

7. Al termine dell'installazione, copiare /etc/nvidia/gridd.conf.template in un nuovo file gridd.conf nel percorso /etc/nvidia/

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. Aggiungere quanto segue a `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Riavviare la VM e procedere a verificare l'installazione.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS o Red Hat Enterprise Linux 

1. Aggiornare il kernel e DKMS.
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. Disabilitare il driver del kernel Nouveau, che è incompatibile con il driver NVIDIA. Nelle macchine virtuali NV usare solo il driver NVIDIA. A tale scopo, creare un file in `/etc/modprobe.d ` denominato `nouveau.conf` con il contenuto seguente:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. Riavviare la macchina virtuale e installare i [servizi di integrazione di Linux più recenti per Hyper-V e Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
  ```bash
  wget https://aka.ms/lis

  tar xvzf lis

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. Ristabilire la connessione alla macchina virtuale ed eseguire il comando `lspci`. Verificare che la scheda o le schede NVIDIA M60 siano visualizzate come dispositivi PCI.
 
5. Scaricare e installare il driver GRID:

  ```bash
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
  ``` 
6. Quando viene chiesto se si vuole eseguire l'utilità nvidia-xconfig per aggiornare il file di configurazione di X, selezionare **Yes** (Sì).

7. Al termine dell'installazione, copiare /etc/nvidia/gridd.conf.template in un nuovo file gridd.conf nel percorso /etc/nvidia/
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. Aggiungere quanto segue a `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Riavviare la VM e procedere a verificare l'installazione.

### <a name="verify-driver-installation"></a>Verificare l'installazione del driver


Per controllare lo stato del dispositivo GPU, eseguire una connessione SSH alla VM ed eseguire l'utilità della riga di comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installata con il driver. 

Se il driver è installato, l'output sarà simile al seguente. Si noti che **GPU-Util** mostra 0% a meno che nella macchina virtuale non sia attualmente in esecuzione un carico di lavoro GPU. La versione del driver e i dettagli GPU possono essere diversi da quelli riportati.

![Stato del dispositivo NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Server X11
Se è necessario un server X11 per le connessioni remote a una macchina virtuale NV, è consigliabile usare [x11vnc](http://www.karlrunge.com/x11vnc/) perché consente l'accelerazione hardware della grafica. Il BusID del dispositivo M60 deve essere aggiunto manualmente al file xconfig (`etc/X11/xorg.conf` in Ubuntu 16.04 LTS, `/etc/X11/XF86config` in CentOS 7.3 o Red Hat Enterprise Server 7.3). Aggiungere una sezione `"Device"` simile alla seguente:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "your-BusID:0:0:0"
EndSection
```
 
Aggiornare anche la sezione `"Screen"` per l'uso del dispositivo.
 
Per individuare il BusID, eseguire

```bash
/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1
```
 
Il BusID può cambiare quando una macchina virtuale viene riallocata o riavviata. Pertanto, è consigliabile usare uno script per aggiornare il BusID nella configurazione di X11 quando una macchina virtuale viene riavviata. Ad esempio: 

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

Questo file può essere richiamato come radice all'avvio mediante la creazione di una voce in `/etc/rc.d/rc3.d`.

## <a name="troubleshooting"></a>risoluzione dei problemi

* È possibile impostare la modalità di persistenza tramite `nvidia-smi`. In questo modo l'output del comando sarà più veloce per l'esecuzione di query sulle schede. Per impostare la modalità di persistenza, eseguire `nvidia-smi -pm 1`. Si noti che se la macchina virtuale viene riavviata, l'impostazione della modalità scompare. È sempre possibile generare script che impostino la modalità affinché venga eseguita all'avvio.

## <a name="next-steps"></a>Passaggi successivi

* Se si intende acquisire un'immagine di una VM Linux in cui sono installati driver NVIDIA, vedere [Come generalizzare e acquisire una macchina virtuale Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
