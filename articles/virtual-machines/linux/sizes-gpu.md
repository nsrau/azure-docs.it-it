---
title: 'Dimensioni delle macchine virtuali Linux di Azure: calcolo accelerato'
description: Elenca le diverse dimensioni ottimizzate per GPU per le macchine virtuali Linux disponibili in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 983e85dc0ebefb27804c0f3a794360def6050ba9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034916"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali ottimizzate per la GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Driver e distribuzioni supportati

Per usufruire delle funzionalità GPU delle macchine virtuali serie N di Azure che eseguono Linux, è necessario installare i driver GPU NVIDIA. L'[estensione del driver NVIDIA GPU](../extensions/hpccompute-gpu-linux.md) consente di installare i driver NVIDIA CUDA o GRID appropriati in una macchina virtuale serie N. Installare o gestire l'estensione usando il portale di Azure o strumenti come l'interfaccia della riga di comando di Azure o Azure Resource Manager. Vedere le [documentazione dell'estensione dei driver GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) per le distribuzioni supportate e i passaggi di distribuzione. Per altre informazioni sulle estensioni macchina virtuale, vedere [Azure virtual machine extensions and features](../extensions/overview.md) (Funzionalità ed estensioni macchina virtuale di Azure).

Se si sceglie di installare manualmente i driver NVIDIA GPU, vedere [N-series GPU driver setup for Linux](n-series-driver-setup.md) (Installazione dei driver GPU serie N) per informazioni su distribuzioni, driver e passaggi di installazione e verifica.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Non è consigliabile installare X Server o altri sistemi che usano il driver `Nouveau` in VM NC Ubuntu. Prima di installare driver NVIDIA GPU, è necessario disabilitare il driver `Nouveau`.  

## <a name="other-sizes"></a>Altre dimensioni
- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.