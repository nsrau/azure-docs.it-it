---
title: Serie NDv2-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie NDv2.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 6c002838d5a4f515a594d61e5137196c4d391795
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605445"
---
# <a name="updated-ndv2-series-preview"></a>Aggiornamento della serie NDv2 (anteprima)

La macchina virtuale della serie NDv2 è una nuova aggiunta alla famiglia di GPU progettata per le esigenze dei carichi di lavoro di intelligenza artificiale, apprendimento automatico, simulazione e HPC con accelerazione più impegnativa.

NDv2 è alimentato da 8 GPU NVIDIA Tesla V100 NVLINK-connected, ciascuna con 32 GB di memoria GPU. Ogni macchina virtuale NDv2 dispone anche di 40 core Intel Xeon Platinum 8168 (Skylake) senza Hyper-Threading e 672 GiB della memoria di sistema.

Le istanze di NDv2 offrono prestazioni eccellenti per i carichi di lavoro HPC e intelligenza artificiale che usano i kernel CUDA con ottimizzazione per la GPU e i molti strumenti di intelligenza artificiale, Machine Learning e analisi che supportano l'accelerazione della GPU predefinita, ad esempio TensorFlow, Pytorch, caffe, RAPIDS e altro quadri.

In modo critico, il NDv2 è progettato per la scalabilità verticale sia a livello di calcolo (sfruttando 8 GPU per macchina virtuale) sia per la scalabilità orizzontale (sfruttando più macchine virtuali che operano insieme) carichi di lavoro. La serie NDv2 supporta ora le reti back-end InfiniBand EDR 100-Gigabit, simili a quelle disponibili nella serie HB della VM HPC, per consentire il clustering a prestazioni elevate per gli scenari paralleli, incluso il training distribuito per AI e ML. Questa rete back-end supporta tutti i principali protocolli InfiniBand, inclusi quelli usati dalle librerie NCCL2 di NVIDIA, consentendo un clustering di GPU senza problemi.


> [!NOTE]
> Quando si [Abilita InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) nella macchina virtuale ND40rs_v2, usare il driver OFED Mellanox 4.7-1.0.0.1.
>
> A causa di una maggiore memoria GPU, la nuova macchina virtuale ND40rs_v2 richiede l'uso di [macchine virtuali di seconda generazione](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) e immagini del Marketplace. 
>
> [Iscriversi per richiedere l'accesso anticipato all'anteprima della macchina virtuale NDv2.](https://aka.ms/AzureNDrv2Preview)
>
> Nota: la ND40s_v2 con 16 GB di memoria per GPU non è più disponibile in anteprima ed è stata sostituita dalla ND40rs_v2 aggiornata.

<br>

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

InfiniBand: supportato

| Dimensione | vCPU | Memoria: GiB | Archiviazione temporanea (SSD): GiB | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Larghezza di banda di rete massima | Schede di interfaccia di rete max |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 16 | 32 | 80000/800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

Per sfruttare i vantaggi delle funzionalità GPU delle VM serie N di Azure, è necessario installare i driver GPU NVIDIA.

L'[estensione del driver NVIDIA GPU](/extensions/hpccompute-gpu-windows.md) consente di installare i driver NVIDIA CUDA o GRID appropriati in una macchina virtuale serie N. Installare o gestire l'estensione usando il portale di Azure o strumenti come i modelli di Azure PowerShell Azure o Azure Resource Manager. Vedere la [documentazione dell'estensione dei driver GPU NVIDIA](/extensions/hpccompute-gpu-windows.md) per informazioni sui sistemi operativi supportati e sui passaggi di distribuzione. Per altre informazioni sulle estensioni macchina virtuale, vedere [Azure virtual machine extensions and features](/extensions/overview.md) (Funzionalità ed estensioni macchina virtuale di Azure).

Se si sceglie di installare manualmente i driver GPU NVIDIA, vedere la pagina relativa alla [configurazione di driver GPU della serie n per](/windows/n-series-driver-setup.md) la configurazione di driver GPU per Windows o [serie n per Linux](/linux/n-series-driver-setup) per i sistemi operativi, i driver, l'installazione e i passaggi di verifica supportati.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
