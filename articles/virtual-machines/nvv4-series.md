---
title: Serie NVv4
description: Specifiche per le macchine virtuali della serie NVv4.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 9b841b6422a4314b43a594cb0b22040f884228eb
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543895"
---
# <a name="nvv4-series"></a>Serie NVv4 

Le macchine virtuali della serie NVv4 sono basate su GPU [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) e sulle CPU AMD EPYC 7V12 (Roma). Con la serie NVv4, Azure introduce macchine virtuali con GPU parziali. Scegli la macchina virtuale di dimensioni appropriate per le applicazioni grafiche con accelerazione GPU e i desktop virtuali a partire dal 1/8 di una GPU con 2 GiB frame buffer in una GPU completa con 16 GiB frame buffer. Le macchine virtuali NVv4 attualmente supportano solo il sistema operativo guest Windows.

<br>

ACU: 230-260

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

Live Migration: Non supportato

Manutenzione con mantenimento della memoria: Non supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max/Larghezza di banda di rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 / 1000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 / 2000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 / 4000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 / 8000 |

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
