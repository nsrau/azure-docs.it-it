---
title: Prestazioni dimensioni macchina virtuale serie HB
description: Informazioni sui risultati dei test delle prestazioni per le dimensioni delle VM serie HB in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 09/09/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 10d1bfea4527eb7ab9a341de8f12a32cb59597eb
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966973"
---
# <a name="hb-series-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali serie HB

Diversi test delle prestazioni sono stati eseguiti sulle dimensioni della serie HB. Di seguito sono riportati alcuni dei risultati di questo test delle prestazioni.

| Carico di lavoro                                        | HB                    |
|-------------------------------------------------|-----------------------|
| Triade di flusso                                    | 260 GB/s (32-33 GB/s per CCX)  |
| High-Performance Linpack (HPL)                  | 1.000 GigaFLOPS (Rpeak), 860 GigaFLOPS (Rmax) |
| Latenza RDMA & larghezza di banda                        | 1,27 microsecondi, 99,1 GB/s   |
| FIO sull'unità SSD NVMe locale                           | 1,7 GB/s letture, 1,0 GB/s Scritture      |  
| IOR su 4 * Azure SSD Premium (P30 Managed Disks, RAID0) * *  | 725 MB/s letture, 780 MB/Scritture   |


## <a name="mpi-latency"></a>Latenza MPI

Viene eseguito il test della latenza MPI dalla suite di microbenchmark OSU. Gli script di esempio sono disponibili in [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hb.png" alt-text="Latenza MPI in HB di Azure.":::

## <a name="mpi-bandwidth"></a>Larghezza di banda MPI

Viene eseguito il test della larghezza di banda MPI da OSU microbenchmark suite. Gli script di esempio sono disponibili in [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hb.png" alt-text="Larghezza di banda MPI in Azure HB.":::


## <a name="mellanox-perftest"></a>Perftest Mellanox

Il [pacchetto perftest di Mellanox](https://community.mellanox.com/s/article/perftest-package) include molti test InfiniBand, ad esempio latenza (ib_send_lat) e larghezza di banda (ib_send_bw). Di seguito è riportato un comando di esempio.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sugli annunci più recenti e su alcuni esempi e risultati di High Performance Computing (HPC), vedere i [Blog della community tecnica di calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per una visualizzazione architettonica di livello superiore dell'esecuzione di carichi di lavoro HPC, vedere [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
