---
title: Abilitare i dischi condivisi per Azure Managed Disks
description: Configurare un disco gestito di Azure con dischi condivisi in modo che sia possibile condividerlo tra più macchine virtuali
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/16/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e36c539cc1143490aeb4862c928589db5c502656
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080225"
---
# <a name="enable-shared-disk"></a>Abilitare il disco condiviso

Questo articolo illustra come abilitare la funzionalità dischi condivisi per Azure Managed Disks. Azure Shared disks è una nuova funzionalità di Azure Managed disks che consente di aggiungere contemporaneamente un disco gestito a più macchine virtuali (VM). Collegando un disco gestito a più macchine virtuali è possibile distribuire nuove applicazioni in cluster o eseguire la migrazione di quelle esistenti in Azure. 

Per informazioni concettuali sui dischi gestiti in cui sono abilitati i dischi condivisi, vedere [Azure Shared disks](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]