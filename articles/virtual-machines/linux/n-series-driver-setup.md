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
ms.date: 11/06/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 46f8b2c20d9ce31ef3f782d098de09952701bbcc
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2017
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Installare i driver GPU NVIDIA in VM serie N che eseguono Linux

Per usufruire delle funzionalità GPU delle VM serie N di Azure che eseguono Linux, installare i driver della scheda grafica NVIDIA supportati. Questo articolo descrive la procedura di installazione dei driver dopo la distribuzione di una macchina virtuale serie N. Le informazioni di configurazione dei driver sono disponibili anche per le [VM Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


Per conoscere le specifiche, le capacità di archiviazione e i dettagli dei dischi delle macchine virtuali serie N, vedere [Dimensioni delle macchine virtuali Linux GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-grid-drivers-for-nv-vms"></a>Installare i driver GRID per VM NV

Per installare i driver NVIDIA GRID nelle macchine virtuali NV, stabilire una connessione SSH a ogni macchina virtuale e seguire la procedura per la distribuzione di Linux. 

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
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
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


### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>Sistema operativo Linux basato su CentOS 7.3 o Red Hat Enterprise 7.3

> [!IMPORTANT]
> Non eseguire `sudo yum update` per aggiornare la versione del kernel in CentOS 7.3 o Red Hat Enterprise Linux 7.3. L'installazione e gli aggiornamenti dei driver attualmente non funzionano se il kernel viene aggiornato.
>

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
 
3. Riavviare la macchina virtuale e installare i servizi di integrazione di Linux più recenti per Hyper-V:
 
  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3.tar.gz

  tar xvzf lis-rpms-4.2.3.tar.gz

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. Ristabilire la connessione alla macchina virtuale ed eseguire il comando `lspci`. Verificare che la scheda o le schede NVIDIA M60 siano visualizzate come dispositivi PCI.
 
5. Scaricare e installare il driver GRID:

  ```bash
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
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

Verrà visualizzato un output simile al seguente:

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
 
Il BusID può cambiare quando una macchina virtuale viene riallocata o riavviata. Pertanto, è consigliabile usare uno script per aggiornare il BusID nella configurazione di X11 quando una macchina virtuale viene riavviata. ad esempio:

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

Questo file può essere richiamato come radice all'avvio mediante la creazione di una voce in `/etc/rc.d/rc3.d`.


## <a name="install-cuda-drivers-for-nc-vms"></a>Installare i driver CUDA per macchine virtuali NC

Di seguito sono indicati i passaggi per installare i driver NVIDIA nelle VM NC Linux dal Toolkit di NVIDIA CUDA. 

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
  CUDA_REPO_PKG=cuda-9-0_9.0.176-1_amd64.deb

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

### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>Sistema operativo Linux basato su CentOS 7.3 o Red Hat Enterprise 7.3

1. Installare la versione più recente di Linux Integration Services per Hyper-V.

  > [!IMPORTANT]
  > Se è installata un'immagine HPC basata su CentOS in una macchina virtuale NC24r, andare al passaggio 3. Poiché i driver RDMA di Azure e Linux Integration Services sono preinstallati nell'immagine HPC, non è necessario aggiornare i servizi LIS e gli aggiornamenti del kernel sono disabilitati per impostazione predefinita.
  >

  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-1.tar.gz
 
  tar xvzf lis-rpms-4.2.3-1.tar.gz
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. Riconnettersi alla macchina virtuale e continuare l'installazione con i comandi seguenti:

  ```bash
  sudo yum install kernel-devel

  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9.0.176-1.x86_64.rpm

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

Verrà visualizzato un output simile al seguente:

![Stato del dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)



## <a name="rdma-network-for-nc24r-vms"></a>Rete RDMA per VM NC24r

La connettività di rete RDMA può essere abilitata nelle VM NC24r distribuite nello stesso set di disponibilità. La rete RDMA supporta il traffico Message Passing Interface (MPI) per le applicazioni in esecuzione con Intel MPI 5.x o una versione più recente. Seguono i requisiti aggiuntivi:

### <a name="distributions"></a>Distribuzioni

Distribuire VM NC24r da una delle immagini seguenti in Azure Marketplace che supporta la connettività RDMA:
  
* **Ubuntu**: Ubuntu Server 16.04 LTS. Configurare i driver RDMA nella VM ed eseguire la registrazione con Intel per scaricare Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **HPC basato su CentOS**: HPC 7.3 basato su CentOS. I driver RDMA e Intel MPI 5.1 vengono installati nella VM. 


## <a name="troubleshooting"></a>Risoluzione dei problemi

* Esiste un problema noto con i driver CUDA sulle macchine virtuali serie N di Azure che eseguono il kernel Linux 4.4.0-75 su Ubuntu 16.04 LTS. Se si usa una versione precedente del kernel, eseguire l'aggiornamento almeno alla versione 4.4.0-77.

* È possibile impostare la modalità di persistenza tramite nvidia-smi; in questo modo l'output del comando sarà più veloce per l'esecuzione di query sulle schede. Per impostare la modalità di persistenza, eseguire `nvidia-smi -pm 1`. Si noti che se la VM viene riavviata, l'impostazione della modalità scomparirà. È sempre possibile generare script che impostino la modalità affinché venga eseguita all'avvio.


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle GPU NVIDIA nelle macchine virtuali serie N, vedere:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (per VM NC Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (per macchine virtuali NC di Azure)

* Se si intende acquisire un'immagine di una VM Linux in cui sono installati driver NVIDIA, vedere [Come generalizzare e acquisire una macchina virtuale Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
