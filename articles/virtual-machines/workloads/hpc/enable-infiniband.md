---
title: Abilitare InifinBand con SR-IOV - Macchine virtuali di Azure Documenti Microsoft
description: Scopri come abilitare InfiniBand con SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196774"
---
# <a name="enable-infiniband-with-sr-iov"></a>Abilita InfiniBand con SR-IOV

Le serie di macchine virtuali Azure NC, ND e H sono tutte supportate da una rete InfiniBand dedicata. Tutte le dimensioni abilitate per RDMA sono in grado di sfruttare tale rete utilizzando Intel MPI. Alcune serie di MACCHINE virtuali hanno ampliato il supporto per tutte le implementazioni MPI e i verbi RDMA tramite SR-IOV. Le macchine virtuali compatibili con RDMA includono macchine virtuali [HPC (GPU ottimizzate](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) e [HPC)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) ottimizzate per GPU.

## <a name="choose-your-installation-path"></a>Scegliere il percorso di installazione

Per iniziare, l'opzione più semplice consiste nell'utilizzare un'immagine della piattaforma preconfigurata per InfiniBand, se disponibile:To get started, the simplest option is to use a platform image pre-configured for InfiniBand, where available:

- **Macchine virtuali HPC IaaS** – Per iniziare a utilizzare le macchine virtuali IaaS per HPC, la soluzione più semplice consiste nell'utilizzare l'immagine del sistema [operativo COS-HPC 7.6 VM](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), già configurata con InfiniBand. Poiché questa immagine è già configurata con InfiniBand, non è necessario configurarla manualmente. Per le versioni compatibili di Windows, vedere [Istanze che supportano Windows RDMA.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

- **Macchine virtuali GPU IaaS:** al momento nessuna immagine della piattaforma è preconfigurata per le macchine virtuali ottimizzate GPU, ad eccezione dell'immagine del sistema [operativo VM CentOS-HPC 7.6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Per configurare un'immagine personalizzata con InfiniBand, vedere Installazione manuale di [Mellanox OFED](#manually-install-mellanox-ofed).

Se si usa un'immagine di macchina virtuale personalizzata o una macchina virtuale [ottimizzata per GPU,](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) è consigliabile configurarla con InfiniBand aggiungendo l'estensione della macchina virtuale InfiniBandDriverLinux o InfiniBandDriverWindows alla distribuzione. Informazioni su come usare queste estensioni VM con [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) e [Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

## <a name="manually-install-mellanox-ofed"></a>Installare manualmente Mellanox OFED

Per configurare manualmente InfiniBand con SR-IOV, attenersi alla seguente procedura. Nell'esempio seguente viene illustrata la sintassi per RHEL/CentOS, ma i passaggi sono generali e possono essere utilizzati per qualsiasi sistema operativo compatibile, ad esempio Ubuntu (16.04, 18.04 19.04) e SLES (12 SP4 e 15). Anche i driver della posta in arrivo funzionano, ma i driver Mellanox OpenFabrics forniscono più funzionalità.

Per ulteriori informazioni sulle distribuzioni supportate per il driver Mellanox, vedere i [driver Mellanox OpenFabrics](https://www.mellanox.com/page/products_dyn?product_family=26)più recenti. Per ulteriori informazioni sul driver Mellanox OpenFabrics, consultare la [Guida per l'utente di Mellanox](https://docs.mellanox.com/category/mlnxofedib).

Vedere l'esempio seguente per informazioni su come configurare InfiniBand su Linux:See the following example for how to configure InfiniBand on Linux:

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

Per Windows, scaricare e installare i [driver Mellanox OFED per Windows.](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ip-over-infiniband"></a>Abilita IP su InfiniBand

Utilizzare i comandi seguenti per abilitare IP su InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su HPC in Azure.Learn more about [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) on Azure.
