---
title: Abilitare InifinBand nelle VM HPC-macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come abilitare InfiniBand nelle VM HPC di Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/01/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 88f1c120ac4578e077e1c51f59bcaf53b1de2083
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87538898"
---
# <a name="enable-infiniband"></a>Abilitare InfiniBand

Le macchine virtuali serie [H](../../sizes-hpc.md) e serie [N](../../sizes-gpu.md) con supporto per [RDMA](../../sizes-hpc.md#rdma-capable-instances) comunicano attraverso la rete InfiniBand a bassa latenza e larghezza di banda elevata. La funzionalità RDMA su tale interconnessione è fondamentale per migliorare la scalabilità e le prestazioni dei carichi di lavoro HPC e AI del nodo distribuito. Le macchine virtuali serie H e serie N abilitate per InfiniBand sono connesse in un albero FAT non bloccante con una progettazione di basso diametro per prestazioni RDMA ottimizzate e coerenti.

Esistono diversi modi per abilitare InfiniBand sulle dimensioni delle macchine virtuali in grado di supportare.

## <a name="vm-images-with-infiniband-drivers"></a>Immagini di VM con driver InfiniBand
Vedere [Immagini di VM](configure.md#vm-images) per un elenco di immagini di VM supportate nel Marketplace, che precaricano i driver InfiniBand (per VM SR-IOV o non SR-IOV) oppure possono essere configurati con i driver appropriati.
Per le [macchine virtuali](../../sizes-hpc.md#rdma-capable-instances)abilitate per SR-IOV abilitate per RDMA, [CentOS-HPC versione 7,6 o una versione successiva](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) le immagini di VM nel Marketplace rappresentano il modo più semplice per iniziare.
Le immagini di macchine virtuali Ubuntu possono essere configurate con i driver corretti per le VM abilitate per SR-IOV e non SR-IOV usando le istruzioni riportate [qui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351).

## <a name="infiniband-driver-vm-extensions"></a>Estensioni macchina virtuale driver InfiniBand
In Linux l' [estensione della macchina virtuale InfiniBandDriverLinux](../../extensions/hpc-compute-infiniband-linux.md) può essere usata per installare i driver OFED di Mellanox e abilitare InfiniBand sulle VM serie H e N abilitate per SR-IOV.

In Windows, l' [estensione della macchina virtuale InfiniBandDriverWindows](../../extensions/hpc-compute-infiniband-windows.md) installa i driver di rete diretta di Windows (in VM non SR-IOV) o i driver OFED di Mellanox (in VM SR-IOV) per la connettività RDMA. In alcune distribuzioni di istanze A8 e A9 l'estensione HpcVmDrivers viene aggiunta automaticamente. Si noti che l'estensione della macchina virtuale HpcVmDrivers è deprecata. non verrà aggiornata.

Per aggiungere l'estensione macchina virtuale a una macchina virtuale, è possibile usare i cmdlet di [Azure PowerShell](/powershell/azure/). Per altre informazioni, vedere [Estensioni e funzionalità della macchina virtuale](../../extensions/overview.md). È anche possibile usare estensioni delle macchine virtuali nel [modello di distribuzione classico](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

## <a name="manual-installation"></a>Installazione manuale
[I driver Mellanox OpenFabrics (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) possono essere installati manualmente nelle macchine virtuali serie [H](../../sizes-hpc.md) [abilitate per SR-IOV](../../sizes-hpc.md#rdma-capable-instances) e [serie N](../../sizes-gpu.md) .

### <a name="linux"></a>Linux
I [driver OFED per Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) possono essere installati con l'esempio seguente. Sebbene l'esempio sia per RHEL/CentOS, ma i passaggi sono generali e possono essere usati per qualsiasi sistema operativo Linux compatibile, ad esempio Ubuntu (16,04, 18,04 19,04, 20,04) e SLES (12 SP4 e 15). I driver della posta in arrivo funzionano anche, ma i driver Mellanox OFED forniscono altre funzionalità.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optinally verify checksum
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
Per Windows, scaricare e installare [MELLANOX OFED per i driver di Windows](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2).

## <a name="enable-ip-over-infiniband-ib"></a>Abilita IP over InfiniBand (IB)
Se si prevede di eseguire processi MPI, in genere non è necessario IPoIB. La libreria MPI utilizzerà l'interfaccia dei verbi per la comunicazione IB (a meno che non si usi in modo esplicito il canale TCP/IP della libreria MPI). Tuttavia, se si dispone di un'app che usa TCP/IP per la comunicazione e si vuole eseguire su IB, è possibile usare IPoIB sull'interfaccia IB. Usare i comandi seguenti (per RHEL/CentOS) per abilitare IP over InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull'installazione di varie [librerie MPI supportate](setup-mpi.md) e sulla relativa configurazione ottimale nelle macchine virtuali.
- Per informazioni sulla configurazione ottimale dei carichi di lavoro per prestazioni e scalabilità, vedere Panoramica della [serie HB](hb-series-overview.md) e [Panoramica sulle serie HC](hc-series-overview.md) .
- Per informazioni sugli annunci più recenti e su alcuni esempi e risultati HPC, vedere i [Blog della community tecnica di calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per una visualizzazione architettonica di livello superiore dell'esecuzione di carichi di lavoro HPC, vedere [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
