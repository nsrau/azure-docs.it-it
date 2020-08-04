---
title: Configurazione e ottimizzazione delle macchine virtuali di Azure di serie H e serie N abilitate per InfiniBand
description: Informazioni sulla configurazione e l'ottimizzazione delle macchine virtuali serie H e serie N abilitate per InfiniBand per HPC.
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
ms.openlocfilehash: c49286f370691c39c3d14d589f2657d6e0bb3c04
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542332"
---
# <a name="configure-and-optimize-vms"></a>Configurare e ottimizzare le macchine virtuali

Questo articolo condivide le tecniche note per configurare e ottimizzare le macchine virtuali serie [H](../../sizes-hpc.md) abilitate per InfiniBand e [serie N](../../sizes-gpu.md) per HPC.

## <a name="vm-images"></a>Immagini di macchine virtuali
Nelle VM con InfiniBand abilitata, i driver appropriati sono necessari per abilitare RDMA. In Linux, le immagini di VM CentOS-HPC nel Marketplace sono preconfigurate con i driver appropriati. Le immagini di macchine virtuali Ubuntu possono essere configurate con i driver appropriati seguendo le istruzioni riportate [qui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351). È inoltre consigliabile creare immagini di [VM personalizzate](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images) con i driver e la configurazione appropriati e riutilizzarle in modo ricorrente.

### <a name="centos-hpc-vm-images"></a>Immagini di VM CentOS-HPC
Per le VM con supporto di [RDMA](../../sizes-hpc.md#rdma-capable-instances)non SR-IOV abilitate, CentOS-HPC versione 6,5 o successiva, fino a 7,5 nel Marketplace sono adatte. Ad esempio, per le [macchine virtuali della serie H16](../../h-series.md), sono consigliate le versioni 7,1 e 7,5. Queste immagini di macchina virtuale vengono precaricate con i driver diretti di rete per RDMA e Intel MPI versione 5,1.

  Per le [macchine virtuali](../../sizes-hpc.md#rdma-capable-instances)abilitate per SR-IOV abilitate per RDMA, [CentOS-HPC versione 7,6 o una versione successiva](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) le immagini di VM nel Marketplace sono adatte. Queste immagini di VM vengono ottimizzate e precaricate con i driver OFED per RDMA e le librerie MPI e i pacchetti di calcolo scientifici usati comunemente e rappresentano il modo più semplice per iniziare.

  Esempio di script usati per la creazione di immagini di VM CentOS-HPC versione 7,6 e successive da un'immagine di base di CentOS Marketplace si trovano nel [repository azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).

### <a name="rhelcentos-vm-images"></a>Immagini di VM RHEL/CentOS
Le immagini di VM non HPC basate su RHEL o CentOS sul Marketplace possono essere configurate per l'uso nelle [macchine virtuali](../../sizes-hpc.md#rdma-capable-instances)abilitate per RDMA SR-IOV abilitate. Altre informazioni sull' [Abilitazione di InfiniBand](enable-infiniband.md) e sulla [configurazione di MPI](setup-mpi.md) nelle VM.

  Esempio di script usati per la creazione di immagini di VM CentOS-HPC versione 7,6 e successive da un'immagine di base di CentOS Marketplace si trovano nel [repository azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).

### <a name="ubuntu-vm-images"></a>Immagini di VM Ubuntu
Le immagini di VM Ubuntu Server 16,04 LTS, 18,04 LTS e 20,04 LTS nel Marketplace sono supportate sia per le VM SR-IOV che per quelle non SR-IOV [in grado](../../sizes-hpc.md#rdma-capable-instances)di supportare RDMA. Altre informazioni sull' [Abilitazione di InfiniBand](enable-infiniband.md) e sulla [configurazione di MPI](setup-mpi.md) nelle VM.

  Esempio di script che è possibile usare nella creazione di immagini di VM HPC basate su Ubuntu 18,04 LTS si trovano nel [repository azhpc-images](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc).

### <a name="suse-linux-enterprise-server-vm-images"></a>Immagini di macchine virtuali SUSE Linux Enterprise Server
Sono supportate le immagini SLES 12 SP3 per HPC, SLES 12 SP3 per HPC (Premium), SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium), SLES 12 SP4 e SLES 15 VM nel Marketplace. Queste immagini di macchina virtuale vengono precaricate con i driver diretti di rete per RDMA e Intel MPI versione 5,1. Altre informazioni sulla [configurazione di MPI](setup-mpi.md) nelle VM.

## <a name="optimize-vms"></a>Ottimizzare le macchine virtuali

Di seguito sono riportate alcune impostazioni di ottimizzazione facoltative per migliorare le prestazioni della macchina virtuale.

### <a name="update-lis"></a>Aggiorna LIS

Se necessario per le funzionalità o le prestazioni, [i driver di Linux Integration Services (LIS)](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) possono essere installati o aggiornati nelle distribuzioni del sistema operativo supportate, in particolare per la distribuzione usando un'immagine personalizzata o una versione precedente del sistema operativo, ad esempio CentOS/RHEL 6. x o versione precedente 7. x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Recupera memoria

Migliorare le prestazioni recuperando automaticamente la memoria per evitare l'accesso remoto alla memoria.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Per renderlo permanente dopo il riavvio della macchina virtuale:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Disabilitare il firewall e SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Disabilitare cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>Configurare WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Facoltativamente, il WALinuxAgent può essere disabilitato come passaggio di pre-processo e abilitato di nuovo dopo il processo per la massima disponibilità delle risorse delle macchine virtuali per il carico di lavoro HPC.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull' [Abilitazione di InfiniBand](enable-infiniband.md) sulle VM serie [H](../../sizes-hpc.md) e serie [N](../../sizes-gpu.md) abilitate per InfiniBand.
- Altre informazioni sull'installazione di varie [librerie MPI supportate](setup-mpi.md) e sulla relativa configurazione ottimale nelle macchine virtuali.
- Per informazioni sulla configurazione ottimale dei carichi di lavoro per prestazioni e scalabilità, vedere Panoramica della [serie HB](hb-series-overview.md) e [Panoramica sulle serie HC](hc-series-overview.md) .
- Per informazioni sugli annunci più recenti e su alcuni esempi e risultati HPC, vedere i [Blog della community tecnica di calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per una visualizzazione architettonica di livello superiore dell'esecuzione di carichi di lavoro HPC, vedere [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
