---
title: Dimensioni delle macchine virtuali Linux in Azure -GPU | Microsoft Docs
description: Elenca le diverse dimensioni ottimizzate per GPU per le macchine virtuali Linux disponibili in Azure.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: e0cd2a14b4102797024925a72783ecaf56919aec
ms.contentlocale: it-it
ms.lasthandoff: 05/02/2017


---

# <a name="gpu-linux-vm-sizes"></a>Dimensioni delle macchine virtuali Linux GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Per informazioni sui sistemi operativi supportati e sui requisiti dei driver, vedere [Configurare i driver GPU per le VM serie N che eseguono Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Non è consigliabile installare X Server o altri sistemi che usano il driver nouveau in VM NC Ubuntu. Prima di installare i driver NVIDIA GPU, è necessario disabilitare il driver nouveau.  

## <a name="other-sizes"></a>Altre dimensioni
- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [High Performance Computing (HPC)](sizes-hpc.md)

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
