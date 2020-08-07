---
title: Configurare l'interfaccia di passaggio dei messaggi per HPC-macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come configurare MPI per HPC in Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 210b2935cd2df81b0ff079c9a1c945fe770933f9
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926519"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Configurare l'interfaccia di passaggio dei messaggi per HPC

L' [interfaccia MPI (Message Passing Interface)](https://en.wikipedia.org/wiki/Message_Passing_Interface) è una libreria aperta ed è di norma per la parallelizzazione della memoria distribuita. Viene comunemente usato in molti carichi di lavoro HPC. I carichi di lavoro HPC sulle VM serie [H](../../sizes-hpc.md) e serie [N](../../sizes-gpu.md) [compatibili con RDMA](../../sizes-hpc.md#rdma-capable-instances) possono usare MPI per comunicare attraverso la rete InfiniBand a bassa latenza e larghezza di banda elevata.

Le dimensioni delle VM abilitate per SR-IOV in Azure (HBv2, HB, HC, NCv3, NDv2) consentono di usare praticamente qualsiasi versione di MPI con Mellanox OFED. Nelle VM abilitate per non SR-IOV, le implementazioni MPI supportate usano l'interfaccia di Microsoft Network Direct (ND) per la comunicazione tra le macchine virtuali. Di conseguenza, sono supportate solo le versioni Microsoft MPI (MS-MPI) 2012 R2 o versioni successive e Intel MPI 5. x. Le versioni successive (2017, 2018) della libreria di runtime Intel MPI possono o non essere compatibili con i driver RDMA di Azure.

Per le [macchine virtuali](../../sizes-hpc.md#rdma-capable-instances)abilitate per SR-IOV abilitate per RDMA, [CentOS-HPC versione 7,6 o una versione successiva](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) immagini di VM nel Marketplace sono ottimizzate e precaricate con i driver OFED per RDMA e le varie librerie MPI e i pacchetti di calcolo scientifici usati di frequente e rappresentano il modo più semplice per iniziare.

Sebbene gli esempi siano per RHEL/CentOS, ma i passaggi sono generali e possono essere usati per qualsiasi sistema operativo Linux compatibile, ad esempio Ubuntu (16,04, 18,04 19,04, 20,04) e SLES (12 SP4 e 15). Altri esempi per la configurazione di altre implementazioni MPI in altre distribuzioni sono disponibili nel [repository azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> L'esecuzione di processi MPI nelle VM abilitate per SR-IOV richiede la configurazione di chiavi di partizione (p-Keys) in un tenant per l'isolamento e la sicurezza. Per informazioni dettagliate su come determinare i valori della chiave p e impostarli correttamente per un processo MPI, attenersi alla procedura descritta nella sezione [Discover Partition Keys](#discover-partition-keys) .

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) è un Framework di API di comunicazione per HPC. È ottimizzato per la comunicazione MPI su InfiniBand e funziona con molte implementazioni MPI come OpenMPi e MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="hpc-x"></a>HPC-X

[HPC-X software Toolkit](https://www.mellanox.com/products/hpc-x-toolkit) contiene UCX e HCOLL.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Eseguire HPC-X

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

## <a name="openmpi"></a>OpenMPI

Installare UCX come descritto in precedenza. HCOLL fa parte del [Toolkit di software HPC-X](https://www.mellanox.com/products/hpc-x-toolkit) e non richiede un'installazione speciale.

Installare OpenMPi dai pacchetti disponibili nel repository.

```bash
sudo yum install –y openmpi
```

Compilazione OpenMPi.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Eseguire OpenMPi.

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Controllare la chiave di partizione come indicato in precedenza.

## <a name="intel-mpi"></a>Intel MPI

Scaricare la versione di [Intel MPI](https://software.intel.com/mpi-library/choose-download)scelta. Modificare la variabile di ambiente I_MPI_FABRICS a seconda della versione. Per Intel MPI 2018, usare `I_MPI_FABRICS=shm:ofa` e per 2019, usare `I_MPI_FABRICS=shm:ofi` .

### <a name="non-sr-iov-vms"></a>VM non SR-IOV
Per le macchine virtuali non SR-IOV, un esempio di download della versione di [valutazione gratuita](https://registrationcenter.intel.com/en/forms/?productid=1740) del runtime 5. x è il seguente:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Per la procedura di installazione, vedere [Intel MPI Library installation guide](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) (Guida all'installazione di Intel MPI Library).
Facoltativamente, potrebbe essere necessario abilitare ptrace per i processi non del debugger non radice (necessari per le versioni più recenti di Intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
Per SUSE Linux Enterprise Server le versioni di immagini di macchina virtuale: SLES 12 SP3 per HPC, SLES 12 SP3 per HPC (Premium), SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium), SLES 12 SP4 e SLES 15, i driver RDMA sono installati e i pacchetti Intel MPI vengono distribuiti nella macchina virtuale. Installare Intel MPI eseguendo il comando seguente:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mpich"></a>MPICH

Installare UCX come descritto in precedenza. Compila MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

Esecuzione di MPICH.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Controllare la chiave di partizione come indicato in precedenza.

## <a name="mvapich2"></a>MVAPICH2

Compila MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Esecuzione di MVAPICH2.

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platform MPI Community Edition

Installare i pacchetti necessari per la piattaforma MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Seguire il processo di installazione.

## <a name="osu-mpi-benchmarks"></a>Benchmark MPI OSU

Scaricare [OSU MPI benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) e untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Creare benchmark usando una particolare libreria MPI:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

I benchmark MPI si trovano nella `mpi/` cartella.


## <a name="discover-partition-keys"></a>Individua chiavi di partizione

Individuare le chiavi di partizione (p-Keys) per la comunicazione con altre macchine virtuali nello stesso tenant (set di disponibilità o set di scalabilità di macchine virtuali).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Il numero maggiore dei due è la chiave del tenant da utilizzare con MPI. Esempio: se le chiavi p sono le seguenti, 0x800b deve essere usato con MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Utilizzare la partizione diversa dalla chiave di partizione predefinita (0x7FFF). Per UCX è necessario cancellare il MSB della chiave p. Ad esempio, impostare UCX_IB_PKEY come 0x000B per 0x800b.

Si noti anche che, a condizione che il tenant (set di disponibilità o set di scalabilità di macchine virtuali) esista, il PKEYs rimane invariato. Questo vale anche quando i nodi vengono aggiunti o eliminati. I nuovi tenant ottengono PKEYs diversi.


## <a name="set-up-user-limits-for-mpi"></a>Configurare i limiti utente per MPI

Configurare i limiti utente per MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>Configurare le chiavi SSH per MPI

Configurare le chiavi SSH per i tipi MPI che lo richiedono.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

La sintassi precedente presuppone una home directory condivisa. in caso contrario, è necessario copiare la directory SSH in ogni nodo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle macchine virtuali serie [H](../../sizes-hpc.md) e serie [N](../../sizes-gpu.md) [abilitate per InfiniBand](../../sizes-hpc.md#rdma-capable-instances)
- Per informazioni sulla configurazione ottimale dei carichi di lavoro per prestazioni e scalabilità, vedere Panoramica della [serie HB](hb-series-overview.md) e [Panoramica sulle serie HC](hc-series-overview.md) .
- Per informazioni sugli annunci più recenti e su alcuni esempi e risultati HPC, vedere i [Blog della community tecnica di calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per una visualizzazione architettonica di livello superiore dell'esecuzione di carichi di lavoro HPC, vedere [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
