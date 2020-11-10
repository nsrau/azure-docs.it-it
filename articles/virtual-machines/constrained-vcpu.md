---
title: Dimensioni con vCPU vincolate
description: Elenca le dimensioni delle macchine virtuali che supportano un numero di vCPU vincolato.
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/09/2018
ms.author: mimckitt
ms.openlocfilehash: 530ae08dabe54ed279a4055e52109aad0ef4f842
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426154"
---
# <a name="constrained-vcpu-capable-vm-sizes"></a>Dimensioni delle macchine virtuali con supporto vCPU vincolate

Alcuni carichi di lavoro di database, ad esempio SQL Server o Oracle, richiedono un utilizzo elevato di memoria, spazio di archiviazione e larghezza di banda di I/O, ma non un numero elevato di core. Molti carichi di lavoro di database non sono a elevato utilizzo di CPU. Azure offre alcune dimensioni di macchina virtuale in cui è possibile limitare il numero di vCPU per ridurre i costi relativi alle licenze software, mantenendo la stessa memoria, archiviazione e larghezza di banda di I/O.

Il numero di vCPU virtuali può essere vincolato a metà o un quarto delle dimensioni originali della macchina virtuale. Queste nuove dimensioni di macchina virtuale hanno un suffisso che specifica il numero di vCPU attive, semplificandone l'identificazione.

Ad esempio, le attuali dimensioni di VM Standard_GS5 prevedono 32 vCPU, 448 GB di RAM, 64 dischi (fino a 256 TB) e 80.000 operazioni di I/O al secondo o 2 GB/s di larghezza di banda di I/O. La nuove dimensioni di VM Standard_GS5-16 e Standard_GS5-8 prevedono rispettivamente 8 e 16 vCPU attive, con le stesse specifiche delle dimensioni Standard_GS5 in termini di memoria, archiviazione e larghezza di banda di I/O.

I costi di licenza addebitati per SQL Server o Oracle sono vincolati al nuovo numero di vCPU e gli addebiti di altri prodotti dovrebbero essere effettuati in base al nuovo numero di vCPU. Questo si traduce in un aumento del 50-75% nel rapporto tra specifiche della macchina virtuale e vCPU attive (fatturabili). Queste nuove dimensioni consentono ai carichi di lavoro dei clienti di usare la stessa memoria, l'archiviazione e la larghezza di banda di I/O, ottimizzando al tempo stesso i costi di gestione delle licenze software. Attualmente il costo di calcolo, che include il costo di licenza per il sistema operativo, è lo stesso delle dimensioni originali. Per altre informazioni, vedere [Azure VM sizes for more cost-effective database workloads](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/) (Nuove dimensioni per le macchine virtuali di Azure per carichi di lavoro di database più convenienti).


