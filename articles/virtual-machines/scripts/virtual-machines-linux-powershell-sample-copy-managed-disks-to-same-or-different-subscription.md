---
title: Copiare dischi gestiti in una sottoscrizione (Linux) - Esempio di PowerShell
description: Esempio di script di PowerShell - Copiare (o spostare) i dischi gestiti nella stessa sottoscrizione o in un'altra
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: ca594801c0886903f395dddb7ccbfccb42bff15f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069385"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell-linux"></a>Copiare dischi gestiti nella stessa sottoscrizione o in una sottoscrizione diversa con PowerShell (Linux)

Questo script crea una copia di un disco gestito esistente nella stessa sottoscrizione o in una sottoscrizione diversa. Il nuovo disco viene creato nella stessa area del disco gestito padre.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un nuovo disco gestito nella sottoscrizione di destinazione usando l'ID del disco gestito di origine. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Crea la configurazione del disco usata per la creazione del disco. Include l'ID risorsa del disco padre e il percorso che è identico a quello del disco padre.  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Crea un disco accettando come parametri la configurazione del disco, il nome del disco e il nome del gruppo di risorse. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di script PowerShell per la macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
