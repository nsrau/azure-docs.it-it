---
title: Panoramica dell'archiviazione su disco di Azure per le macchine virtuali Windows
description: Panoramica di Azure Managed Disks, che gestisce automaticamente gli account di archiviazione quando si usano macchine virtuali Windows di Azure
author: roygara
ms.service: virtual-machines-windows
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7b2c2c1289a40d63b2f396ee59000c3aedb14c3d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75460035"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduzione ad Azure Managed Disks

Azure Managed Disks offre volumi di archiviazione a livello di blocco gestiti da Azure e usati con le macchine virtuali di Azure. Un disco gestito può essere considerato come un disco fisico in un server locale, ma virtualizzato. Con i dischi gestiti è sufficiente specificare le dimensioni e il tipo di disco ed effettuare il provisioning del disco. Una volta effettuato il provisioning, Azure gestisce il resto.

I tipi di dischi disponibili sono Ultra, SSD Premium, SSD Standard e HDD Standard. Per informazioni su ogni singolo tipo di disco, vedere [Selezionare un tipo di disco per macchine virtuali IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selezionare un tipo di disco per macchine virtuali IaaS](disks-types.md)
