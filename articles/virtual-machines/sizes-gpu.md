---
title: Dimensioni delle macchine virtuali di Azure - GPU Documenti Microsoft
description: Elenca le diverse dimensioni ottimizzate GPU disponibili per le macchine virtuali in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
services: virtual-machines
documentationcenter: ''
author: vikancha
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 7e1e0d488844a94bd0be2b91398678e620295729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913583"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali ottimizzate per la GPU

Le dimensioni delle macchine virtuali ottimizzate per la GPU sono macchine virtuali specializzate disponibili con una o più GPU NVIDIA. Queste dimensioni sono progettate per carichi di lavoro di visualizzazione oppure a elevato utilizzo di calcolo o di grafica. Questo articolo fornisce informazioni relative a numero e tipo di GPU, vCPU, dischi dati e schede di rete. Anche velocità effettiva di archiviazione e larghezza di banda della rete sono incluse per ogni dimensione di questo raggruppamento.

- [Le](nc-series.md)dimensioni della [serie NCv3](ncv3-series.md) sono ottimizzate per applicazioni e algoritmi che richiedono un uso intensivo del calcolo e della rete. [NCv2-series](ncv2-series.md) Alcuni esempi sono le applicazioni e le simulazioni basate su CUDA e OpenCL, AI e Deep Learning. La serie NCv3 è progettata per i carichi di lavoro HPC (High-Performance Computing) e dotata di GPU NVIDIA Tesla V100. La serie NC utilizza il processore Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell) e le macchine virtuali della serie NCv2 e della serie NCv3 utilizzano il processore Intel Xeon E5-2690 v4 (Broadwell).

- [Le](nd-series.md)dimensioni delle serie ND e [NDv2](ndv2-series.md) sono incentrate sugli scenari di training e inferenza per il deep learning. Usano la GPU NVIDIA Tesla P40 e il processore Intel Xeon E5-2690 v4 (Broadwell). La serie NDv2 utilizza il processore Intel Xeon Platinum 8168 (Skylake).

- Le dimensioni della [serie NV](nv-series.md) e [della serie NVv3](nvv3-series.md) sono ottimizzate e progettate per la visualizzazione remota, lo streaming, il gioco, la codifica e gli scenari VDI utilizzando framework come OpenGL e DirectX. Queste macchine virtuali hanno GPU NVIDIA Tesla M60.

- [Serie NVv4](nvv4-series.md) Dimensioni delle macchine virtuali ottimizzate e progettate per la visualizzazione VDI e remota. Con GPU partizionate, NVv4 offre la dimensione giusta per i carichi di lavoro che richiedono risorse GPU più piccole. Queste macchine virtuali sono supportate dalla GPU AMD Radeon Instinct MI25. Le macchine virtuali NVv4 attualmente supportano solo il sistema operativo guest Windows.

## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

Per sfruttare le funzionalità GPU delle macchine virtuali di Azure Serie N, è necessario installare i driver GPU NVIDIA.

L'[estensione del driver NVIDIA GPU](/azure/virtual-machines/extensions/hpccompute-gpu-windows) consente di installare i driver NVIDIA CUDA o GRID appropriati in una macchina virtuale serie N. Installare o gestire l'estensione usando il portale di Azure o strumenti come i modelli di Azure PowerShell Azure o Azure Resource Manager. Vedere la [documentazione dell'estensione dei driver GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) per informazioni sui sistemi operativi supportati e sui passaggi di distribuzione. Per altre informazioni sulle estensioni macchina virtuale, vedere [Azure virtual machine extensions and features](/azure/virtual-machines/extensions/overview) (Funzionalità ed estensioni macchina virtuale di Azure).

Se si sceglie di installare manualmente i driver GPU NVIDIA, vedere [Configurazione dei driver GPU di serie N per Windows](/azure/virtual-machines/windows/n-series-driver-setup) o Configurazione del driver [GPU di serie N per Linux](/azure/virtual-machines/linux/n-series-driver-setup) per i passaggi operativi supportati, i driver, l'installazione e la verifica.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

- Per la disponibilità delle VM serie N, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

- Le VM serie N possono essere distribuite solo nel modello di distribuzione Resource Manager.

- Le macchine virtuali serie N differiscono nel tipo di Archiviazione di Azure supportato per i dischi. Le VM serie NC e NV supportano solo dischi della macchina virtuale con archiviazione su disco di Azure di tipo Standard (HDD). Le macchine virtuali NCv2, NCv3, ND, NDv2 e NVv2 supportano solo dischi di macchine virtuali con archiviazione su disco Premium (unità SSD).

- Per distribuire numerose VM serie N, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto. Con un [account gratuito di Azure](https://azure.microsoft.com/free/)è possibile usare solo un numero limitato di core di calcolo di Azure.

- Può essere necessario aumentare la quota di core (per area) nella sottoscrizione di Azure e la quota separata per i core NC, NCv2, NCv3, ND, NDv2, NV o NVv2. Per richiedere un aumento della quota, apri una richiesta di [assistenza clienti online](../azure-portal/supportability/how-to-create-azure-support-request.md) gratuitamente. I limiti predefiniti possono variare in base alla categoria della sottoscrizione.

## <a name="other-sizes"></a>Altre dimensioni

- [Finalità generale](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
