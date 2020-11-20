---
title: Prestazioni dimensioni macchina virtuale serie HBv2
description: Informazioni sui risultati dei test delle prestazioni per le dimensioni delle macchine virtuali della serie HBv2 in Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b782f3d435b7b3737de09b7a12d0fb17aa5f1edc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963318"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali serie HBv2

Sono stati eseguiti diversi test delle prestazioni sulle macchine virtuali di dimensioni della [serie HBv2](../../hbv2-series.md) . Di seguito sono riportati alcuni dei risultati di questo test delle prestazioni.


| Carico di lavoro                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| Triade di flusso                                    | 350 GB/s (21-23 GB/s per CCX)                                     |
| High-Performance Linpack (HPL)                  | 4 teraflop (Rpeak, FP64), 8 teraflop (Rmax, FP32)               |
| Latenza RDMA & larghezza di banda                        | 1,2 microsecondi, 190 GB/s                                        |
| FIO sull'unità SSD NVMe locale                           | 2,7 GB/s letture, 1,1 GB/s Scritture; letture IOPS 102.000, scritture IOPS 115 |
| IOR su 8 * Azure SSD Premium (P40 Managed Disks, RAID0) * *  | 1,3 GB/s letture, 2,5 GB/Scritture; letture IOPS 101K, scritture IOPS 105K |


## <a name="mpi-latency"></a>Latenza MPI

Viene eseguito il test della latenza MPI dalla suite di microbenchmark OSU. Gli script di esempio sono disponibili su [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
 
:::image type="content" source="./media/latency-hbv2.png" alt-text="Latenza MPI in HB di Azure.":::


## <a name="mpi-bandwidth"></a>Larghezza di banda MPI

Viene eseguito il test della larghezza di banda MPI da OSU microbenchmark suite. Gli script di esempio sono disponibili su [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
``` 

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="Larghezza di banda MPI in Azure HB.":::


## <a name="mellanox-perftest"></a>Perftest Mellanox

Il [pacchetto perftest di Mellanox](https://community.mellanox.com/s/article/perftest-package) include molti test InfiniBand, ad esempio latenza (ib_send_lat) e larghezza di banda (ib_send_bw). Di seguito è riportato un comando di esempio. 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sugli annunci più recenti e su alcuni esempi e risultati di High Performance Computing (HPC), vedere i [Blog della community tecnica di calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per una visualizzazione architettonica di livello superiore dell'esecuzione di carichi di lavoro HPC, vedere [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).