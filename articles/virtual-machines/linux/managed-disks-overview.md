---
title: Panoramica dell'archiviazione su disco di Azure per le macchine virtuali Linux
description: Panoramica di Azure Managed Disks, che gestisce automaticamente gli account di archiviazione quando si usano macchine virtuali Linux
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2b25da174399b092fe821a46b235d7a2bd14572b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355899"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduzione ad Azure Managed Disks

Azure Managed Disks offre volumi di archiviazione a livello di blocco gestiti da Azure e usati con le macchine virtuali di Azure. Un disco gestito può essere considerato come un disco fisico in un server locale, ma virtualizzato. Con i dischi gestiti è sufficiente specificare le dimensioni e il tipo di disco ed effettuare il provisioning del disco. Una volta effettuato il provisioning, Azure gestisce il resto.

I tipi di dischi disponibili sono Ultra, SSD Premium, SSD Standard e HDD Standard. Per informazioni su ogni singolo tipo di disco, vedere [Selezionare un tipo di disco per macchine virtuali IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selezionare un tipo di disco per macchine virtuali IaaS](disks-types.md)