---
title: Prestazioni delle dimensioni della VM serie HC-macchine virtuali di Azure | Microsoft Docs
description: Informazioni sui risultati dei test delle prestazioni per le dimensioni delle macchine virtuali della serie HC in Azure.
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
ms.openlocfilehash: 6b8e66ad2c907815b8395ab47059db92e57ad4cf
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438058"
---
# <a name="hc-series-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali serie HC

Diversi test delle prestazioni sono stati eseguiti sulle dimensioni della serie HC. Di seguito sono riportati alcuni dei risultati di questo test delle prestazioni.

| Carico di lavoro                                        | HB                    |
|-------------------------------------------------|-----------------------|
| Triade di flusso                                    | ~ 190 GB/s (Intel MLC AVX-512)  |
| Linpack a prestazioni elevate (HPL)                  | ~ 3520 GigaFLOPS (Rpeak), ~ 2970 GigaFLOPS (Rmax) |
| Latenza RDMA & larghezza di banda                        | 1,80 microsecondi, 96,3 GB/s   |
| FIO sull'unità SSD NVMe locale                           | ~ 1,3 GB/s di letture, ~ 900 MB/s Scritture |  
| IOR su 4 SSD Premium di Azure (P30 Managed Disks, RAID0) * *  | ~ 780 MB/s di letture, ~ 780 MB/Scritture |

## <a name="infiniband-send-latency"></a>Latenza di invio InfiniBand

Perftest Mellanox.

```bash
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

|  #bytes         | #iterations     | t_min [microsecondi]     | t_max [microsecondi]     | t_typical [microsecondi] | t_avg [microsecondi]     | t_stdev [microsecondi]   |
|-----------------|-----------------|-----------------|-----------------|-----------------|-----------------|-----------------|
| 2               | 1000            | 1,80            | 7.50            | 1,85            | 1,86            | 0,20            |
| 4               | 1000            | 1,79            | 6,06            | 1,83            | 1,84            | 0,20            |
| 8               | 1000            | 1,78            | 5.26            | 1,83            | 1,84            | 0,19            |
| 16              | 1000            | 1,79            | 6,21            | 1,83            | 1,84            | 0,22            |
| 32              | 1000            | 1,80            | 6.82            | 1,84            | 1,85            | 0,24            |
| 64              | 1000            | 1,85            | 5,47            | 1,88            | 1,86            | 0.12            |
| 128             | 1000            | 1,88            | 5,61            | 1,93            | 1,89            | 0,25            |
| 256             | 1000            | 2.24            | 6,39            | 2.28            | 2,02            | 0.18            |
| 512             | 1000            | 2,32            | 5.42            | 2,36            | 2.30            | 0,17            |
| 1024            | 1000            | 2,43            | 6,22            | 2,48            | 2,38            | 0,21            |
| 2048            | 1000            | 2.68            | 6.14            | 2.75            | 2.52            | 0,20            |
| 4096            | 1000            | 3,17            | 7,02            | 3,26            | 2.81            | 0,24            |

## <a name="osu-mpi-latency-test"></a>Test della latenza MPI di OSU

Test della latenza MPI di OSU v 5.4.3.

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

| #bytes  | Latenza [microsecondi] (MPICH 3,3 + CH4) | Latenza [microsecondi] (OpenMPi 4.0.0) | Latenza [microsecondi] (MVAPICH2 2,3) |
|------|----------|----------|----------|
| 2    | 1,84     | 1,78     | 2.08     |
| 4    | 1,84     | 1,79     | 2.08     |
| 8    | 1,85     | 1,79     | 2.05     |
| 16   | 1,85     | 1,79     | 2.1      |
| 32   | 1,87     | 1,82     | 2.12     |
| 64   | 2        | 1,95     | 2,13     |
| 128  | 2.05     | 2        | 2.18     |
| 256  | 2,48     | 2.44     | 2.75     |
| 512  | 2.57     | 2.52     | 2.81     |
| 1024 | 2.76     | 2.71     | 2,97     |
| 2048 | 3,09     | 3.11     | 3,34     |
| 4096 | 3.72     | 3.91     | 4.44     |

## <a name="mpi-bandwidth"></a>Larghezza di banda MPI

Test della larghezza di banda MPI di OSU v 5.4.3.

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

| #Size   | Larghezza di banda (MB/s) | Larghezza di banda (GB/sec) |
|---------|------------------|------------------|
| 2       | 6,18             | 0,04944          |
| 4       | 13,27            | 0,10616          |
| 8       | 26,58            | 0,21264          |
| 16      | 53,51            | 0,42808          |
| 32      | 106,81           | 0,85448          |
| 64      | 211,24           | 1,68992          |
| 128     | 386,98           | 3,09584          |
| 256     | 756,32           | 6,05056          |
| 512     | 1434,2           | 11,4736          |
| 1024    | 2663,8           | 21,3104          |
| 2048    | 4396,99          | 35,17592         |
| 4096    | 6365,86          | 50,92688         |
| 8192    | 8137,9           | 65,1032          |
| 16384   | 9218,29          | 73,74632         |
| 32768   | 10564,61         | 84,51688         |
| 65536   | 11275,6          | 90,2048          |
| 131072  | 11633,7          | 93,0696          |
| 262144  | 11856,27         | 94,85016         |
| 524288  | 11962,69         | 95,70152         |
| 1048576 | 12025,43         | 96,20344         |
| 2097152 | 12038,7          | 96,3096          |
| 4194304 | 11290,92         | 90,32736         |
