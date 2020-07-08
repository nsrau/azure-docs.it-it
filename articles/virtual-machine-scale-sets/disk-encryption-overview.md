---
title: Abilitare crittografia dischi di Azure per i set di scalabilità di macchine virtuali
description: Questo articolo fornisce istruzioni sull'abilitazione della crittografia del disco Microsoft Azure per i set di scalabilità di macchine virtuali
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 6b9805d66149a18216a200bc89a79b3e06106c9d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83195118"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Crittografia dischi di Azure per i set di scalabilità di macchine virtuali

Crittografia dischi di Azure offre la crittografia dei volumi per il sistema operativo e i dischi dati delle macchine virtuali, contribuendo a proteggere e salvaguardare i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Per altre informazioni, vedere [crittografia dischi di Azure: VM Linux](../virtual-machines/linux/disk-encryption-overview.md) e [crittografia dischi di Azure: VM Windows](../virtual-machines/windows/disk-encryption-overview.md)  

Crittografia dischi di Azure può essere applicata anche a set di scalabilità di macchine virtuali Windows e Linux, in questi casi:
- Set di scalabilità creati con Managed Disks. Crittografia dischi di Azure non è supportata per i set di scalabilità di dischi nativi (o non gestiti).
- Volumi del sistema operativo e dei dati nei set di scalabilità Windows.
- Volumi di dati nei set di scalabilità Linux. La crittografia del disco del sistema operativo non è attualmente supportata per i set di scalabilità Linux.

È possibile apprendere le nozioni di base di crittografia dischi di Azure per i set di scalabilità di macchine virtuali in pochi minuti con i [set di scalabilità di una macchina virtuale usando l'interfaccia](disk-encryption-cli.md) della riga di comando di Azure o i [set di scalabilità di macchine virtuali usando le](disk-encryption-powershell.md) esercitazioni Azure PowerShell.

## <a name="next-steps"></a>Passaggi successivi

- [Crittografare i set di scalabilità di macchine virtuali usando il Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Creare e configurare un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md)
- [Usare Crittografia dischi di Azure con sequenziazione delle estensioni del set di scalabilità di macchine virtuali](disk-encryption-extension-sequencing.md)
