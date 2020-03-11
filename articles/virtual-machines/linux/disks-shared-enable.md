---
title: Abilitare i dischi condivisi per Azure Managed Disks
description: Configurare un disco gestito di Azure con dischi condivisi (anteprima) in modo che sia possibile condividerlo tra più macchine virtuali
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0e925abf151abc1a229b57bb035775430fa5332f
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970371"
---
# <a name="enable-shared-disk"></a>Abilita disco condiviso

Questo articolo illustra come abilitare la funzionalità dischi condivisi (anteprima) per Azure Managed Disks. Azure Shared Disks (anteprima) è una nuova funzionalità di Azure Managed disks che consente di aggiungere contemporaneamente un disco gestito a più macchine virtuali (VM). Il fissaggio di un disco gestito a più macchine virtuali consente di distribuire le applicazioni in cluster nuove o migrate in Azure. 

Per informazioni concettuali sui dischi gestiti in cui sono abilitati i dischi condivisi, vedere [Azure Shared disks](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]