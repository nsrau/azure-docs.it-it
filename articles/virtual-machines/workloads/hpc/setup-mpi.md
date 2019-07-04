---
title: Configurare Message Passing Interface per HPC - macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come configurare MPI per HPC in Azure.
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
ms.openlocfilehash: 5356a033dbc3d989dd27019f03b1fe36035ff9a4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441642"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Configurare Message Passing Interface per HPC

I carichi di lavoro di messaggio Passing Interface (MPI) sono una parte significativa dei carichi di lavoro HPC tradizionali. SR-IOV abilitate dimensioni delle macchine Virtuali in Azure consentono di quasi qualsiasi versione di MPI da usare. 

Eseguire processi MPI in macchine virtuali richiede l'impostazione di chiavi di partizione (p-chiavi) in un tenant. Seguire i passaggi nel [individua le chiavi di partizione](#discover-partition-keys) sezione per informazioni dettagliate su come determinare i valori della chiave p.

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) offre prestazioni ottimali su IB ed è compatibile con MPICH e OpenMPI.

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

Compilazione OpenMPI.

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

Compilare MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

MPICH è in esecuzione.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Controllare la chiave di partizione come indicato in precedenza.

## <a name="mvapich2"></a>MVAPICH2

Compilazione MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

MVAPICH2 è in esecuzione.

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

[Scaricare Intel MPI](https://software.intel.com/mpi-library/choose-download).

Modificare la variabile di ambiente I_MPI_FABRICS a seconda della versione. Per Intel MPI 2018, usare `I_MPI_FABRICS=shm:ofa` allo stesso modo per 2019, `I_MPI_FABRICS=shm:ofi`.

L'aggiunta di processo funziona correttamente per PPN 15, 30 e 60 per impostazione predefinita.

## <a name="osu-mpi-benchmarks"></a>Benchmark MPI OSU

[Scaricare benchmark MPI OSU] [ http://mvapich.cse.ohio-state.edu/benchmarks/ ](http://mvapich.cse.ohio-state.edu/benchmarks/) e decomprimere.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Creare i benchmark con una determinata libreria MPI:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

I benchmark MPI sono sotto `mpi/` cartella.


## <a name="discover-partition-keys"></a>Individuare le chiavi di partizione

Individuare le chiavi di partizione (p-chiavi) per la comunicazione con altre macchine virtuali.

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Il più elevato tra i due è la chiave del tenant che deve essere usata con MPI. Esempio: Se i seguenti sono le chiavi p, 0x800b deve essere utilizzato con MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Utilizzare la partizione diversa dalla chiave di partizione predefinito (0x7fff). UCX richiede il byte più significativo della chiave p da cancellare. Ad esempio, impostare UCX_IB_PKEY come 0x000b per 0x800b.


## <a name="set-up-user-limits-for-mpi"></a>Impostare i limiti di utenti per MPI

Configurare i limiti utente per MPI.

```bash
cat << EOF >> /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>Configurare le chiavi SSH per MPI

Consente di impostare le chiavi SSH per i tipi MPI che lo richiedono.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

La sintassi riportata sopra presuppone una home directory condivisa, altra directory. SSH devono essere copiati in ogni nodo.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
