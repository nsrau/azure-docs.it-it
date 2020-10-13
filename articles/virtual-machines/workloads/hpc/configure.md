---
title: Configurazione e ottimizzazione delle macchine virtuali di Azure di serie H e serie N abilitate per InfiniBand
description: Informazioni sulla configurazione e l'ottimizzazione delle macchine virtuali serie H e serie N abilitate per InfiniBand per HPC.
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 08/07/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 9ecfe1df273834ae38bd6bb94980444f5e34f786
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994811"
---
# <a name="configure-and-optimize-vms"></a>Configurare e ottimizzare le macchine virtuali

Questo articolo condivide le tecniche note per configurare e ottimizzare le macchine virtuali serie [H](../../sizes-hpc.md) abilitate per InfiniBand e [serie N](../../sizes-gpu.md) per HPC.

## <a name="vm-images"></a>Immagini di macchine virtuali
Nelle VM con InfiniBand abilitata, i driver appropriati sono necessari per abilitare RDMA. In Linux, le immagini di VM CentOS-HPC nel Marketplace sono preconfigurate con i driver appropriati. Le immagini di macchine virtuali Ubuntu possono essere configurate con i driver appropriati seguendo le istruzioni riportate [qui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351). È inoltre consigliabile creare immagini di [VM personalizzate](../../linux/tutorial-custom-images.md) con i driver e la configurazione appropriati e riutilizzarle in modo ricorrente.

> [!NOTE]
> Nelle macchine virtuali [serie N](../../sizes-gpu.md) abilitate per la GPU sono necessari anche i driver GPU appropriati che possono essere aggiunti tramite le [estensioni della macchina virtuale](../../extensions/hpccompute-gpu-linux.md) o [manualmente](../../linux/n-series-driver-setup.md). Alcune immagini di VM nel Marketplace sono preinstallate anche con i driver GPU NVIDIA.

### <a name="centos-hpc-vm-images"></a>Immagini di VM CentOS-HPC

#### <a name="non-sr-iov-enabled-vms"></a>VM non abilitate per SR-IOV
Per le VM con supporto di [RDMA](../../sizes-hpc.md#rdma-capable-instances)non SR-IOV abilitate, CentOS-HPC versione 6,5 o successiva, fino a 7,5 nel Marketplace sono adatte. Ad esempio, per le [macchine virtuali della serie H16](../../h-series.md), sono consigliate le versioni 7,1 e 7,5. Queste immagini di macchina virtuale vengono precaricate con i driver diretti di rete per RDMA e Intel MPI versione 5,1.

> [!NOTE]
> In queste immagini HPC basate su CentOS per le macchine virtuali abilitate per non SR-IOV, gli aggiornamenti del kernel sono disabilitati nel file di configurazione **yum** . Ciò è dovuto al fatto che i driver RDMA di NetworkDirect Linux sono distribuiti come un pacchetto RPM e gli aggiornamenti dei driver potrebbero non funzionare se il kernel viene aggiornato.

#### <a name="sr-iov-enabled-vms"></a>VM abilitate per SR-IOV
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

Se necessario per le funzionalità o le prestazioni, [i driver di Linux Integration Services (LIS)](../../linux/endorsed-distros.md) possono essere installati o aggiornati nelle distribuzioni del sistema operativo supportate, in particolare per la distribuzione usando un'immagine personalizzata o una versione precedente del sistema operativo, ad esempio CentOS/RHEL 6. x o versione precedente 7. x.

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
- Esaminare la [panoramica della serie HB](hb-series-overview.md) e la [panoramica della serie HC](hc-series-overview.md) per informazioni su come configurare in modo ottimale i carichi di lavoro ai fini delle prestazioni e della scalabilità.
- Leggere gli ultimi annunci e alcuni esempi HPC e risultati nei [blog della community tecnica di Calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
