---
title: Abilitare InifinBand con SR-IOV - macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come abilitare InfiniBand con SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 879b1eed7bf4778d4d49f6f991d6d74214d33823
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537655"
---
# <a name="enable-infiniband-with-sr-iov"></a>Abilitare InfiniBand con SR-IOV


Il modo più semplice e consigliato per configurare l'immagine di macchina virtuale personalizzata con InfiniBand (IB) consiste nell'aggiungere l'estensione VM InfiniBandDriverWindows o InfiniBandDriverLinux alla distribuzione.
Informazioni su come usare queste estensioni di macchina virtuale con [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

Per configurare manualmente InfiniBand su SR-IOV abilitato le macchine virtuali (attualmente serie HB e connessione ibrida), seguire questa procedura. Questi passaggi sono solo per RHEL/CentOS. Per Ubuntu (16.04 e 18.04) e SLES (12 SP4 e 15), i driver della posta in arrivo funzionano correttamente. Per Ubuntu, 


## <a name="manually-install-ofed"></a>Installare manualmente OFED

Installare i driver più recenti MLNX_OFED per ConnectX-5 dal [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26).

Per RHEL/CentOS (esempio di seguito per 7.6):
```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Per Windows, scaricare e installare i driver WinOF-2 per ConnectX-5 da [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ipoib"></a>Abilitare IPoIB

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>Assegnare un indirizzo IP

Assegnare un indirizzo IP all'interfaccia di ib0, utilizzando:

- Assegnare manualmente indirizzi IP all'interfaccia ib0 (come radice).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

Oppure

- Usare WALinuxAgent per assegnare l'indirizzo IP e renderli persistenti.

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
