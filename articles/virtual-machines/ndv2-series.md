---
title: Serie NDv2 - Macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie NDv2.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8a2d275501816dd504130b255b73a752c5615f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247283"
---
# <a name="updated-ndv2-series"></a>Serie NDv2 aggiornata

La macchina virtuale della serie NDv2 è una nuova aggiunta alla famiglia GPU progettata per le esigenze dei carichi di lavoro IA accelerati GPU, apprendimento automatico, simulazione e HPC più esigenti.

NDv2 è alimentato da 8 GPU collegate n. NVIDIA Tesla V100, ognuna con 32 GB di memoria GPU. Ogni VM NDv2 ha anche 40 core Intel Xeon Platinum 8168 (Skylake) non HyperThreaded e 672 GiB di memoria di sistema.

Le istanze NDv2 offrono prestazioni eccellenti per i carichi di lavoro HPC e AI utilizzando kernel di calcolo CUDA GPU e i numerosi strumenti di analisi e AI che supportano l'accelerazione GPU "out-of-box", come TensorFlow, Pytorch, Caffe, RAPIDS e altri Framework.

In modo critico, l'NDv2 è progettato per carichi di lavoro con scalabilità verticale computazionalmente intenso (harnessing 8 GPU per macchina virtuale) che per quelli di scalabilità orizzontale (harnessing multiple VMs working together). La serie NDv2 ora supporta una rete back-end infiniBand EDR da 100 Gigabit, simile a quella disponibile nella serie HB della VM HPC, per consentire clustering ad alte prestazioni per scenari paralleli, tra cui formazione distribuita per AI e ML. Questa rete back-end supporta tutti i principali protocolli InfiniBand, inclusi quelli utilizzati dalle librerie NCCL2 di NVIDIA, consentendo un clustering senza soluzione di continuità delle GPU.

> Quando si [abilita InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) nella macchina virtuale ND40rs_v2, usare il driver OFED 4.7-1.0.0.1 Mellanox.
>
> A causa dell'aumento della memoria GPU, la nuova macchina virtuale ND40rs_v2 richiede l'uso di macchine virtuali di [generazione 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) e immagini del marketplace. 
>
> Nota: il ND40s_v2 con 16 GB di memoria per GPU non è più disponibile per l'anteprima ed è stato sostituito dal ND40rs_v2 aggiornato.

<br>

Archiviazione Premium: supportata

Memorizzazione nella cache di archiviazione Premium: supportata

Live Migration: non supportato

Aggiornamenti di conservazione della memoria: non supportatiMemory Preserving Updates: Not Supported

InfiniBand: Supportato

| Dimensione | vCPU | Memoria: GiB | Temp Storage (SSD): GiB | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Larghezza di banda di rete massima | Schede di interfaccia di rete max |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000 / 800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

Per sfruttare le funzionalità GPU delle macchine virtuali di Azure Serie N, è necessario installare i driver GPU NVIDIA.

L'[estensione del driver NVIDIA GPU](./extensions/hpccompute-gpu-linux.md) consente di installare i driver NVIDIA CUDA o GRID appropriati in una macchina virtuale serie N. Installare o gestire l'estensione usando il portale di Azure o strumenti come i modelli di Azure PowerShell Azure o Azure Resource Manager. Per altre informazioni sulle estensioni macchina virtuale, vedere [Azure virtual machine extensions and features](./extensions/overview.md) (Funzionalità ed estensioni macchina virtuale di Azure).

Se si sceglie di installare manualmente i driver GPU NVIDIA, vedere [Configurazione del driver GPU di serie N per Linux](./linux/n-series-driver-setup.md).

## <a name="other-sizes"></a>Altre dimensioni

- [Finalità generale](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
