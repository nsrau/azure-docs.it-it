---
title: Dimensioni delle macchine virtuali Linux in Azure -GPU | Microsoft Docs
description: Elenca le diverse dimensioni ottimizzate per GPU per le macchine virtuali Linux disponibili in Azure.
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
ms.date: 07/28/2017
ms.author: jonbeck
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ec289cf53f2cfecd2744b739667ef831dafd59a4
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="gpu-linux-vm-sizes"></a>Dimensioni delle macchine virtuali Linux GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

Per le procedure di installazione e verifica dei driver, vedere [Installare i driver GPU NVIDIA in VM serie N che eseguono Linux](n-series-driver-setup.md).

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
