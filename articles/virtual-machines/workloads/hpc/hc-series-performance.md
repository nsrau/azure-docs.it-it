---
title: Prestazioni dimensioni macchina virtuale serie HC
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
ms.date: 09/10/2020
ms.author: amverma
ms.openlocfilehash: 0d63d9770dacf6a200e8b81e8d47d9f807a8a448
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603435"
---
# <a name="hc-series-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali serie HC

Diversi test delle prestazioni sono stati eseguiti sulle dimensioni della serie HC. Di seguito sono riportati alcuni dei risultati di questo test delle prestazioni.

| Carico di lavoro                                        | HB                    |
|-------------------------------------------------|-----------------------|
| Triade di flusso                                    | 190 GB/s (Intel MLC AVX-512)  |
| Linpack a prestazioni elevate (HPL)                  | 3520 GigaFLOPS (Rpeak), 2970 GigaFLOPS (Rmax) |
| Latenza RDMA & larghezza di banda                        | 1,05 microsecondi, 96,8 GB/s   |
| FIO sull'unità SSD NVMe locale                           | 1,3 GB/s letture, 900 MB/s Scritture |  
| IOR su 4 SSD Premium di Azure (P30 Managed Disks, RAID0) * *  | 780 MB/s letture, 780 MB/Scritture |

## <a name="mpi-latency"></a>Latenza MPI

Viene eseguito il test della latenza MPI dalla suite di microbenchmark OSU. Gli script di esempio sono disponibili in [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hc.png" alt-text="Latenza MPI in Azure HC.":::

## <a name="mpi-bandwidth"></a>Larghezza di banda MPI

Viene eseguito il test della larghezza di banda MPI da OSU microbenchmark suite. Gli script di esempio sono disponibili in [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hc.png" alt-text="Larghezza di banda MPI in Azure HC.":::


## <a name="mellanox-perftest"></a>Perftest Mellanox

Il [pacchetto perftest di Mellanox](https://community.mellanox.com/s/article/perftest-package) include molti test InfiniBand, ad esempio latenza (ib_send_lat) e larghezza di banda (ib_send_bw). Di seguito è riportato un comando di esempio.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sugli annunci più recenti e su alcuni esempi e risultati di High Performance Computing (HPC), vedere i [Blog della community tecnica di calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per una visualizzazione architettonica di livello superiore dell'esecuzione di carichi di lavoro HPC, vedere [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
