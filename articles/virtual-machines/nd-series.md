---
title: Serie ND-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie ND.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 1dfd4185f077806d6b84376e786cfe5de1a8ffc3
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653441"
---
# <a name="nd-series"></a>Serie ND

Le macchine virtuali della serie ND sono una novità della famiglia di GPU progettata per carichi di lavoro di intelligenza artificiale e Deep Learning. Offrono prestazioni ottimali per il training e l'inferenza. Le istanze ND sono basate su GPU [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) e CPU Intel Xeon E5-2690 V4 (Broadwell). Queste istanze offrono prestazioni eccellenti per le operazioni con precisione singola e virgola mobile e per carichi di lavoro di intelligenza artificiale che usano Microsoft Cognitive Toolkit, TensorFlow, Caffe e altri framework. La serie ND offre anche dimensioni di memoria di GPU significativamente superiori (24 GB), ottimali per modelli di rete neurale molto più grandi. Analogamente alla serie NC, la serie ND offre una configurazione con latenza bassa secondaria, rete con velocità effettiva elevata tramite RDMA e connettività InfiniBand, in modo che sia possibile eseguire processi di training su vasta scala per molte GPU.

[Archiviazione Premium](premium-storage-performance.md): supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): non supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): non supportato<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1 e 2<br>

> [!IMPORTANT]
> Per questa serie di macchine virtuali, la quota di vCPU (Core) per area nella sottoscrizione viene inizialmente impostata su 0. [Richiedere un aumento della quota di vCPU](../azure-portal/supportability/resource-manager-core-quotas-request.md) per questa serie in un' [area disponibile](https://azure.microsoft.com/regions/services/).
>
| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 24 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = Pari a una scheda P40.

*Con supporto di RDMA

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

Per sfruttare i vantaggi delle funzionalità GPU delle VM serie N di Azure, è necessario installare i driver GPU NVIDIA.

L'[estensione del driver NVIDIA GPU](./extensions/hpccompute-gpu-windows.md) consente di installare i driver NVIDIA CUDA o GRID appropriati in una macchina virtuale serie N. Installare o gestire l'estensione usando il portale di Azure o strumenti come i modelli di Azure PowerShell Azure o Azure Resource Manager. Vedere la [documentazione dell'estensione dei driver GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) per informazioni sui sistemi operativi supportati e sui passaggi di distribuzione. Per altre informazioni sulle estensioni macchina virtuale, vedere [Azure virtual machine extensions and features](./extensions/overview.md) (Funzionalità ed estensioni macchina virtuale di Azure).

Se si sceglie di installare manualmente i driver GPU NVIDIA, vedere la pagina relativa alla [configurazione di driver GPU della serie n per](./windows/n-series-driver-setup.md) la configurazione di driver GPU per Windows o [serie n per Linux](./linux/n-series-driver-setup.md) per i sistemi operativi, i driver, l'installazione e i passaggi di verifica supportati.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
