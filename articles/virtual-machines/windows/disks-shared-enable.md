---
title: Abilitare i dischi condivisi per Azure Managed Disks
description: Configurare un disco gestito di Azure con dischi condivisi in modo che sia possibile condividerlo tra più macchine virtuali
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/30/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cc365f6eef4da61ab7de5113b8f4b2b185360740
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460057"
---
# <a name="enable-shared-disk"></a>Abilitare il disco condiviso

Questo articolo illustra come abilitare la funzionalità dischi condivisi per Azure Managed Disks. Azure Shared disks è una nuova funzionalità di Azure Managed disks che consente di aggiungere contemporaneamente un disco gestito a più macchine virtuali (VM). Collegando un disco gestito a più macchine virtuali è possibile distribuire nuove applicazioni in cluster o eseguire la migrazione di quelle esistenti in Azure. 

Per informazioni concettuali sui dischi gestiti in cui sono abilitati i dischi condivisi, vedere [Azure Shared disks](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]