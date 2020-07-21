---
title: Abilitare InifinBand con SR-IOV-macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come abilitare InfiniBand con SR-IOV.
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
ms.openlocfilehash: de61403b62f80bea7872d5ab3561567ae2109590
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500069"
---
# <a name="enable-infiniband-with-sr-iov"></a>Abilitare InfiniBand con SR-IOV

Le macchine virtuali di Azure, ND e serie H sono supportate da una rete InfiniBand dedicata. Tutte le dimensioni abilitate per RDMA sono in grado di sfruttare la rete con Intel MPI. Alcune serie di VM hanno esteso il supporto per tutte le implementazioni MPI e i verbi RDMA tramite SR-IOV. Le macchine virtuali con supporto per RDMA includono VM [ottimizzate per GPU](../../sizes-gpu.md) e [HPC (High Performance COMPUTE)](../../sizes-hpc.md) .

## <a name="choose-your-installation-path"></a>Scegliere il percorso di installazione

Per iniziare, l'opzione più semplice consiste nell'usare un'immagine della piattaforma preconfigurata per InfiniBand, se disponibile:

- **VM IaaS HPC** : per iniziare a usare VM IaaS per HPC, la soluzione più semplice consiste nell'usare l' [immagine del sistema operativo della VM CentOS-HPC 7,6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), che è già configurata con InfiniBand. Poiché questa immagine è già stata configurata con InfiniBand, non è necessario configurarla manualmente. Per le versioni di Windows compatibili, vedere istanze con supporto per [RDMA di Windows](../../sizes-hpc.md#rdma-capable-instances).

- **VM IaaS GPU** : nessuna immagine della piattaforma è attualmente preconfigurata per le macchine virtuali con ottimizzazione GPU, tranne che per l' [immagine del sistema operativo della VM CentOS-HPC 7,6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Per configurare un'immagine personalizzata con InfiniBand, vedere [installazione manuale di MELLANOX OFED](#manually-install-mellanox-ofed).

Se si usa un'immagine di macchina virtuale personalizzata o una VM [ottimizzata](../../sizes-gpu.md) per la GPU, è necessario configurarla con InfiniBand aggiungendo l'estensione della macchina virtuale InfiniBandDriverLinux o InfiniBandDriverWindows alla distribuzione. Informazioni su come usare queste estensioni VM con [Linux](../../sizes-hpc.md#rdma-capable-instances) e [Windows](../../sizes-hpc.md#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Installare manualmente Mellanox OFED

Per configurare manualmente InfiniBand con SR-IOV, seguire questa procedura. L'esempio in questa procedura mostra la sintassi per RHEL/CentOS, ma i passaggi sono generali e possono essere usati per qualsiasi sistema operativo compatibile, ad esempio Ubuntu (16,04, 18,04 19,04) e SLES (12 SP4 e 15). I driver della posta in arrivo funzionano anche, ma i driver Mellanox OpenFabrics forniscono altre funzionalità.

Per ulteriori informazioni sulle distribuzioni supportate per il driver Mellanox, vedere i driver più recenti di [Mellanox OpenFabrics](https://www.mellanox.com/page/products_dyn?product_family=26). Per ulteriori informazioni sul driver Mellanox OpenFabrics, vedere il [manuale dell'utente di Mellanox](https://docs.mellanox.com/category/mlnxofedib).

Vedere l'esempio seguente per informazioni su come configurare InfiniBand in Linux:

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

Per Windows, scaricare e installare [MELLANOX OFED per i driver di Windows](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>Abilita IP over InfiniBand

Usare i comandi seguenti per abilitare IP over InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su [HPC](/azure/architecture/topics/high-performance-computing/) in Azure.
