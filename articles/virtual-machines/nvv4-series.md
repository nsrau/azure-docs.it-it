---
title: Serie NVv4-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie NVv4.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 0295ed7d44d64fcc1aeb68e1beaa37987b177edb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273857"
---
# <a name="nvv4-series"></a>Serie NVv4 

Le macchine virtuali della serie NVv4 sono basate su GPU [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) e sulle CPU AMD EPYC 7V12 (Roma). Con la serie NVv4, Azure introduce macchine virtuali con GPU parziali. Scegli la macchina virtuale di dimensioni appropriate per le applicazioni grafiche con accelerazione GPU e i desktop virtuali a partire dal 1/8 di una GPU con 2 GiB frame buffer in una GPU completa con 16 GiB frame buffer. Le macchine virtuali NVv4 attualmente supportano solo il sistema operativo guest Windows.

<br>

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

Live Migration: non supportato

Aggiornamenti con mantenimento della memoria: non supportato

| Dimensioni | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> macchine virtuali della serie NVV4 funzionalità AMD simultanea multithreading

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

Per sfruttare i vantaggi delle funzionalità GPU delle macchine virtuali della serie NVv4 di Azure che eseguono Windows, è necessario installare i driver GPU AMD.

Per installare manualmente i driver GPU AMD, vedere la [serie N configurazione driver GPU AMD per Windows](./windows/n-series-amd-driver-setup.md) per i sistemi operativi supportati, i driver, l'installazione e i passaggi di verifica.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
