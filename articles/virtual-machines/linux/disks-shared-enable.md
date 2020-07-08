---
title: Abilitare i dischi condivisi per Azure Managed Disks
description: Configurare un disco gestito di Azure con dischi condivisi (anteprima) in modo che sia possibile condividerlo tra più macchine virtuali
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 3b949905f311b1a8878aa691e32abc3f568e1e6b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84657283"
---
# <a name="enable-shared-disk"></a>Abilitare il disco condiviso

Questo articolo illustra come abilitare la funzionalità dischi condivisi (anteprima) per Azure Managed Disks. Azure Shared Disks (anteprima) è una nuova funzionalità di Azure Managed disks che consente di aggiungere contemporaneamente un disco gestito a più macchine virtuali (VM). Collegando un disco gestito a più macchine virtuali è possibile distribuire nuove applicazioni in cluster o eseguire la migrazione di quelle esistenti in Azure. 

Per informazioni concettuali sui dischi gestiti in cui sono abilitati i dischi condivisi, vedere [Azure Shared disks](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]