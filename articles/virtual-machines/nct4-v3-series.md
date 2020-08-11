---
title: Serie ANGC T4 V3
description: Specifiche per le macchine virtuali della serie ANGC T4 V3.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: ab0205f05fc1d58157f4363dcc73564f3d4786b2
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068310"
---
# <a name="ncast4_v3-series"></a>Serie di NCasT4_v3 

Le macchine virtuali serie NCasT4_v3 sono basate su GPU [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) e CPU AMD EPYC 7V12 (Roma). Le macchine virtuali sono dotate di un massimo di 4 GPU NVIDIA T4 con 16 GB di memoria ciascuno, fino a 64 core del processore AMD EPYC 7V12 (Roma) non multithread e 440 GiB della memoria di sistema. Queste macchine virtuali sono ideali per eseguire carichi di lavoro di machine learning e di intelligenza artificiale che usano CUDA, TensorFlow, Pytorch, caffe e altri Framework o i carichi di lavoro grafici con la tecnologia NVIDIA GRID.

<br>

ACU: 230-260

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

Live Migration: Non supportato

Manutenzione con mantenimento della memoria: Non supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NV8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NV16as_T4_v3 |16 |110 |360 | 16 | 8 | 32 | 8  |
| Standard_NV64as_T4_v3 |64 |440 |2880 | 64 | 16 | 32 | 8  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

Per sfruttare i vantaggi delle funzionalità GPU delle VM serie NCasT4_v3 di Azure che eseguono Windows o Linux, è necessario installare i driver GPU NVIDIA.

Per installare manualmente i driver GPU NVIDIA, vedere la pagina relativa alla [configurazione di driver GPU serie N per Windows](./windows/n-series-driver-setup.md) per i sistemi operativi supportati, i driver, l'installazione e i passaggi di verifica.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