| Nome                | vCPU | Specifiche           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | Come M8ms    |
| Standard_M8-4ms     | 4    | Come M8ms    |
| Standard_M16-4ms    | 4    | Come M16ms   |
| Standard_M16-8ms    | 8    | Come M16ms   |
| Standard_M32-8ms    | 8    | Come M32ms   |
| Standard_M32-16ms   | 16   | Come M32ms   |
| Standard_M64-32ms   | 32   | Come M64ms   |
| Standard_M64-16ms   | 16   | Come M64ms   |
| Standard_M128-64ms  | 64   | Come M128ms  |
| Standard_M128-32ms  | 32   | Come M128ms  |
| Standard_E4-2s_v3   | 2    | Come E4s_v3  |
| Standard_E8-4s_v3   | 4    | Come E8s_v3  |
| Standard_E8-2s_v3   | 2    | Come E8s_v3  |
| Standard_E16-8s_v3  | 8    | Come E16s_v3 |
| Standard_E16-4s_v3  | 4    | Come E16s_v3 |
| Standard_E32-16s_v3 | 16   | Come E32s_v3 |
| Standard_E32-8s_v3  | 8    | Come E32s_v3 |
| Standard_E64-32s_v3 | 32   | Come E64s_v3 |
| Standard_E64-16s_v3 | 16   | Come E64s_v3 |
| Standard_E4 2s_v4   | 2    | Uguale a E4s_v4  |
| Standard_E8 4s_v4   | 4    | Uguale a E8s_v4  |
| Standard_E8 2s_v4   | 2    | Uguale a E8s_v4  |
| Standard_E16 8s_v4  | 8    | Uguale a E16s_v4 |
| Standard_E16 4s_v4  | 4    | Uguale a E16s_v4 |
| Standard_E32 16s_v4 | 16   | Uguale a E32s_v4 |
| Standard_E32 8s_v4  | 8    | Uguale a E32s_v4 |
| Standard_E64 32s_v4 | 32   | Uguale a E64s_v4 |
| Standard_E64 16s_v4 | 16   | Uguale a E64s_v4 |
| Standard_E4 2ds_v4  | 2    | Uguale a E4ds_v4 |
| Standard_E8 4ds_v4  | 4    | Uguale a E8ds_v4 |
| Standard_E8 2ds_v4  | 2    | Uguale a E8ds_v4 |
| Standard_E16 8ds_v4 | 8    | Uguale a E16ds_v4|
| Standard_E16 4ds_v4 | 4    | Uguale a E16ds_v4|
| Standard_E32 16ds_v4| 16   | Uguale a E32ds_v4|
| Standard_E32 8ds_v4 | 8    | Uguale a E32ds_v4|
| Standard_E64 32ds_v4| 32   | Uguale a E64ds_v4|
| Standard_E64 16ds_v4| 16   | Uguale a E64ds_v4|
| Standard_E4 2as_v4  | 2    | Uguale a E4as_v4 |
| Standard_E8 4as_v4  | 4    | Uguale a E8as_v4 |
| Standard_E8 2as_v4  | 2    | Uguale a E8as_v4 |
| Standard_E16 8as_v4 | 8    | Uguale a E16as_v4|
| Standard_E16 4as_v4 | 4    | Uguale a E16as_v4|
| Standard_E32 16as_v4| 16   | Uguale a E32as_v4|
| Standard_E32 8as_v4 | 8    | Uguale a E32as_v4|
| Standard_E64 32as_v4| 32   | Uguale a E64as_v4|
| Standard_E64 16as_v4| 16   | Uguale a E64as_v4|
| Standard_E96 48as_v4| 48   | Uguale a E96as_v4|
| Standard_E96 24as_v4| 24   | Uguale a E96as_v4|
| Standard_GS4-8      | 8    | Come GS4     |
| Standard_GS4-4      | 4    | Come GS4     |
| Standard_GS5-16     | 16   | Come GS5     |
| Standard_GS5-8      | 8    | Come GS5     |
| Standard_DS11-1_v2  | 1    | Come DS11_v2 |
| Standard_DS12-2_v2  | 2    | Come DS12_v2 |
| Standard_DS12-1_v2  | 1    | Come DS12_v2 |
| Standard_DS13-4_v2  | 4    | Come DS13_v2 |
| Standard_DS13-2_v2  | 2    | Come DS13_v2 |
| Standard_DS14-8_v2  | 8    | Come DS14_v2 |
| Standard_DS14-4_v2  | 4    | Come DS14_v2 |
| Standard_M416 208s_v2 | 208    | Uguale a M416s_v2|
| Standard_M416 208ms_v2 | 208    | Uguale a M416ms_v2 |

## <a name="other-sizes"></a>Altre dimensioni
- [Ottimizzate per il calcolo](./sizes-compute.md)
- [Ottimizzate per la memoria](./sizes-memory.md)
- [Ottimizzate per l'archiviazione](./sizes-storage.md)
- [GPU](./sizes-gpu.md)
- [High Performance Computing (HPC)](./sizes-hpc.md)

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su come le [unità di calcolo di Azure](./acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
