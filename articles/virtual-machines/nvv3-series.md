---
title: Serie NVv3 - Macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie NVv3.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: d74b00a4bade956d3a511a47b0a6b0011b9fb212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267420"
---
# <a name="nvv3-series"></a>Serie NVv3

Le macchine virtuali della serie NVv3 sono alimentate da GPU [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e tecnologia NVIDIA GRID con CPU Intel E5-2690 v4 (Broadwell) e tecnologia Intel Hyper-Threading. Queste macchine virtuali sono specifiche per applicazioni con grafica accelerata per GPU e desktop virtuali in cui i clienti vogliono visualizzare i propri dati, simulare risultati da visualizzare, lavorare in CAD o eseguire il rendering e lo streaming di contenuti. Queste macchine virtuali possono anche eseguire carichi di lavoro con precisione singola, ad esempio per la codifica e il rendering. Le macchine virtuali NVv3 supportano l'archiviazione Premium e sono dotate di un doppio della memoria di sistema (RAM) rispetto alla serie NV precedente.  

Ogni GPU nelle istanze NVv3 viene fornito con una licenza GRID. Questa licenza consente di usare un'istanza NV come workstation virtuale per un singolo utente oppure consente a 25 utenti simultanei di connettersi a una macchina virtuale per uno scenario di applicazione virtuale.

Memorizzazione nella cache di archiviazione Premium: supportata

Live Migration: non supportato

Aggiornamenti di conservazione della memoria: non supportatiMemory Preserving Updates: Not Supported

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Velocità effettiva massima disco non memorizzato nella cache: IOPS/MBpsMax uncached disk throughput: IOPS/MBps | Schede di interfaccia di rete max | Workstation virtuali | Applicazioni virtuali |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_NV12s_v3 |12 | 112 | 320  | 1 | 8  | 12 | 20000/200 | 4 | 1 | 25  |
| Standard_NV24s_v3 |24 | 224 | 640  | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50  |
| Standard_NV48s_v3 |48 | 448 | 1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = Pari a metà scheda M60.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

Per sfruttare le funzionalità GPU delle macchine virtuali di Azure Serie N, è necessario installare i driver GPU NVIDIA.

L'[estensione del driver NVIDIA GPU](./extensions/hpccompute-gpu-windows.md) consente di installare i driver NVIDIA CUDA o GRID appropriati in una macchina virtuale serie N. Installare o gestire l'estensione usando il portale di Azure o strumenti come i modelli di Azure PowerShell Azure o Azure Resource Manager. Vedere la [documentazione dell'estensione dei driver GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) per informazioni sui sistemi operativi supportati e sui passaggi di distribuzione. Per altre informazioni sulle estensioni macchina virtuale, vedere [Azure virtual machine extensions and features](./extensions/overview.md) (Funzionalità ed estensioni macchina virtuale di Azure).

Se si sceglie di installare manualmente i driver GPU NVIDIA, vedere [Configurazione dei driver GPU di serie N per Windows](./windows/n-series-driver-setup.md) o Configurazione del driver [GPU di serie N per Linux](./linux/n-series-driver-setup.md) per i passaggi operativi supportati, i driver, l'installazione e la verifica.

## <a name="other-sizes"></a>Altre dimensioni

- [Finalità generale](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
