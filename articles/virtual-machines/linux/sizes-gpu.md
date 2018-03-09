---
title: Dimensioni delle macchine virtuali Linux in Azure -GPU | Microsoft Docs
description: "Elenca le diverse dimensioni ottimizzate per GPU per le macchine virtuali Linux disponibili in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jonbeck
ms.openlocfilehash: a1057cd901725f2c8ad209db507a5bb9fdf33e77
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali ottimizzate per la GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

Per le procedure di installazione e verifica dei driver, vedere [Installare i driver GPU NVIDIA in VM serie N che eseguono Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Non è consigliabile installare X Server o altri sistemi che usano il driver `Nouveau` in VM NC Ubuntu. Prima di installare driver NVIDIA GPU, è necessario disabilitare il driver `Nouveau`.  

## <a name="other-sizes"></a>Altre dimensioni
- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [High Performance Computing (HPC)](sizes-hpc.md)

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.