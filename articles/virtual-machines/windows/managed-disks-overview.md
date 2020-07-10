---
title: Panoramica dell'archiviazione su disco di Azure
description: Panoramica di Azure Managed Disks, che gestisce automaticamente gli account di archiviazione quando si usano macchine virtuali di Azure
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 13237011e160d0190475eadb3f9d20f0d731df60
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146626"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduzione ad Azure Managed Disks

Azure Managed Disks offre volumi di archiviazione a livello di blocco gestiti da Azure e usati con le macchine virtuali di Azure. Un disco gestito può essere considerato come un disco fisico in un server locale, ma virtualizzato. Con i dischi gestiti è sufficiente specificare le dimensioni e il tipo di disco ed effettuare il provisioning del disco. Una volta effettuato il provisioning, Azure gestisce il resto.

I tipi di dischi disponibili sono Ultra, SSD Premium, SSD Standard e HDD Standard. Per informazioni su ogni singolo tipo di disco, vedere [Selezionare un tipo di disco per macchine virtuali IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selezionare un tipo di disco per macchine virtuali IaaS](disks-types.md)
