---
title: Configurare l'interfaccia di passaggio dei messaggi per HPC - Macchine virtuali di Azure Documenti Microsoft
description: Informazioni su come configurare MPI per HPC in Azure.Learn how to set up MPI for HPC on Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023991"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Configurare l'interfaccia di passaggio dei messaggi per HPC

I carichi di lavoro MPI (Message Passing Interface) sono una parte significativa dei carichi di lavoro HPC tradizionali. Le dimensioni delle macchine virtuali abilitate per SR-IOV in Azure consentono l'utilizzo di quasi tutte le dimensioni di MPI. 

L'esecuzione di processi MPI nelle macchine virtuali richiede la configurazione delle chiavi di partizione (chiavi p) in un tenant. Seguire i passaggi nella sezione Individuare le chiavi di [partizione](#discover-partition-keys) per informazioni dettagliate sulla determinazione dei valori p-key.

## <a name="ucx"></a>Ucx

[UCX](https://github.com/openucx/ucx) offre le migliori prestazioni su IB e funziona con MPICH e OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>File MpI

Installare UCX come descritto in precedenza.

```bash
sudo yum install –y openmpi
```

Costruire OpenMPI.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Eseguire OpenMPI.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Controllare la chiave di partizione come indicato in precedenza.

## <a name="mpich"></a>MPICH

Installare UCX come descritto in precedenza.

Costruire MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

In esecuzione MPICH.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Controllare la chiave di partizione come indicato in precedenza.

## <a name="mvapich2"></a>MVAPICH2

Costruisci MVAPICH2.

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

## <a name="platform-mpi-community-edition"></a>Piattaforma MPI Community Edition

Installare i pacchetti necessari per la piattaforma MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Seguire il processo di installazione.

## <a name="intel-mpi"></a>Intel MPI

[Scarica Intel MPI](https://software.intel.com/mpi-library/choose-download).

Modificare la variabile di ambiente I_MPI_FABRICS a seconda della versione. Per Intel MPI 2018, utilizzare `I_MPI_FABRICS=shm:ofa` e per `I_MPI_FABRICS=shm:ofi`2019, utilizzare .

Il blocco del processo funziona correttamente per 15, 30 e 60 PPN per impostazione predefinita.

## <a name="osu-mpi-benchmarks"></a>OSU MPI Benchmark

[Scarica OSU MPI Benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) e untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Costruisci Benchmark utilizzando una particolare libreria MPI:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

I benchmark MPI `mpi/` sono presenti nella cartella.


## <a name="discover-partition-keys"></a>Individuare le chiavi di partizioneDiscover partition keys

Individuare le chiavi di partizione (chiavi p) per comunicare con altre macchine virtuali all'interno dello stesso tenant (set di disponibilità o set di scalabilità della macchina virtuale).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Il più grande dei due è la chiave tenant che deve essere utilizzata con MPI. Esempio: se i seguenti sono i tasti p, 0x800b deve essere utilizzato con MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Utilizzare la partizione diversa da quella predefinita (0x7fff). UCX richiede la cancellazione dell'MSB di p-key. Ad esempio, impostare UCX_IB_PKEY su 0x000b per 0x800b.

Si noti inoltre che finché il tenant (AVSet o VMSS) esiste, le PKEY rimangono invariate. Questo vale anche quando i nodi vengono aggiunti/eliminati. I nuovi tenant ottengono PKEY diversi.


## <a name="set-up-user-limits-for-mpi"></a>Impostare i limiti utente per MPI

Impostare i limiti utente per MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>Configurare le chiavi SSH per MPI

Impostare le chiavi SSH per i tipi MPI che lo richiedono.

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

La sintassi precedente presuppone una home directory condivisa, altrimenti la directory .ssh deve essere copiata in ogni nodo.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su HPC in Azure.Learn more about [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) on Azure.
