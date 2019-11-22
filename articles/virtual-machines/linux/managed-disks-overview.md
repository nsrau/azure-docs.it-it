---
title: Panoramica dei dischi gestiti di archiviazione su disco di Azure per le macchine virtuali Linux
description: Panoramica di Azure Managed Disks, che gestisce automaticamente gli account di archiviazione quando si usano macchine virtuali Linux
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 11/06/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7e9a5e1b1e1b0c879a1264573e7073e70a1e95fc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035860"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduzione ad Azure Managed Disks

Un disco gestito di Azure è un disco rigido virtuale. È possibile considerarlo come un disco fisico in un server locale, ma virtualizzato. I dischi gestiti di Azure vengono archiviati come BLOB di pagine, ovvero un oggetto di archiviazione di I/O casuale in Azure. Un disco gestito viene definito "gestito" perché è un'astrazione di BLOB di pagine, contenitori BLOB e account di archiviazione di Azure. Con i dischi gestiti, è sufficiente effettuare il provisioning del disco e Azure si occupa di tutto il resto.

Quando si sceglie di usare i dischi gestiti di Azure con i carichi di lavoro, Azure crea e gestisce automaticamente il disco. I tipi di dischi disponibili sono dischi Ultra, SSD Premium, SSD Standard e HDD Standard. Per altre informazioni su ogni singolo tipo di disco, vedere [Selezionare un tipo di disco per macchine virtuali IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selezionare un tipo di disco per macchine virtuali IaaS](disks-types.md)
