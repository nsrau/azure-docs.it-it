---
title: Abilitare Crittografia disco di Azure per set di scalabilità di macchine virtualiEnable Azure Disk Encryption for Virtual Machine Scale Sets
description: Questo articolo fornisce istruzioni sull'abilitazione di Crittografia del disco di Microsoft Azure per set di scalabilità di macchine virtuali
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278980"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Disk Encryption for Virtual Machine Scale Sets

Crittografia disco di Azure offre la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali, che consente di proteggere e proteggere i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Per altre informazioni, vedere Crittografia disco di Azure: Macchine virtuali Linux e Crittografia disco di Azure: Macchine virtuali WindowsTo learn more, see [Azure Disk Encryption: Linux VMs](../virtual-machines/linux/disk-encryption-overview.md) and [Azure Disk Encryption: Windows VMs](../virtual-machines/windows/disk-encryption-overview.md)  

Crittografia disco di Azure può essere applicata anche ai set di scalabilità delle macchine virtuali Windows e Linux, in questi casi:Azure Disk Encryption can also be applied to Windows and Linux virtual machine scale sets, in these instances:
- Set di scalabilità creati con dischi gestiti. La crittografia del disco di Azure non è supportata per i set di scalabilità del disco nativi (o non gestiti).
- OS e volumi di dati nei set di scalabilità di Windows.OS and data volumes in Windows scale sets.
- Volumi di dati nei set di scalabilità Linux.Data volumes in Linux scale sets. La crittografia del disco del sistema operativo NON è attualmente supportata per i set di scalabilità Linux.OS disk encryption is NOT supported at or supported at or

È possibile apprendere le nozioni di base di Crittografia disco di Azure per i set di scalabilità delle macchine virtuali in pochi minuti con i set di scalabilità Di una macchina virtuale che [usano l'interfaccia della](disk-encryption-cli.md) riga di comando di Azure o Crittografare una macchina virtuale con le esercitazioni di Azure PowerShell.You can learn the fundamentals of Azure Disk Encryption for virtual machine scale sets in just few minutes with the Encrypt a virtual machine scale sets using the Azure CLI o the Encrypt a virtual machine scale sets using the Azure [PowerShell](disk-encryption-powershell.md) tutorials.

## <a name="next-steps"></a>Passaggi successivi

- [Crittografare un set di scalabilità di macchine virtuali usando Azure Resource ManagerEncrypt a virtual machine scale sets using the Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Creare e configurare un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md)
- [Usare Crittografia dischi di Azure con sequenziazione delle estensioni del set di scalabilità di macchine virtuali](disk-encryption-extension-sequencing.md)
