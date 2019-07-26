---
title: Panoramica dei dischi gestiti di archiviazione su disco di Azure per le macchine virtuali Windows| Microsoft Docs
description: Panoramica di Azure Managed Disks, che gestisce automaticamente gli account di archiviazione quando si usano macchine virtuali Windows di Azure
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.workload: storage
ms.tgt_pltfrm: vm-windows
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: fb1ee8556935b141dfee6a18c96ecafb476aa584
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "64725824"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduzione ad Azure Managed Disks

Un disco gestito di Azure è un disco rigido virtuale. È possibile considerarlo come un disco fisico in un server locale, ma virtualizzato. I dischi gestiti di Azure vengono archiviati come BLOB di pagine, ovvero un oggetto di archiviazione di I/O casuale in Azure. Un disco gestito viene definito "gestito" perché è un'astrazione di BLOB di pagine, contenitori BLOB e account di archiviazione di Azure. Con i dischi gestiti, è sufficiente effettuare il provisioning del disco e Azure si occupa di tutto il resto.

Quando si sceglie di usare i dischi gestiti di Azure con i carichi di lavoro, Azure crea e gestisce automaticamente il disco. I tipi di dischi disponibili sono Ultra SSD (anteprima), SSD Premium, SSD Standard e HDD Standard. Per altre informazioni su ogni singolo tipo di disco, vedere [Selezionare un tipo di disco per macchine virtuali IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selezionare un tipo di disco per macchine virtuali IaaS](disks-types.md)