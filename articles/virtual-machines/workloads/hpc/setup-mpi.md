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
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77023991"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Configurare l'interfaccia di passaggio dei messaggi per HPC

I carichi di lavoro MPI (Message Passing Interface) rappresentano una parte significativa dei carichi di lavoro HPC tradizionali. Le dimensioni delle VM abilitate per SR-IOV in Azure consentono di usare praticamente qualsiasi versione di MPI. 

L'esecuzione di processi MPI nelle VM richiede la configurazione di chiavi di partizione (p-Keys) in un tenant. Per informazioni dettagliate su come determinare i valori della chiave p, seguire i passaggi della sezione [Discover Partition Keys](#discover-partition-keys) .

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) offre le migliori prestazioni in IB e funziona con MPICH e openmpi.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Installare UCX come descritto in precedenza.

```bash
sudo yum install –y openmpi
```

Compilazione OpenMPi.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Eseguire OpenMPi.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Controllare la chiave di partizione come indicato in precedenza.

## <a name="mpich"></a>MPICH

Installare UCX come descritto in precedenza.

Compila MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

Esecuzione di MPICH.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Controllare la chiave di partizione come indicato in precedenza.

## <a name="mvapich2"></a>MVAPICH2

Compila MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Esecuzione di MVAPICH2.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
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

## <a name="intel-mpi"></a>Intel MPI

[Scaricare Intel MPI](https://software.intel.com/mpi-library/choose-download).

Modificare la variabile di ambiente I_MPI_FABRICS a seconda della versione. Per Intel MPI 2018, usare `I_MPI_FABRICS=shm:ofa` e per 2019, usare `I_MPI_FABRICS=shm:ofi`.

Il blocco del processo funziona correttamente per le PPN 15, 30 e 60 per impostazione predefinita.

## <a name="osu-mpi-benchmarks"></a>Benchmark MPI OSU

[Scaricare OSU MPI benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) e untar.

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

I benchmark MPI si trovano `mpi/` nella cartella.


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

Si noti anche che, a condizione che il tenant (AVSet o VMSS) esista, il PKEYs rimane invariato. Questo vale anche quando i nodi vengono aggiunti o eliminati. I nuovi tenant ottengono PKEYs diversi.


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

Scopri di più su [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
