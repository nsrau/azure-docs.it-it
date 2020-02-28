---
title: Serie NVv4-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie NVv4.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 2afec4dc89c8d8892356888ec3404d36ba929d23
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669642"
---
# <a name="nvv4-series-preview"></a>Serie NVv4 (anteprima)

Le macchine virtuali della serie NVv4 sono basate su GPU [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) e sulle CPU AMD EPYC 7V12 (Roma). Con la serie NVv4, Azure introduce macchine virtuali con GPU parziali. Scegli la macchina virtuale di dimensioni appropriate per le applicazioni grafiche con accelerazione GPU e i desktop virtuali a partire dal 1/8 di una GPU con 2 GiB frame buffer in una GPU completa con 16 GiB frame buffer. Le macchine virtuali NVv4 attualmente supportano solo il sistema operativo guest Windows.

[Iscriviti e ottieni l'accesso a queste macchine virtuali durante l'anteprima](https://aka.ms/nvv4signup).
<br>

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> macchine virtuali della serie NVV4 funzionalità AMD simultanea multithreading

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

Per sfruttare i vantaggi delle funzionalità GPU delle VM serie N di Azure che eseguono Windows, è necessario installare i driver GPU NVIDIA o AMD.

L' [estensione driver GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) installa driver NVIDIA CUDA o Grid appropriati in una macchina virtuale Windows serie N. Installare o gestire l'estensione usando il portale di Azure o strumenti come i modelli di Azure PowerShell Azure o Azure Resource Manager. Vedere la [documentazione dell'estensione dei driver GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) per informazioni sui sistemi operativi supportati e sui passaggi di distribuzione. Per altre informazioni sulle estensioni macchina virtuale, vedere [Azure virtual machine extensions and features](/.extensions/overview.md) (Funzionalità ed estensioni macchina virtuale di Azure).

Se si sceglie di installare manualmente i driver GPU NVIDIA, vedere la pagina relativa alla [configurazione di driver GPU serie N per Windows](./windows/n-series-driver-setup.md) per i sistemi operativi supportati, i driver, l'installazione e i passaggi di verifica.

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